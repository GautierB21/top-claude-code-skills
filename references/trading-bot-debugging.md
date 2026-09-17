# Trading Bot Debugging — Known Issues & Fixes

## Snapshot Corruption (Daily Bots)

**Symptom**: A bot shows P&L of $357 trillion or $437 million instead of normal ~$10k.

**Root cause**: Crypto bought at $0.00 price (yfinance didn't have data for that token).  
`quantity × $0.00 = $0` → ok. But when CoinGecko later returns a real price (`$0.62`),  
`360,001 tokens × $0.62 = $223k` — portfolio explodes.

**Fix**:
1. Delete all `portfolio_snapshots`: `DELETE FROM portfolio_snapshots`
2. Find and delete problematic holdings with `avg_price < 0.001`
3. Fix strategies to reject buy signals when `price <= 0`
4. Run bots again to create clean snapshots

**Prevention**: Add `if price <= 0: continue` in strategy signal generation.  
For CoinGecko: filter out coins with `current_price < 0.01`.

## Duplicate Trades (Intraday Bots)

**Symptom**: Every trade appears twice in the intraday trade log.  
Cash/P&L values inflate because each trade's cash impact is applied twice.

**Root cause**: The scheduler's `process_signals` loop is called twice per cycle, or  
a redundant execution path in the Kraken WS callback.

**Fix**: Add a dedup check in `insert_trade()`:
```python
dup = conn.execute(
    "SELECT id FROM intraday_trades WHERE bot_name=? AND symbol=? AND side=? "
    "AND quantity=? AND price=? AND executed_at > datetime('now','-60 seconds')",
    (bot_name, symbol, side, round(quantity, 6), round(price, 2))
).fetchone()
if dup:
    return  # skip duplicate
```

**Reset after fix**:
1. Stop container
2. Delete `data/intraday.db` entirely
3. Restart (DB is auto-created fresh)

## Frontend Shows Wrong Values

**Symptom**: Dashboard shows `$0` or wrong amounts for bots that have positions.

**Root cause**: Frontend reads `bot.cash` as fallback when no snapshot exists.  
But `bot.cash` is just the cash balance — doesn't include holdings value.

**Fix**:
1. API must return `total_value = cash + holdings_value` per bot  
2. Frontend fallback: `Number(bot.total_value || bot.capital || bot.cash)`

## API 500 Errors After Deployment

Common causes:
- Missing function import (`_collect_all_symbols` → `_collect_symbols`)
- Undefined function (`build_universe_info` → `compute_universe`)
- Service worker cache (bump `CACHE_NAME` in `sw.js`)

Check with: `docker logs trading-bots --tail 10`
