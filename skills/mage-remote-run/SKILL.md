---
name: mage-remote-run
description: Guidance for using the Mage Remote Run CLI to manage Adobe Commerce, Magento Open Source, and Mage-OS instances remotely. Use when a user needs to: inspect or manage a remote Adobe Commerce, Magento Open Source, or Mage-OS instance; get help with mage-remote-run commands, flags, filters, or workflow design; configure connections, authentication, MCP server mode, plugins, or the interactive console; suggest automation-friendly command examples for CI/CD, scripts, or agent integrations.
---

# Mage Remote Run

Mage Remote Run is a CLI toolkit for operating Adobe Commerce, Magento Open Source, and Mage-OS environments remotely. It supports SaaS, Cloud/PaaS, and on-premise deployments with commands covering stores, customers, orders, products, inventory, shipments, webhooks, imports, and more. Output can be rendered as text, JSON, or XML.

## Core guidance

1. Prefer concrete `mage-remote-run` command examples over abstract descriptions.
2. Match the command set to the deployment type when relevant (SaaS-only, Adobe Commerce-only, on-prem/PaaS-only).
3. Mention `--format json` when machine-readable output may be useful.
4. For write or delete actions, call out confirmation, force flags, or environment-specific limitations.
5. If the user is working with AI tooling, consider MCP mode and its filtering model.

## Reference files

Load these as needed:

- **[references/connection-env.md](references/connection-env.md)** — Connection profiles, authentication, configuration, and environment variables
- **[references/commerce-commands.md](references/commerce-commands.md)** — Website, store, customer, order, product, cart, shipment, inventory, company (B2B), EAV, tax, and module commands
- **[references/integration.md](references/integration.md)** — Webhooks (SaaS), Adobe I/O Events, imports (JSON/CSV), and manual REST API calls
- **[references/filtering.md](references/filtering.md)** — Filtering syntax, operators, and examples for list commands
- **[references/advanced.md](references/advanced.md)** — MCP server mode, interactive console (REPL), and plugin development
