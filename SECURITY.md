# Security Policy

## Supported versions

Security fixes are applied to the current `main` branch. Build the application from source and keep your checkout up to date. If a release is published, use the latest available version.

## How Solanna handles data

Solanna is a local first desktop application.

* **API keys stay local.** Keys for OpenAI, Anthropic, DeepSeek, GLM and Ollama are stored in the app data directory through `tauri-plugin-store`. They are not sent to a Solanna controlled server.
* **Data flow.** During analysis, news text and analysis context are sent to the AI provider you configure. The app does not send this data to any other third party.
* **Key handling.** API keys pass from the Tauri core to the sidecar as process arguments and are used only for the provider request. Keys are redacted from logs.

Keep your API keys and local app data directory private. Anyone with access to that directory may be able to read the stored keys and research data.

## Reporting a vulnerability

Do not open a public issue for a security vulnerability.

Report it through [GitHub private vulnerability reporting](https://github.com/truongdatdev/solanna/security/advisories/new) or email **truongdatdev@users.noreply.github.com** with `[SECURITY] Solanna` in the subject.

Include the following in the report.

* A description of the issue and its impact
* Steps to reproduce it
* The affected version or commit
* An optional suggested fix

We aim to acknowledge reports within 72 hours. Credit will be given in the release notes after a fix, unless you prefer to remain anonymous.
