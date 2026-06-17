# Virtual Commands

## Overview

Virtual commands define reusable REST requests through configuration instead of JavaScript. They behave like built-in commands, including generated help, option parsing, and standard output formatting.

They can live directly in CLI `config.json` or be shipped from a plugin.

When a plugin needs logic around a virtual command instead of pure declarative config, use `context.lib.utils` in the plugin entrypoint so filter, sort, pagination, table rendering, and output formatting stay aligned with the built-in commands.

## Example

```json
{
  "$schema": "https://mage-remote-run.muench.dev/config.schema.json",
  "commands": [
    {
      "name": "product get",
      "method": "GET",
      "endpoint": "/:store/V1/products/:sku",
      "description": "Fetch a specific product's data via its SKU",
      "options": {
        "store": {
          "type": "string",
          "required": false,
          "description": "Store view code, e.g. 'default'",
          "default": "all"
        },
        "sku": {
          "type": "string",
          "required": true,
          "description": "Product SKU"
        }
      },
      "connection_types": [
        "ac-cloud-paas",
        "magento-os",
        "mage-os",
        "ac-on-prem"
      ]
    },
    {
      "name": "product list-enabled",
      "method": "GET",
      "endpoint": "/:store/V1/products",
      "description": "List only enabled products",
      "filters": [
        "status=1",
        "visibility:in=2,3,4"
      ],
      "options": {
        "store": {
          "type": "string",
          "default": "all"
        }
      }
    }
  ]
}
```

Using spaces in `name` creates nested commands.

## Namespace-Only Entries

Create a parent command group without an endpoint by supplying metadata only:

```json
{
  "name": "example",
  "description": "Custom namespace for all my example commands",
  "summary": "Holds custom virtual examples"
}
```

## Supported Properties

| Property | Type | Notes |
|---|---|---|
| `name` | string | **Required.** Spaces create nested commands. |
| `method` | string | Required for executable commands (`GET`, `POST`, `PUT`, `DELETE`). |
| `endpoint` | string | Required for executable commands. Supports `:placeholders`. |
| `description` | string | Help text. |
| `summary` | string | Short listing text; falls back to `description`. |
| `options` | object | Map of CLI option definitions. |
| `option` / `parameter` / `parameters` | object | Backward-compatible aliases for `options`. |
| `filters` / `filter` | string or array | Predefined search filters (same syntax as `--filter` flags). |
| `connection_types` | array | Restricts which profile types may use this command. |
| `body` | object | JSON body template for POST/PUT/PATCH (top-level, not inside `options`). |

Each option supports:

| Property | Type | Description |
|---|---|---|
| `required` | boolean | Marks option as required. |
| `default` | any | Fallback value when not provided. |
| `description` | string | Help text for the option. |
| `short` | string | Adds a short flag (e.g., `-s`). |
| `long` | string | Overrides the generated long flag name. |
| `flags` | string | Sets the full Commander flag string directly. |
| `type` | string | Use `"boolean"` for a flag without a value placeholder. |
| `choices` | array | Restricts to predefined values; triggers an interactive prompt when omitted. |

## Mapping Rules

- Options that match `endpoint` placeholders fill those placeholders.
- Remaining options go to the query string for `GET` and `DELETE`.
- Remaining options go to the JSON request body for `POST`, `PUT`, and `PATCH`.

Example:

```bash
mage-remote-run product get --sku 12345 --store default
```

Maps to:

```text
GET /default/V1/products/12345
```

## Filters

Predefined filters support placeholders such as `${firstLetter}` or `{:firstLetter}`.

```json
{
  "name": "product list-by-letter",
  "method": "GET",
  "endpoint": "/:store/V1/products",
  "filters": [
    "name:like=${firstLetter}*"
  ],
  "options": {
    "firstLetter": {
      "type": "string",
      "required": true,
      "description": "First product-name letter"
    }
  }
}
```

Only simple placeholder substitution is supported; JavaScript expressions are not evaluated.

Users can still add extra `--filter` flags at runtime.

## JSON Request Body Templates

For POST/PUT/PATCH, define a `body` object at the top level with `${paramName}` or `{:paramName}` placeholders:

```json
{
  "name": "customer create",
  "method": "POST",
  "endpoint": "/:store/V1/customers",
  "body": {
    "customer": {
      "email": "${email}",
      "firstname": "${firstname}",
      "lastname": "${lastname}",
      "websiteId": "${websiteId}"
    },
    "password": "${password}"
  },
  "options": {
    "store":     { "type": "string", "default": "all" },
    "email":     { "type": "string", "required": true },
    "firstname": { "type": "string", "required": true },
    "lastname":  { "type": "string", "required": true },
    "websiteId": { "type": "string", "default": "1" },
    "password":  { "type": "string", "required": true }
  }
}
```

When `body` is defined, only path parameters (`:store`) are substituted in the URL. Options not referenced in the template are ignored for the body.

### Type Preservation

A placeholder covering the entire string value (e.g., `"${websiteId}"`) preserves the original type — a number stays a number. Mixed-content strings like `"Order ${orderId} created"` always produce a string. Static JSON values (numbers, booleans, `null`) pass through unchanged.

## Option Choices

Adding `choices` to an option restricts it to predefined values:
- Providing the flag via CLI → Commander validates the value against the list.
- Omitting the flag → an interactive selection prompt appears automatically.

### Simple String Choices

```json
"status": {
  "type": "string",
  "required": true,
  "choices": ["enabled", "disabled"]
}
```

### Object Choices (Label + Value)

```json
"choices": [
  { "name": "Not Visible Individually", "value": "1" },
  { "name": "Catalog",                  "value": "2" },
  { "name": "Search",                   "value": "3" },
  { "name": "Catalog, Search",          "value": "4" }
]
```

The `name` displays in the prompt; `value` is sent in the API request.

## Non-Interactive Mode

Interactive prompts are suppressed automatically when any of these conditions is met:

| Condition | Details |
|---|---|
| `CI=<any>` | Set by GitHub Actions, GitLab CI, CircleCI, and most CI systems. |
| `NO_INTERACTIVE=1` | Explicit opt-out. |
| `NON_INTERACTIVE=1` | Alias for `NO_INTERACTIVE=1`. |
| `NONINTERACTIVE=1` | Alias for `NO_INTERACTIVE=1`. |
| stdin is not a TTY | Stdin is piped or redirected. |

In non-interactive mode, missing required options cause an error exit; missing optional options without a default are silently skipped.

If the surrounding plugin needs to inspect or normalize command tokens before dispatch, use `context.lib.commandHelper` alongside these virtual command definitions.
