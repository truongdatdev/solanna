# Contributing to Solanna

Contributions are welcome. Bug reports, feature proposals, documentation fixes and code changes all help the project.

## Before you start

* Search the [issue tracker](https://github.com/truongdatdev/solanna/issues) before opening a new issue.
* Open an issue before a large change so the approach can be discussed.
* Documentation fixes can be submitted directly as a pull request.

## Requirements

* [Bun](https://bun.sh/) for dependency management, tests and the compiled sidecar
* [Rust](https://www.rust-lang.org/tools/install) stable and the [Tauri 2 prerequisites](https://v2.tauri.app/start/prerequisites/) for your platform

## Local development

```bash
bun install
bun tauri dev
```

The development command builds the sidecar, starts Vite and opens the Tauri window.

## Architecture

Solanna uses a strictly unidirectional three layer architecture.

```text
React UI  ->  Tauri Core (Rust)  ->  Sidecar (Bun)
```

* **Frontend.** React and TypeScript in `src/`. All IPC goes through `src/lib/ipc.ts`.
* **Tauri core.** Rust code in `src-tauri/`. Handles configuration and sidecar process lifetime.
* **Sidecar.** Bun code in `sidecar/`. Collects news, computes quantitative metrics, runs backtests and calls AI providers.
* **Shared contracts.** `shared/` is the single source of truth for DTOs, action names, provider profiles and market rules. Do not duplicate these definitions in another layer.

More detail is available in [`.claude/rules/architecture.md`](./.claude/rules/architecture.md).

## Conventions

* **Comments.** Inline logic comments use Simplified Chinese.
* **Component size.** Keep UI component files under 200 lines and move complex logic into hooks.
* **Sidecar output.** Write only the final JSON to stdout. Send debug logs to stderr.
* **Test separation.** Keep parsers in `sidecar/parsers/` and make the default suite offline. Use `*.integration.ts` for live network checks.
* **Translations.** Add new UI strings to `src/i18n/zh.json` first and keep the Chinese, English and Japanese files aligned.

## Extension points

* **AI provider.** Update `shared/constants.ts` and `sidecar/providers/registry.ts`.
* **Investor agent.** Add an implementation under `sidecar/agents/masters/` and register it.
* **News strategy.** Implement `ScrapeStrategy` from `sidecar/strategies/base.ts` and register it in `sidecar/strategies/registry.ts`.

## Before opening a pull request

```bash
bun run test
bun run format:check
bunx tsc --noEmit
cd src-tauri && cargo check
```

* All tests pass.
* Formatting, TypeScript and Rust checks pass.
* New logic has unit tests.
* Commit messages are clear. [Conventional Commits](https://www.conventionalcommits.org/) is recommended.
* Cross layer changes update the shared source.

## Workflow

1. Fork the repository and create a branch with `git checkout -b feat/your-feature`.
2. Make the change and run the checks above.
3. Push the branch to your fork and open a pull request against `main`.
4. Respond to review feedback and update the branch as needed.
