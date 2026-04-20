# TradingView MCP Setup (FX Workflow)

You are going to install the TradingView MCP server and configure it for an FX trading workflow. Follow these steps in order. At each step, if something fails, report the error clearly and stop — do not continue past a failure.

## Step 1 — Install the MCP server

Clone `https://github.com/tradesdontlie/tradingview-mcp.git` to `~/tradingview-mcp`. Run `npm install` inside it. If the directory already exists, pull the latest changes instead of cloning.

## Step 2 — Add MCP to Claude Code config

Edit `~/.claude/.mcp.json` to add the tradingview server. If the file already exists and has other MCP servers, merge the tradingview entry into the existing `mcpServers` object — do not overwrite other servers.

```json
{ "mcpServers": { "tradingview": { "command": "node", "args": ["<HOME>/tradingview-mcp/src/server.js"] } } }
```

Replace `<HOME>` with your absolute home directory path.

## Step 3 — Create trading rules file

Create at `~/tradingview-mcp/rules.json` with this configuration:

```json
{
  "watchlist": {
    "majors": ["OANDA:EURUSD", "OANDA:GBPUSD", "OANDA:USDJPY", "OANDA:USDCHF", "OANDA:AUDUSD", "OANDA:USDCAD", "OANDA:NZDUSD"],
    "crosses": ["OANDA:EURJPY", "OANDA:GBPJPY", "OANDA:EURGBP", "OANDA:AUDJPY"],
    "macro": ["TVC:DXY", "TVC:US10Y", "TVC:DE10Y", "TVC:GOLD"]
  },
  "timeframes_to_check": ["1D", "4H", "1H", "15M"],
  "bias_criteria": {
    "bullish": "Price above 50 EMA on daily, RSI on daily between 45 and 70, higher highs and higher lows on 4H, aligned with DXY direction",
    "bearish": "Price below 50 EMA on daily, RSI on daily below 45, lower highs and lower lows on 4H, aligned with DXY direction",
    "neutral": "Price chopping around 50 EMA, RSI between 40 and 60, no clear structure, inside daily range"
  },
  "risk_rules": {
    "max_risk_per_trade": "1% of portfolio",
    "min_rr_ratio": 2,
    "no_trades_during": ["NFP", "FOMC", "ECB", "BoE", "BoJ", "SNB rate decision", "Asian session for non-JPY pairs", "rollover spread widening"]
  },
  "indicators_i_care_about": ["RSI (14)", "MACD (12, 26, 9)", "50 EMA", "200 EMA", "ATR (14)", "DXY correlation"],
  "sessions_to_trade": ["London", "New York", "London/NY overlap"]
}
```

## Step 4 — Launch TradingView

Use the `tv_launch` tool. If unavailable, auto-detect and launch manually with `--remote-debugging-port=9222`:

- Mac: `/Applications/TradingView.app/Contents/MacOS/TradingView`
- Windows: `%LOCALAPPDATA%\TradingView\TradingView.exe`
- Linux: `/opt/TradingView/tradingview`

## Step 5 — Verify connection

Run `tv_health_check`. If it returns `cdp_connected: true`, the setup is working.

## Step 6 — Final validation

Fetch EURUSD price and DXY level, then report full setup status:

```
MCP installed and connected: yes/no
Rules file created at: [path]
TradingView connected on port 9222: yes/no
Current EURUSD price: [number]
Current DXY level: [number]
Ready to use: yes/no
```

If all six items pass, the setup is complete.

---

*Adapted for FX from Miles Deutscher's original crypto workflow.*
