# Plugin Configuration

## Runtime Configuration Mutation

Plugins can modify the live CLI config object before command registration finishes:

```javascript
export default async function plugin(context) {
  const { config } = context;

  if (!config.commands) {
    config.commands = [];
  }

  config.commands.push({
    name: 'mycompany check',
    method: 'GET',
    endpoint: '/V1/my-plugin-check',
    description: 'Sample injected virtual command from plugin',
  });
}
```

Because command registration reads `config.commands`, injected entries become available during the same execution.

## Persist Settings

Use `saveConfig()` to write defaults or preferences back to `config.json`:

```javascript
export default async function plugin(context) {
  const { config, saveConfig } = context;

  if (!config.myPluginSettings) {
    config.myPluginSettings = { enabled: true };
    await saveConfig(config);
  }
}
```

If a plugin needs the shared config helpers directly instead of only the injected `context.saveConfig`, it can also use `context.lib.config.loadConfig()` and `context.lib.config.saveConfig()`.

## Static Configuration Sources

Instead of mutating config in JavaScript, a plugin can provide static JSON. The loader checks in this order:

1. `mage-remote-run.json` in the plugin root
2. the `mage-remote-run` property in `package.json`

Example `mage-remote-run.json`:

```json
{
  "$schema": "https://mage-remote-run.muench.dev/config.schema.json",
  "commands": [
    {
      "name": "mycompany check",
      "method": "GET",
      "endpoint": "/V1/my-plugin-check",
      "description": "Sample injected virtual command from plugin"
    }
  ]
}
```

## Typical Uses

- add default plugin settings to `config.json`
- inject virtual commands for custom APIs
- package reusable command definitions for a team
- combine static config with runtime logic in one plugin

When a plugin also adds executable commands around those config entries, pair this with `context.lib.utils` so command behavior matches built-in filter, pagination, sort, and output handling.
