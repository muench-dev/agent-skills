# Integration Commands

## Webhook management (SaaS only)

```bash
mage-remote-run webhook list
mage-remote-run webhook list --format json
mage-remote-run webhook supported-list
mage-remote-run webhook supported-list --format json
mage-remote-run webhook show "Product Save Hook"

# Create interactively
mage-remote-run webhook create

# Create with explicit configuration
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

# Create with fields, headers, and rules
mage-remote-run webhook create \
  --name "Product Update Hook" \
  --webhook-method "observer.catalog_product_save_after" \
  --webhook-type "after" \
  --url "https://example.com/products" \
  --fields '[{"name":"product_id","source":"product.entity_id"}]' \
  --headers '[{"name":"X-Auth-Token","value":"secret123"}]' \
  --rules '[{"field":"product.status","operator":"eq","value":"1"}]'

mage-remote-run webhook delete "Product Save Hook"
```

## Adobe I/O Events

```bash
mage-remote-run event check-configuration
mage-remote-run event check-configuration --format json
mage-remote-run event provider list
mage-remote-run event provider list --format json
mage-remote-run event provider show <id>
mage-remote-run event provider create
mage-remote-run event provider delete <id>
mage-remote-run event supported-list
mage-remote-run event supported-list --format json
```

## Import commands

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
```

**Entity types:** `catalog_product`, `customer`, `customer_composite`, `advanced_pricing`
**Behaviors:** `append`, `replace`, `delete_entity`

## REST API (manual calls)

Use when calling custom or unsupported endpoints directly.

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

# Redirect to file
mage-remote-run rest V1/store/websites -f json > websites.json
```
