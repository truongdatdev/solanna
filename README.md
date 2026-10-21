<div align="center">
  <img src="./images/solanna-banner.png" alt="Solanna" width="900" />
</div>

# Solanna

Solanna is a local first AI equity research desk for US stocks and China A shares. It combines live news, quotes, fundamentals and quantitative signals in one workspace, with money flow and sector data for China A shares, then layers AI analysis on top.

The application runs on your machine. There is no hosted backend, and provider keys stay in local storage.

[Issue tracker](https://github.com/truongdatdev/solanna/issues) | [License](LICENSE)

## Overview

Most research tools separate market data, news, screening and AI analysis into different products. Solanna keeps them in one desktop workspace, so a stock can move from discovery to a written research note without leaving the app.

A typical research session moves through four stages.

1. Search for a ticker and load its quote, candlestick history, news and company profile.
2. Review the quantitative score, valuation range, money flow and risk metrics.
3. Run AI analysis, ask follow up questions or start a deep review with one or more investor agents.
4. Save the result locally, revisit it later and compare new calls with the track record.

The desktop shell uses Tauri 2 and React 19. Data collection, quantitative calculations, backtests and provider calls run in a compiled Bun sidecar. Analysis history, portfolio positions and investor signals are stored in local SQLite.

## Features

* **Market workspace.** Search US and China A share tickers by code or name and load quotes, news, fundamentals and quantitative data in one view.
* **Candlestick charts.** Use MA and BOLL overlays, nine time ranges, MACD, RSI, KDJ, OBV and VWAP subcharts, logarithmic scale, adjusted prices and benchmark comparison.
* **Market overview.** Follow a major index strip, China A share sector and concept leaderboards, fund flow rankings and the Dragon Tiger list.
* **Company profile.** Read the business overview, revenue breakdown, shareholder structure and sector tags for A shares, loaded on demand.
* **Quantitative score.** Combine technical, fundamental and valuation signals with a volatility adjustment.
* **Natural language screener.** Describe criteria in plain language and scan the China A share market. The app shows how the request was interpreted before presenting the matches.
* **Strategy backtesting.** Review total return, annualized return, trade count, win rate, maximum drawdown, Sharpe ratio, a buy and hold comparison, entries, exits and an equity curve.
* **News sentiment.** Scan recent news for a ticker and return a bullish, bearish or neutral breakdown with factor cards linked to sources.
* **Investor agent analysis.** Run up to 13 investor agents, each with its own framework, and combine their views into a consensus rating.
* **Investor track record.** Record directional calls locally, mark them to market and show hit rates, average returns and an equal weight NAV curve.
* **Grounded chat.** Ask follow up questions against earnings reports, recent news, quantitative metrics and earlier analysis, with citations back to the source.
* **Provider flexibility.** Use OpenAI, Anthropic, DeepSeek, GLM or Ollama. Assign a different provider and model to core analysis, news tagging and chat.
* **Portfolio tracking.** Record positions, live valuation, unrealized and daily profit and loss, position weights and separate currency totals.
* **Watchlist and alerts.** Follow symbols in one list and receive system notifications when a price crosses a configured threshold.
* **Local history.** Archive AI, deep, quant, backtest and screener results in SQLite and reopen them at any time.
* **Interface languages.** Use the app in Chinese, English or Japanese.

## Quick start

The repository does not currently publish prebuilt installers. Build the desktop application from source.

### Requirements

* [Bun](https://bun.sh/), used for dependency management, tests and the compiled sidecar
* [Rust](https://www.rust-lang.org/tools/install) stable and the [Tauri 2 prerequisites](https://v2.tauri.app/start/prerequisites/) for your platform
* An API key for a cloud provider, or a local Ollama installation

### Run from source

```bash
git clone https://github.com/truongdatdev/solanna.git
cd solanna
bun install
bun tauri dev
```

The development command builds the sidecar, starts Vite and opens the Tauri window.

### Build a desktop bundle

Build the sidecar for the target that matches your machine, then run the Tauri bundle command.

```bash
# macOS Apple Silicon
BUN_TARGET=bun-darwin-arm64 OUTFILE=src-tauri/bin/solanna-backend-aarch64-apple-darwin bun sidecar/build-script.ts

# Linux x64
BUN_TARGET=bun-linux-x64 OUTFILE=src-tauri/bin/solanna-backend-x86_64-unknown-linux-gnu bun sidecar/build-script.ts

# Windows x64
BUN_TARGET=bun-windows-x64 OUTFILE=src-tauri/bin/solanna-backend-x86_64-pc-windows-msvc.exe bun sidecar/build-script.ts

bun tauri build
```

The installer is written under `src-tauri/target/release/bundle`.

The examples use a POSIX shell. In PowerShell, set `$env:BUN_TARGET` and `$env:OUTFILE` before calling the build script.

Supported targets are `bun-darwin-arm64`, `bun-darwin-x64`, `bun-linux-x64` and `bun-windows-x64`. For an Intel Mac, use `bun-darwin-x64` with the `x86_64-apple-darwin` triple.

## Markets and data

Solanna covers US stocks and China A shares. Market data comes from Tencent, Sina, Eastmoney and Yahoo, with fallback when a source fails.

News collection uses Google News RSS, Eastmoney News, Google News and Yahoo. Some sources are opened through a local browser when a page requires JavaScript. All requests go directly from your machine to the public data source.

Market data may be delayed, incomplete or unavailable when a third party source changes or blocks access.

## AI providers

* **OpenAI.** Cloud models through the OpenAI API.
* **Anthropic.** Claude models through the Anthropic API.
* **DeepSeek.** Cloud models through an OpenAI compatible endpoint.
* **GLM.** Zhipu models through an OpenAI compatible endpoint.
* **Ollama.** Local models served from `http://127.0.0.1:11434` by default.

Models can be assigned per role. Use a stronger model for core analysis and a smaller or cheaper model for news tagging and chat. Keys, endpoints and model choices are stored locally.

Solanna does not proxy requests or host models. Analysis requests go from your machine to the provider you configure.

## Architecture

Solanna uses a strictly unidirectional three layer architecture.

```text
React UI  ->  Tauri Core (Rust)  ->  Sidecar (Bun)
```

* **Frontend.** React 19, TypeScript and Vite. All IPC goes through `src/lib/ipc.ts`. Local history uses SQLite through `tauri-plugin-sql`.
* **Tauri core.** Exposes a generic `invoke_sidecar` command, manages process lifetime and keeps business logic out of Rust.
* **Sidecar.** A compiled Bun binary that collects news, computes quantitative metrics, runs backtests and calls AI providers.
* **Shared contracts.** The `shared` directory is the single source of truth for DTOs, action names, provider profiles and market detection.

Settings and provider keys live in the app data directory. Analysis history, portfolio positions and investor signals live in SQLite.

## Configuration

Open Settings and choose a provider. Each provider has fields for an API key, an endpoint and a model. Keys are stored in the local app data directory through `tauri-plugin-store`.

General settings control the interface language, automatic analysis on stock switch and deep mode. Deep mode fetches full article text during analysis. It is slower but produces more complete context. Automatic analysis is off by default to avoid unintended token use.

Model settings can assign a provider and model to three roles. Core analysis handles basic AI analysis, investor agents and final synthesis. Quick tagging classifies news sentiment. Chat answers follow up questions against the loaded context.

Solanna does not require an account or a Solanna hosted service.

## Project layout

```text
src/         React frontend, hooks, charts and views
src-tauri/   Tauri core, capabilities and SQLite migrations
sidecar/     Bun sidecar, data sources, agents and AI providers
shared/      Cross layer types, actions, market rules and constants
scripts/     Build, verification and smoke test tooling
docs/        Design notes, release notes and screenshots
```

## Testing

```bash
bun run test
bun run test:integration
bun run test:e2e
bun run format
bunx tsc --noEmit
cd src-tauri && cargo test
```

The default suite is offline and covers the frontend, the sidecar and the shared contracts. Integration tests call live data sources and run separately. The end to end smoke test starts a browser and verifies that candlesticks render.

Run `bun run format` before submitting a change. The repository includes a pre push hook for formatting, TypeScript and Rust checks.

## Security

Solanna stores provider keys in the local app data directory. It does not run a hosted backend and does not proxy model calls. News text and analysis context are sent directly to the provider you configure.

Diagnostic logs are written locally. Review them before sharing them in an issue. API keys and authentication parameters are redacted from logs, but local files can still contain sensitive research data.

Report vulnerabilities privately through [GitHub Security Advisories](https://github.com/truongdatdev/solanna/security/advisories/new). See [SECURITY.md](./SECURITY.md) for the full policy.

## Contributing

Contributions are welcome. Read [CONTRIBUTING.md](./CONTRIBUTING.md) and [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md) before opening a pull request. For a large change, open an issue first so the approach can be discussed.

## Support

Questions and bug reports can be filed in the [GitHub issue tracker](https://github.com/truongdatdev/solanna/issues). Security issues should follow the private process in [SECURITY.md](./SECURITY.md).

## License

MIT. See [LICENSE](LICENSE).

## Disclaimer

Solanna is a research and educational tool. Its output, including AI ratings, investor consensus, quantitative scores and backtest results, is not investment advice. Market data comes from public third party sources and may be delayed, incomplete or incorrect. You are responsible for your own investment decisions.
