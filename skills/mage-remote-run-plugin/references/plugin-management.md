# Plugin Management

## Create a Plugin Package

Start with a Node.js package:

```bash
mkdir my-plugin
cd my-plugin
npm init -y
```

Set `"type": "module"` in `package.json` and declare `mage-remote-run` as a peer dependency, then create `index.js`:

```json
{
  "name": "mage-remote-run-plugin-my-plugin",
  "version": "1.0.0",
  "type": "module",
  "peerDependencies": {
    "mage-remote-run": "^1.5.0"
  }
}
```

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
