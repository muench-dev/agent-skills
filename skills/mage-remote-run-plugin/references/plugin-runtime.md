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
