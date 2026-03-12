# Plugin Runtime

## Module Shape

A plugin is an ESM module that exports a default async function. The CLI calls this entrypoint during startup.

```javascript
import chalk from 'chalk';

export default async function plugin(context) {
  const { program, eventBus, events } = context;

  program
    .command('hello')
    .description('Say hello from a plugin')
    .option('-n, --name <name>', 'Name to greet', 'World')
    .action((options) => {
      console.log(chalk.green(`Hello ${options.name}!`));
    });

  eventBus.on(events.BEFORE_COMMAND, ({ thisCommand, actionCommand, profile }) => {
    void thisCommand;
    void actionCommand;
    void profile;
  });
}
```

## Runtime Context

The documented context fields are:

- `program`: Commander root program used to register commands.
- `config`: active CLI configuration object.
- `saveConfig`: persist configuration to disk.
- `profile`: active connection profile or `null`.
- `eventBus`: event emitter for plugin lifecycle hooks.
- `events`: event name constants.
- `createClient`: factory for the active Magento or Adobe Commerce API client.
- `lib`: built-in helper modules exposed by `mage-remote-run`.

## Built-in Library Utilities

`context.lib` gives plugins access to the same internal helpers used by built-in CLI commands, without importing internal package paths directly.

### `lib.utils`

- `printTable(headers, rows)`: render a colored CLI table.
- `handleError(error)`: format and print Magento API errors.
- `buildSearchCriteria(options)`: convert `--filter` and pagination options into Magento search params.
- `buildSortCriteria(options)`: convert `--sort` options into Magento sort params.
- `addFilterOption(command)`: add the standard `--filter` option.
- `addSortOption(command)`: add the standard sort-related options.
- `addPaginationOptions(command)`: add `--page` and `--size` options.
- `addFormatOption(command)`: add the standard `--format` option.
- `formatOutput(options, data)`: print JSON or XML output and return whether output was handled.
- `applyLocalSearchCriteria(data, options)`: filter, sort, and paginate a local array.

Example:

```javascript
export default async function plugin(context) {
  const { program, createClient, lib } = context;
  const {
    printTable,
    handleError,
    addFilterOption,
    addPaginationOptions,
    buildSearchCriteria,
  } = lib.utils;

  const cmd = program.command('my-items').description('List items from a custom endpoint');

  addFilterOption(cmd);
  addPaginationOptions(cmd);

  cmd.action(async (options) => {
    try {
      const client = await createClient();
      const { params } = buildSearchCriteria(options);
      const result = await client.get('V1/my-items', { params });
      const items = result.items ?? [];

      if (items.length === 0) {
        console.log('No items found.');
        return;
      }

      printTable(['ID', 'Name', 'Status'], items.map((item) => [item.id, item.name, item.status]));
    } catch (error) {
      handleError(error);
    }
  });
}
```

### `lib.commandHelper`

- `expandCommandAbbreviations(rootCommand, argv)`: expand abbreviated or colon-separated command tokens.
- `resolveCommandMatch(parent, token)`: resolve an exact or abbreviated command match and report ambiguity.

Use these helpers when plugin behavior needs to inspect or manipulate Commander command trees in the same way as the core CLI.

### `lib.config`

- `loadConfig()`: load the persisted CLI config from disk.
- `saveConfig(config)`: save a config object to disk.

Example:

```javascript
export default async function plugin(context) {
  const { program, lib } = context;
  const { loadConfig, saveConfig } = lib.config;

  program
    .command('my-plugin-config')
    .description('Show or initialize plugin config')
    .action(async () => {
      const config = await loadConfig();

      if (!config.myPluginSettings) {
        config.myPluginSettings = { enabled: true };
        await saveConfig(config);
      }

      console.log(JSON.stringify(config.myPluginSettings, null, 2));
    });
}
```

## API Usage

Use `createClient()` for authenticated API calls:

```javascript
export default async function plugin(context) {
  const { program, createClient } = context;

  program
    .command('my-custom-endpoint')
    .description('Call a custom endpoint')
    .action(async () => {
      const client = await createClient();
      const data = await client.get('V1/custom-endpoint');
      console.log(data);
    });
}
```

## Connection-Type Guards

Restrict registration when a plugin only supports specific profile types:

```javascript
const SUPPORTED_TYPES = new Set([
  'ac-cloud-paas',
  'magento-os',
  'mage-os',
  'ac-on-prem',
]);

export default async function plugin(context) {
  const { profile } = context;

  if (!profile || !SUPPORTED_TYPES.has(profile.type)) {
    return;
  }

  // Register commands here
}
```

Available connection types:

- `magento-os`
- `mage-os`
- `ac-on-prem`
- `ac-cloud-paas`
- `ac-saas`

## Events

### `events.INIT` (`init`)

Triggered after plugins are loaded and before commands are fully processed.

Payload: app context with `program`, `config`, `profile`, `eventBus`, and `events`.

### `events.BEFORE_COMMAND` (`beforeCommand`)

Triggered right before a command action runs.

Payload: `thisCommand`, `actionCommand`, and `profile`.

### `events.AFTER_COMMAND` (`afterCommand`)

Triggered right after a command action finishes.

Payload: same shape as `BEFORE_COMMAND`.

### `events.MCP_START` (`mcpStart`)

Triggered when the MCP server initializes before it starts listening or connecting.

Payload: `server` and `options`.

## MCP Behavior

When `mage-remote-run` runs as an MCP server:

- plugin commands are automatically exposed as MCP tools
- tool discovery and execution use isolated contexts
- plugins are loaded inside those isolated contexts
- events emitted during tool execution are scoped to that execution
- global events such as `MCP_START` are emitted on the main process event bus
