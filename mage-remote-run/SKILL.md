---
name: mage-remote-run
description: Guidance for using the Mage Remote Run CLI to manage Adobe Commerce, Magento Open Source, and Mage-OS instances remotely.
---

# Mage Remote Run

Mage Remote Run is a command-line toolkit for operating Adobe Commerce, Magento Open Source, and Mage-OS environments from a single remote interface. It acts like a commerce operations swiss army knife, exposing commands for stores, customers, orders, products, inventory, shipments, webhooks, imports, and more, while keeping the workflow approachable through connection profiles, interactive prompts, and rich terminal output.

It supports Adobe Commerce SaaS, Cloud/PaaS, on-premise deployments, Magento Open Source, and Mage-OS. Beyond standard CLI usage, it can run as an MCP (Model Context Protocol) server for agent integrations, provides an interactive console/REPL, supports a plugin system, and works with both Bearer token and OAuth 1.0a authentication. Output can be rendered as text, JSON, or XML for both manual work and automation pipelines.

## When to use

Use this skill when you need to:

- inspect or manage a remote Adobe Commerce, Magento Open Source, or Mage-OS instance
- help a user with `mage-remote-run` commands, flags, filters, or workflow design
- explain which commands exist for commerce domains like orders, customers, products, shipments, inventory, companies, or webhooks
- configure connections, authentication, MCP server mode, plugins, or the interactive console
- suggest automation-friendly command examples for CI/CD, scripts, or agent integrations

## Core guidance

1. Prefer concrete `mage-remote-run` command examples over abstract descriptions.
2. Match the command set to the deployment type when relevant, especially for SaaS-only, Adobe Commerce-only, or on-prem/PaaS-only capabilities.
3. Mention output format options like `--format json` when the user may want machine-readable output.
4. For write or delete actions, call out confirmation, force flags, or environment-specific limitations where useful.
5. If the user is working with AI tooling, consider MCP mode and its filtering model.

## Connection management

Manage multiple Magento or Adobe Commerce targets through named profiles. Profiles hold the system type, base URL, and authentication details. The active profile is used by API commands.

```bash
# Add a connection interactively
mage-remote-run connection add

# Add Adobe Commerce SaaS profile non-interactively
mage-remote-run connection add \
  --name "Production" \
  --type ac-saas \
  --url "https://example.com" \
  --client-id "my-client-id" \
  --client-secret "my-client-secret" \
  --active

# Add a profile that uses a Bearer token
mage-remote-run connection add \
  --name "Staging" \
  --type magento-os \
  --url "https://staging.example.com" \
  --token "integration_access_token"

# Add a profile that uses OAuth 1.0a
mage-remote-run connection add \
  --name "MyOAuth" \
  --type ac-on-prem \
  --url "https://example.com" \
  --consumer-key "ck_..." \
  --consumer-secret "cs_..." \
  --access-token "at_..." \
  --token-secret "ts_..."

# Review configured profiles
mage-remote-run connection list

# Pick the active profile interactively
mage-remote-run connection select

# Show active profile details
mage-remote-run connection status

# Verify connectivity for every profile
mage-remote-run connection test --all

# Remove cached access tokens
mage-remote-run connection clear-token-cache
```

## Website management

Use website commands to inspect and maintain Magento website definitions.

```bash
# List websites
mage-remote-run website list

# Search by code or name
mage-remote-run website search base

# Edit a website interactively
mage-remote-run website edit 1

# Delete a website with confirmation
mage-remote-run website delete 2
```

## Store management

Manage store groups, store views, and store configuration such as currencies and locales.

```bash
# List store groups
mage-remote-run store group list
mage-remote-run store group list --format json

# Search store groups
mage-remote-run store group search "us"

# List store views
mage-remote-run store view list
mage-remote-run store view list --format json

# Search store views
mage-remote-run store view search "default"

# Show store configuration values
mage-remote-run store config list

# Edit or delete groups and views
mage-remote-run store group edit 1
mage-remote-run store view delete 2
```

## Customer management

Work with customers, customer groups, and account lifecycle actions.

```bash
# List customers with paging
mage-remote-run customer list --page 1 --size 20

# Search by email or partial email
mage-remote-run customer search "example.com"

# Show one customer
mage-remote-run customer show 42
mage-remote-run customer show 42 --format json

# Create a customer interactively
mage-remote-run customer create

# Edit a customer interactively
mage-remote-run customer edit 42

# Delete a customer
mage-remote-run customer delete 42
mage-remote-run customer delete 42 --force

# Resend a confirmation email
mage-remote-run customer confirm 42
mage-remote-run customer confirm --redirect-url https://store.example.com/confirm

# List customer groups
mage-remote-run customer group list --page 1 --size 20
```

## Order management

Use order commands for inspection, filtering, and operational actions like cancel, hold, and unhold.

