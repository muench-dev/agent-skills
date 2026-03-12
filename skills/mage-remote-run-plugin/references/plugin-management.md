# Plugin Management

## Commands

Register an installed npm package or local path:

```bash
mage-remote-run plugin register <package-name-or-path>
```

Examples:

```bash
mage-remote-run plugin register mage-remote-run-plugin-audit
mage-remote-run plugin register /path/to/my-plugin
```

Unregister a plugin:

```bash
mage-remote-run plugin unregister <package-name-or-path>
```

List registered plugins:

```bash
mage-remote-run plugin list
```

## What Plugins Can Do

- Register custom CLI commands.
- Listen to lifecycle events such as `beforeCommand` or `mcpStart`.
- Use the active connection through `createClient()`.
- Reuse built-in CLI helpers through `context.lib.utils`, `context.lib.commandHelper`, and `context.lib.config`.
- Inject configuration, including virtual commands.
