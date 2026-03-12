---
name: mage-remote-run-plugin
description: Build, explain, and maintain `mage-remote-run` plugins and configuration-based extensions. Use when an agent needs to create or review plugin packages, register custom commands, hook into plugin lifecycle events, inject or persist CLI configuration, ship static `mage-remote-run.json` settings, or define virtual commands for Magento or Adobe Commerce APIs.
---

# Mage Remote Run Plugin

Use this skill to work on the `mage-remote-run` extensibility model. It covers JavaScript plugins, configuration injection, static plugin configuration, lifecycle events, MCP behavior, and virtual commands.

## Workflow

1. Identify whether the task needs executable JavaScript, static configuration, or both.
2. Read the matching reference file before editing:
   - `references/plugin-management.md` for registration and distribution behavior.
   - `references/plugin-runtime.md` for plugin module shape, runtime context, events, connection guards, and MCP notes.
   - `references/plugin-configuration.md` for `config` mutation, `saveConfig()`, and static config files.
   - `references/virtual-commands.md` for config-driven REST command definitions.
3. Prefer the lowest-complexity extension that solves the request:
   - Use a virtual command when the task is a declarative REST request.
   - Use a plugin when the task needs custom logic, event listeners, or dynamic behavior.
   - Combine both when a plugin needs runtime logic plus reusable command definitions.
4. Match functionality to supported connection types. Guard commands when they do not work across all profile types.
5. When the task involves MCP, remember that plugin commands become MCP tools automatically and execution runs in isolated contexts.

## Core Rules

- Export a default async ESM function from the plugin entrypoint.
- Treat `context.config` as the live CLI configuration object for the current run.
- Use `saveConfig()` only when settings should persist to `config.json`.
- Use `createClient()` for Magento or Adobe Commerce API calls instead of reimplementing auth.
- Keep plugin commands compatible with normal CLI help, options, and output formats.
- Prefer static `mage-remote-run.json` or package metadata for lightweight command bundles with no runtime logic.

## Extension Decision Guide

### Use a JavaScript plugin when you need

- custom Commander commands
- event listeners for `INIT`, `BEFORE_COMMAND`, `AFTER_COMMAND`, or `MCP_START`
- runtime checks against the active profile
- dynamic API calls through `createClient()`
- config mutation based on runtime state

### Use static plugin configuration when you need

- reusable virtual commands
- team-shared command bundles
- plugin defaults with no custom runtime behavior

### Use virtual commands when you need

- REST endpoints exposed as native-feeling CLI commands
- placeholder-based path mapping
- predefined filters and option metadata
- connection-type scoping without writing JavaScript

## Implementation Notes

- `name` values with spaces create nested commands, so `product get` becomes `mage-remote-run product get`.
- Endpoint placeholders like `:sku` are filled from matching CLI options.
- Extra parameters map to the query string for `GET` and `DELETE`, and to the JSON body for `POST`, `PUT`, and `PATCH`.
- Predefined filters support `${optionName}` and `{:optionName}` placeholder substitution only.
- Supported connection types are `magento-os`, `mage-os`, `ac-on-prem`, `ac-cloud-paas`, and `ac-saas`.

## Deliverables

When implementing or reviewing a plugin, provide:

- the plugin entrypoint or config artifact
- any required registration command such as `mage-remote-run plugin register <package-name-or-path>`
- connection-type assumptions and event usage
- example invocation commands for the new behavior