```bash
# List orders with pagination
mage-remote-run order list --page 1 --size 20

# Filter by status, email, or date window
mage-remote-run order list --status pending
mage-remote-run order list --email user@example.com
mage-remote-run order list --date-from 2023-01-01 --date-to 2023-12-31

# Apply generic filters and add extra fields
mage-remote-run order list --filter "grand_total>=100" --add-fields "base_grand_total,billing_address.city"

# Select columns and sort order
mage-remote-run order list --fields "increment_id,grand_total,customer_email"
mage-remote-run order list --sort "grand_total:DESC" --sort "created_at:ASC"

# Search by increment ID
mage-remote-run order search 1000

# Show order details by entity ID or increment ID
mage-remote-run order show 42
mage-remote-run order show 100000123 --format json

# Review the newest orders, optionally with interactive selection
mage-remote-run order latest --page 1 --size 20
mage-remote-run order latest --select

# Operational actions
mage-remote-run order cancel 42
mage-remote-run order hold 42
mage-remote-run order unhold 42

# Add order history comments through edit flow
mage-remote-run order edit 42
```

## Product management

Inspect product records, product types, attributes, and link types.

```bash
# List products with pagination and sorting
mage-remote-run product list --page 1 --size 20
mage-remote-run product list --sort-by price --sort-order DESC

# Show product details by SKU
mage-remote-run product show <sku>
mage-remote-run product show "24-MB01" --format json

# List product types
mage-remote-run product type list

# List attributes with paging
mage-remote-run product attribute list --page 1 --size 20

# Show one attribute
mage-remote-run product attribute show color
mage-remote-run product attribute show color --format json

# List available attribute input types
mage-remote-run product attribute type list

# List product link types
mage-remote-run product link-type list
mage-remote-run product link-type list --format json
```

## Cart management

Inspect carts and quote state with filtering and detailed quote views.

```bash
# List carts
mage-remote-run cart list

# Filter active carts
mage-remote-run cart list --filter "is_active:1"

# Sort carts by creation date
mage-remote-run cart list --sort "created_at:DESC"

# Paginate results
mage-remote-run cart list --page 2 --size 50

# Filter format: field:value:condition
# Conditions include eq, lt, gt, like, and others
mage-remote-run cart list --filter "customer_email:user@example.com:like"

# Show cart details
mage-remote-run cart show 42
mage-remote-run cart show 42 --format json
```

## Shipment management

Handle shipment lookup, creation, tracking, labels, notifications, and comments.

```bash
# List shipments
mage-remote-run shipment list --page 1 --size 20

# Filter by order ID
mage-remote-run shipment list --order-id 42

# Show shipment details
mage-remote-run shipment show 123
mage-remote-run shipment show 123 --format json

# Create a shipment
mage-remote-run shipment create 123 --notify

# Create a shipment with tracking data
mage-remote-run shipment create 123 --tracks '[{"carrier_code":"fedex","title":"FedEx","track_number":"123456"}]'

# Create a partial shipment
mage-remote-run shipment create 123 --items '[{"order_item_id":1,"qty":2}]' --notify

# Add tracking to an existing shipment
mage-remote-run shipment track 123 --carrier fedex --title FedEx --number 987654321

# Fetch shipping label
mage-remote-run shipment label 123

# Send shipment email
mage-remote-run shipment email 123

# Add a shipment comment
mage-remote-run shipment comments 123 --comment "Shipped via FedEx" --notify --visible
```

## Inventory management

Inspect stocks, sources, and stock resolution for sales channels.

```bash
# List stocks
mage-remote-run inventory stock list

# Show stock details
mage-remote-run inventory stock show 1

# List sources
mage-remote-run inventory source list --page 1 --size 20
mage-remote-run inventory source list --format json

# Resolve stock for a sales channel
mage-remote-run inventory resolve-stock website base
# Output: Resolved Stock ID: 1

# List source selection algorithms
mage-remote-run inventory source selection-algorithm list
```

## Company management (B2B)

Company commands are available for Adobe Commerce installations with B2B modules enabled.

```bash
# List companies
mage-remote-run company list --page 1 --size 10
mage-remote-run company list --sort-by entity_id --sort-order DESC

# Show one company
mage-remote-run company show 1
mage-remote-run company show 1 --format json

# Create a company interactively
# Set MAGE_REMOTE_RUN_DEFAULT_COUNTRY=DE to change the default country
mage-remote-run company create

# Update or delete a company
mage-remote-run company update 1
mage-remote-run company delete 1

# Show company structure
mage-remote-run company structure 1

# Work with company roles
mage-remote-run company role list
mage-remote-run company role show 1

# Work with credit information
mage-remote-run company credit show 1
mage-remote-run company credit history 1

# Adjust credit balances
mage-remote-run company credit increase 1 500.00 --comment "Credit adjustment" --po "PO123"
mage-remote-run company credit decrease 1 100.00 --comment "Deduction" --po "PO456"
```

