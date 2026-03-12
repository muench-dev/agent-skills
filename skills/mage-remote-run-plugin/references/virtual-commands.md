# Virtual Commands

## Overview

Virtual commands define reusable REST requests through configuration instead of JavaScript. They behave like built-in commands, including generated help, option parsing, and standard output formatting.

They can live directly in CLI `config.json` or be shipped from a plugin.

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

- `name`: required CLI command path
- `method`: required for executable commands
- `endpoint`: required for executable commands
- `description`: optional help text
- `summary`: optional short listing text
- `options`: option definitions
- `option` / `parameter` / `parameters`: backward-compatible aliases for `options`
- `filters` / `filter`: predefined search filters
- `connection_types`: allowed profile types

Each option supports:

- `required`
- `default`
- `description`
- `short`
- `long`
- `flags`
- `type: "boolean"`

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
