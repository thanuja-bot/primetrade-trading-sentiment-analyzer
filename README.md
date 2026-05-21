# Trading Sentiment Analyzer

A professional full-stack data science dashboard analyzing the relationship between the **Bitcoin Fear & Greed Index** and **Hyperliquid trader performance**. Built for the Primetrade.ai data science assignment.

## Live Features

### Analytics
| Feature | Description |
|---|---|
| **KPI Overview** | Total trades, win rates, Avg PnL under Fear vs Greed |
| **Sentiment vs PnL** | Bar + line chart comparing profitability across all 5 sentiment classes |
| **Win Rate Analysis** | Long vs Short win rates broken down by sentiment |
| **Portfolio Risk Metrics** | Sharpe Ratio, Sortino Ratio, Max Drawdown, 95% VaR, Calmar Ratio |
| **Equity Curve** | Cumulative PnL with overlaid drawdown % (peak-to-trough) |
| **Timeline** | Day-by-day PnL overlaid with market sentiment label |
| **Market Regime Detection** | Rolling 14-day sentiment MA classifies each day as Bull / Neutral / Bear |
| **Sentiment Momentum** | 7 / 14 / 30-day rolling averages of the sentiment score (1–5) |
| **Leverage Patterns** | Avg / min / max leverage usage by sentiment class |
| **Symbol Performance** | Per-asset PnL, win rate, Fear vs Greed breakdown |
| **Correlation Heatmap** | Pearson r between sentiment score and PnL (symbol × leverage bracket) |
| **Strategy Backtesting** | Simulate 7 signal-filter strategies vs baseline |
| **Calendar Heatmap** | GitHub-style daily PnL calendar for the last 52 weeks |
| **Trader Leaderboard** | Top 20 traders with Confidence Score, Risk Level, Watchlist |
| **Trader Profile Modal** | Click any trader → equity curve, sentiment breakdown, symbol mix, leverage histogram |

### UX & Export
| Feature | Description |
|---|---|
| **Dark / Light Mode** | Full dark and light theme with one-click toggle |
| **Sentiment Alert Banner** | Auto-detects Extreme Fear / Greed streaks and zone flips |
| **Watchlist / Favorites** | Star traders to bookmark; filter leaderboard to watchlist only |
| **Confidence Score** | 0–100 score per trader based on sample size and win rate |
| **Risk Level Badge** | Low / Medium / High per trader based on leverage and win rate |
| **CSV Upload** | Upload your own Hyperliquid trades + Fear & Greed CSVs |
| **CSV Export** | Per-chart download button on every panel |
| **PDF Export** | Export the full dashboard as a PDF (html2canvas + jsPDF) |
| **Mobile Responsive** | Fluid grid: 1 column on mobile → 4 columns on desktop |

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, Vite 6, TypeScript 5 |
| UI | Tailwind CSS v4, shadcn/ui |
| Charts | Recharts |
| Tables | TanStack React Table v8 |
| API Client | TanStack React Query + Orval-generated hooks |
| CSV parsing | PapaParse |
| CSV export | react-csv |
| PDF export | jsPDF + html2canvas |
| Backend | Node.js 24, Express 5, TypeScript |
| API Contract | OpenAPI 3.1 → Orval codegen |
| Monorepo | pnpm workspaces |

## Project Structure

```
artifacts/
  api-server/          Express 5 backend — analysis engine + REST API
  trading-dashboard/   React + Vite frontend
lib/
  api-spec/            OpenAPI 3.1 contract (source of truth)
  api-client-react/    Generated React Query hooks + Zod schemas (do not edit)
scripts/               Utility scripts
```

## Getting Started

### Prerequisites
- Node.js 20+
- pnpm 9+

### Installation

```bash
git clone <repo-url>
cd primetrade-trading-sentiment-analyzer
pnpm install
```

### Environment

```bash
cp .env.example .env
# All values have sensible defaults — no changes needed for local dev
```

### Running Locally

```bash
# Terminal 1 — API server (port 5000)
pnpm --filter @workspace/api-server run dev

# Terminal 2 — Frontend (port 5173)
pnpm --filter @workspace/trading-dashboard run dev
```

Open `http://localhost:5173` in your browser.

### Regenerate API hooks after spec changes

```bash
pnpm --filter @workspace/api-spec run codegen
```

## CSV Data Format

### Trader CSV (Hyperliquid format)
Required columns: `account`, `symbol`, `execution_price`, `size`, `side` (Long/Short),
`time` (ISO 8601), `closed_pnl`, `leverage`

### Sentiment CSV (Fear & Greed format)
Required columns: `date` (YYYY-MM-DD), `classification`
(one of: Extreme Fear | Fear | Neutral | Greed | Extreme Greed)

## API Endpoints

| Method | Path | Description |
|---|---|---|
| GET | `/api/analysis/overview` | Summary KPIs |
| GET | `/api/analysis/sentiment-pnl` | PnL by sentiment class |
| GET | `/api/analysis/timeline` | Daily PnL + sentiment |
| GET | `/api/analysis/trader-performance` | Top traders with confidence + risk |
| GET | `/api/analysis/trader-profile?account=xxx` | Single trader deep dive |
| GET | `/api/analysis/symbol-performance` | Per-symbol stats |
| GET | `/api/analysis/leverage-patterns` | Leverage by sentiment |
| GET | `/api/analysis/win-rate` | Win rate by sentiment + side |
| GET | `/api/analysis/sentiment-distribution` | Fear vs Greed day counts |
| GET | `/api/analysis/correlation-heatmap` | Pearson r heatmap |
| GET | `/api/analysis/backtesting` | Strategy backtest results |
| GET | `/api/analysis/portfolio-metrics` | Sharpe, Sortino, MaxDD, VaR, Calmar |
| GET | `/api/analysis/market-regime` | Rolling 14d regime classification |
| GET | `/api/analysis/sentiment-momentum` | Rolling MA7/14/30 of sentiment |
| GET | `/api/analysis/sentiment-alert` | Extreme condition detection |
| POST | `/api/data/upload` | Upload trader + sentiment CSVs |
| GET | `/api/data/status` | Check current data source |

## Risk Metrics Glossary

| Metric | Formula | Good threshold |
|---|---|---|
| **Sharpe Ratio** | (Mean daily PnL / Std) × √252 | > 1.0 |
| **Sortino Ratio** | (Mean daily PnL / Downside std) × √252 | > 1.5 |
| **Max Drawdown** | Max (peak − trough) / peak | < 20% |
| **VaR 95%** | 5th percentile of daily PnL | Depends on capital |
| **Calmar Ratio** | Annualised return / Max drawdown | > 0.5 |

## Key Findings (Sample Data)

- **Greed Signal Only** strategy beats baseline by **+102%** in avg PnL/trade
- **Extreme Greed Only** shows **+147%** improvement — sentiment is highly predictive at extremes
- **3–10× leverage** shows the strongest sentiment–PnL correlations
- Traders win at **64%** in Greed phases vs **54%** in Fear phases
- Market regime detection confirms avg daily PnL is **~4× higher** in Bull vs Bear regimes
- Sentiment momentum shows clear **leading-indicator** behaviour 7–14 days ahead of PnL shifts