## Webhook management (SaaS)

Configure Adobe Commerce SaaS webhooks for event-driven integrations.

```bash
# List configured webhooks
mage-remote-run webhook list
mage-remote-run webhook list --format json

# List supported webhook types
mage-remote-run webhook supported-list
mage-remote-run webhook supported-list --format json

# Show one webhook
mage-remote-run webhook show "Product Save Hook"

# Create a webhook interactively
mage-remote-run webhook create

# Create a webhook with explicit configuration
mage-remote-run webhook create \
  --name "Order Complete Hook" \
  --webhook-method "observer.sales_order_save_after" \
  --webhook-type "after" \
  --url "https://example.com/orders" \
  --method "POST" \
  --timeout "5000" \
  --soft-timeout "3000" \
  --ttl "3600" \
  --priority "10" \
  --required

# Create a webhook with fields, headers, and rules
mage-remote-run webhook create \
  --name "Product Update Hook" \
  --webhook-method "observer.catalog_product_save_after" \
  --webhook-type "after" \
  --url "https://example.com/products" \
  --fields '[{"name":"product_id","source":"product.entity_id"}]' \
  --headers '[{"name":"X-Auth-Token","value":"secret123"}]' \
  --rules '[{"field":"product.status","operator":"eq","value":"1"}]'

# Delete a webhook
mage-remote-run webhook delete "Product Save Hook"
```

## Event management (Adobe I/O)

Manage Adobe I/O Events configuration and providers.

```bash
# Check Adobe I/O Events setup
mage-remote-run event check-configuration
mage-remote-run event check-configuration --format json

# List providers
mage-remote-run event provider list
mage-remote-run event provider list --format json

# Show a provider
mage-remote-run event provider show <id>

# Create a provider interactively
mage-remote-run event provider create

# Delete a provider
mage-remote-run event provider delete <id>

# List supported events
mage-remote-run event supported-list
mage-remote-run event supported-list --format json
```

## Import commands

Import JSON or CSV data into Adobe Commerce with support for multiple entity types and import behaviors.

```bash
# Import JSON interactively
mage-remote-run import json data.json

# Import JSON with explicit options
mage-remote-run import json products.json \
  --entity-type catalog_product \
  --behavior append \
  --allowed-error-count 10 \
  --validation-strategy validation-skip-errors

# Pipe JSON over stdin
cat data.json | mage-remote-run import json

# Import CSV (PaaS/on-prem only)
mage-remote-run import csv products.csv

# Import CSV with custom separators
mage-remote-run import csv products.csv \
  --field-separator ";" \
  --multi-value-separator "|"

# Import CSV with full options
mage-remote-run import csv customers.csv \
  --entity-type customer \
  --behavior replace \
  --validation-strategy validation-skip-errors \
  --allowed-error-count 10

# Entity types: catalog_product, customer, customer_composite, advanced_pricing
# Behaviors: append, replace, delete_entity
```

## REST API commands

Use the manual REST command when you need to call custom or unsupported endpoints directly.

```bash
# Interactive REST mode
mage-remote-run rest

# GET requests
mage-remote-run rest V1/store/websites
mage-remote-run rest V1/customers/1 -m GET

# POST JSON data
mage-remote-run rest V1/customers -m POST -d '{"customer": {"email": "test@example.com", "firstname": "John", "lastname": "Doe"}}'

# Add query parameters
mage-remote-run rest V1/products -q "searchCriteria[pageSize]=10&fields=items[sku,name]"

# Use pagination shortcuts
mage-remote-run rest V1/products --page 1 --size 10

# Apply filters and sorting
mage-remote-run rest V1/products --filter "status=pending" --filter "grand_total>100" --sort "created_at:DESC"

# Redirect machine-readable output to a file
mage-remote-run rest V1/store/websites -f json > websites.json
```

## Filtering syntax

Many list endpoints support `--filter` with either shorthand operators or explicit syntax.

```bash
# Shorthand operators
--filter "status=1"           # Equals
--filter "price>100"          # Greater than
--filter "price<50"           # Less than
--filter "price>=100"         # Greater than or equal
--filter "price<=50"          # Less than or equal
--filter "status!=canceled"   # Not equal
--filter "name~*shirt*"       # LIKE (contains)
--filter "name!~*shirt*"      # NOT LIKE
--filter "created_at!"        # Is not null
--filter "updated_at?"        # Is null

# Explicit operators
--filter "entity_id:in=1,2,3"         # In list
--filter "entity_id:!in=1,2,3"        # Not in list
--filter "category_ids@@3"            # FIND IN SET
--filter "created_at:from=2024-01-01" # Range start
--filter "created_at:to=2024-12-31"   # Range end

# Combine filters with AND
mage-remote-run order list --filter "status=pending" --filter "grand_total>100"

# OR conditions
mage-remote-run product list --filter "sku:like=DRONE-* || price>100"

# Practical examples
mage-remote-run product list --filter "type_id=simple" --filter "price>=100"
mage-remote-run customer list --filter "email~*@example.com*" --filter "group_id=1"
mage-remote-run customer group list --filter "code~*VIP*"
```

