# Advanced Features

## MCP server mode

Expose `mage-remote-run` commands as MCP tools for AI agents.

```bash
# Start with stdio transport
mage-remote-run mcp --transport stdio

# Start with HTTP/SSE transport
mage-remote-run mcp --transport http --host 127.0.0.1 --port 18098

# Provide an auth token for HTTP mode
mage-remote-run mcp --transport http --token secure-token-123
# Or use: MAGE_REMOTE_RUN_MCP_TOKEN

# Include or exclude commands by group and pattern
mage-remote-run mcp --include "@safe @connection order:*" --exclude "order:cancel"
```

**Command groups:**
- `@safe` — read-only commands (default)
- `@risky` / `@all` — full command set
- Domain groups: `@connection`, `@website`, `@store`, `@customer`, `@order`, `@product`, `@cart`, `@tax`, `@inventory`, `@shipment`, `@event`, `@webhook`, `@company`, `@po-cart`, `@import`, `@module`, `@plugin`, `@rest`, `@console`, `@eav`
- Combined groups: `@sales`, `@catalog`, `@cloud`, `@commerce`

**Tool naming:** `website_list`, `store_view_list`, `order_show`, etc.

**HTTP endpoints:** `GET /sse` and `POST /messages`
**Auth:** `?token=<token>` for SSE or `Authorization: Bearer <token>` for POST

## Interactive console (REPL)

```bash
mage-remote-run console
# or
mage-remote-run repl

# Enable debug mode
mage-remote-run console --debug
```

Inside the console:
- Run CLI commands: `website list`, `order latest`, `customer search user@example.com`
- Execute JavaScript with top-level await
- `list` — inspect available commands
- `help` — command help
- `.exit` — quit

**Available globals:** `client` (async API client factory), `config`, `chalk`, `reload()`

## Plugin development

Plugins register commands or subscribe to runtime events.

```javascript
// index.js
import chalk from 'chalk';

/**
 * @param {Object} context
 * @param {import('commander').Command} context.program
 * @param {Object} context.config
 * @param {Object|null} context.profile
 * @param {import('events').EventEmitter} context.eventBus
 * @param {Object} context.events
 * @param {Function} context.createClient
 */
export default async function(context) {
    const { program, eventBus, events, createClient } = context;

    // Register a command
    program.command('hello')
        .description('Say hello from a plugin')
        .option('-n, --name <name>', 'Name to greet', 'World')
        .action((options) => {
            console.log(chalk.green(`Hello ${options.name}!`));
        });

    // Register a command that calls an API endpoint
    program.command('my-custom-endpoint')
        .description('Call a custom endpoint')
        .action(async () => {
            try {
                const client = await createClient();
                const data = await client.get('V1/custom-endpoint');
                console.log(data);
            } catch (error) {
                console.error(error.message);
            }
        });

    // Subscribe to events
    eventBus.on(events.BEFORE_COMMAND, ({ thisCommand, actionCommand, profile }) => {
        // Logic before command execution
    });

    eventBus.on(events.AFTER_COMMAND, (data) => {
        // Logic after command execution
    });

    eventBus.on(events.MCP_START, ({ server, options }) => {
        // Logic when MCP server starts
    });
}
```

```bash
# Register a plugin
mage-remote-run plugin register mage-remote-run-plugin-audit
mage-remote-run plugin register /path/to/my-plugin

# Unregister a plugin
mage-remote-run plugin unregister mage-remote-run-plugin-audit

# List plugins
mage-remote-run plugin list
```