## EAV attribute set management

Inspect attribute sets with pagination support.

```bash
# List attribute sets
mage-remote-run eav attribute-set list --page 1 --size 20
mage-remote-run eav attribute-set list --format json

# Show one attribute set
mage-remote-run eav attribute-set show 4
```

## Tax class management

Inspect tax classes with pagination support.

```bash
# List tax classes
mage-remote-run tax class list --page 1 --size 20
mage-remote-run tax class list --format json

# Show one tax class
mage-remote-run tax class show 2
```

## Module management

List installed modules when the target is not Adobe Commerce SaaS.

```bash
# List installed modules
mage-remote-run module list
mage-remote-run module list --format json
mage-remote-run module list --format xml
```

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

# Command groups:
# @safe - read-only commands (default)
# @risky / @all - full command set
# Domain groups: @connection, @website, @store, @customer, @order, @product, @cart, @tax, @inventory, @shipment, @event, @webhook, @company, @po-cart, @import, @module, @plugin, @rest, @console, @eav
# Combined groups: @sales, @catalog, @cloud, @commerce

# Tool names look like: website_list, store_view_list, order_show
# HTTP endpoints: GET /sse and POST /messages
# Auth: ?token=<token> for SSE or Authorization: Bearer <token> for POST
```

## Interactive console (REPL)

Launch a shell-like console for running CLI commands or JavaScript without repeatedly typing the binary name.

```bash
# Start the console
mage-remote-run console
# or
mage-remote-run repl

# Enable debug mode
mage-remote-run console --debug

# In the console you can:
# - run CLI commands like: website list, order latest, customer search user@example.com
# - execute JavaScript with top-level await
# - use 'list' to inspect commands
# - use 'help' for command help
# - use '.exit' to quit

# Available globals:
# client - async API client factory
# config - current configuration
# chalk - output styling
# reload() - reload config and commands
```

## Plugin development

Extend the CLI with plugins that register commands or subscribe to runtime events.

```javascript
// index.js - Example plugin structure
import chalk from 'chalk';

/**
 * @param {Object} context
 * @param {import('commander').Command} context.program - Commander.js program instance
 * @param {Object} context.config - User configuration object
 * @param {Object|null} context.profile - Active profile (if any)
 * @param {import('events').EventEmitter} context.eventBus - Application event bus
 * @param {Object} context.events - Event name constants
 * @param {Function} context.createClient - Factory to create API client
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

    // Subscribe to application events
    eventBus.on(events.BEFORE_COMMAND, (data) => {
        const { thisCommand, actionCommand, profile } = data;
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

# Supported profile types for plugins:
# magento-os, mage-os, ac-on-prem, ac-cloud-paas, ac-saas
```

## Configuration and environment variables

The CLI stores config and token cache files in standard OS-specific locations and supports a small set of environment variables for customization.

```bash
# Configuration file locations:
# macOS: ~/.config/mage-remote-run/config.json
# Linux: ~/.config/mage-remote-run/config.json
# Windows: %APPDATA%\mage-remote-run\Config\config.json

# Cache file locations:
# macOS: ~/Library/Caches/mage-remote-run/token-cache.json
# Linux: ~/.cache/mage-remote-run/token-cache.json
# Windows: %LOCALAPPDATA%\mage-remote-run\Cache\token-cache.json

# Environment variables:
# DEBUG                           - Enable debug output when set
# MAGE_REMOTE_RUN_DEFAULT_COUNTRY - Default country for company create (default: US)
# MAGE_REMOTE_RUN_MCP_TOKEN       - Auth token for MCP HTTP transport
# NODE_ENV                        - Runtime mode; test mode disables spec cache

# Create initial configuration
mage-remote-run connection add
```

## Summary

Mage Remote Run provides a single CLI surface for remote commerce administration across Adobe Commerce, Magento Open Source, and Mage-OS. It covers day-to-day operational areas like stores, customers, orders, products, shipments, inventory, tax classes, modules, and B2B company data, while also supporting integration-oriented workflows such as webhooks, Adobe I/O Events, imports, REST calls, and machine-readable output.

It is especially useful for automation and agent-driven tooling because it supports non-interactive execution, structured output, deep filtering, MCP server mode, and a plugin system for custom extensions. Whether the goal is store operations, troubleshooting, remote inspection, or workflow automation, `mage-remote-run` offers a consistent interface across SaaS, PaaS, and on-premise commerce deployments.
