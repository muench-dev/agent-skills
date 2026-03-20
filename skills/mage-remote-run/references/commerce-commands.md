# Commerce Commands Reference

## Website management

```bash
mage-remote-run website list
mage-remote-run website search base
mage-remote-run website edit 1
mage-remote-run website delete 2
```

## Store management

```bash
# Store groups
mage-remote-run store group list
mage-remote-run store group list --format json
mage-remote-run store group search "us"
mage-remote-run store group edit 1

# Store views
mage-remote-run store view list
mage-remote-run store view list --format json
mage-remote-run store view search "default"
mage-remote-run store view delete 2

# Configuration
mage-remote-run store config list
```

## Customer management

```bash
mage-remote-run customer list --page 1 --size 20
mage-remote-run customer search "example.com"
mage-remote-run customer show 42
mage-remote-run customer show 42 --format json
mage-remote-run customer create
mage-remote-run customer edit 42
mage-remote-run customer delete 42
mage-remote-run customer delete 42 --force
mage-remote-run customer confirm 42
mage-remote-run customer confirm --redirect-url https://store.example.com/confirm
mage-remote-run customer group list --page 1 --size 20
```

## Order management

```bash
mage-remote-run order list --page 1 --size 20
mage-remote-run order list --status pending
mage-remote-run order list --email user@example.com
mage-remote-run order list --date-from 2023-01-01 --date-to 2023-12-31
mage-remote-run order list --filter "grand_total>=100" --add-fields "base_grand_total,billing_address.city"
mage-remote-run order list --fields "increment_id,grand_total,customer_email"
mage-remote-run order list --sort "grand_total:DESC" --sort "created_at:ASC"
mage-remote-run order search 1000
mage-remote-run order show 42
mage-remote-run order show 100000123 --format json
mage-remote-run order latest --page 1 --size 20
mage-remote-run order latest --select
mage-remote-run order cancel 42
mage-remote-run order hold 42
mage-remote-run order unhold 42
mage-remote-run order edit 42   # Add history comments
```

## Product management

```bash
mage-remote-run product list --page 1 --size 20
mage-remote-run product list --sort-by price --sort-order DESC
mage-remote-run product show "24-MB01"
mage-remote-run product show "24-MB01" --format json
mage-remote-run product type list
mage-remote-run product attribute list --page 1 --size 20
mage-remote-run product attribute show color
mage-remote-run product attribute show color --format json
mage-remote-run product attribute type list
mage-remote-run product link-type list
mage-remote-run product link-type list --format json
```

## Cart management

```bash
mage-remote-run cart list
mage-remote-run cart list --filter "is_active:1"
mage-remote-run cart list --sort "created_at:DESC"
mage-remote-run cart list --page 2 --size 50
mage-remote-run cart list --filter "customer_email:user@example.com:like"
mage-remote-run cart show 42
mage-remote-run cart show 42 --format json
```

## Shipment management

```bash
mage-remote-run shipment list --page 1 --size 20
mage-remote-run shipment list --order-id 42
mage-remote-run shipment show 123
mage-remote-run shipment show 123 --format json
mage-remote-run shipment create 123 --notify
mage-remote-run shipment create 123 --tracks '[{"carrier_code":"fedex","title":"FedEx","track_number":"123456"}]'
mage-remote-run shipment create 123 --items '[{"order_item_id":1,"qty":2}]' --notify
mage-remote-run shipment track 123 --carrier fedex --title FedEx --number 987654321
mage-remote-run shipment label 123
mage-remote-run shipment email 123
mage-remote-run shipment comments 123 --comment "Shipped via FedEx" --notify --visible
```

## Inventory management

```bash
mage-remote-run inventory stock list
mage-remote-run inventory stock show 1
mage-remote-run inventory source list --page 1 --size 20
mage-remote-run inventory source list --format json
mage-remote-run inventory resolve-stock website base
mage-remote-run inventory source selection-algorithm list
```

## Company management (B2B — Adobe Commerce only)

```bash
mage-remote-run company list --page 1 --size 10
mage-remote-run company list --sort-by entity_id --sort-order DESC
mage-remote-run company show 1
mage-remote-run company show 1 --format json
mage-remote-run company create   # Set MAGE_REMOTE_RUN_DEFAULT_COUNTRY=DE to change default country
mage-remote-run company update 1
mage-remote-run company delete 1
mage-remote-run company structure 1
mage-remote-run company role list
mage-remote-run company role show 1
mage-remote-run company credit show 1
mage-remote-run company credit history 1
mage-remote-run company credit increase 1 500.00 --comment "Credit adjustment" --po "PO123"
mage-remote-run company credit decrease 1 100.00 --comment "Deduction" --po "PO456"
```

## EAV attribute sets

```bash
mage-remote-run eav attribute-set list --page 1 --size 20
mage-remote-run eav attribute-set list --format json
mage-remote-run eav attribute-set show 4
```

## Tax classes

```bash
mage-remote-run tax class list --page 1 --size 20
mage-remote-run tax class list --format json
mage-remote-run tax class show 2
```

## Module management (non-SaaS only)

```bash
mage-remote-run module list
mage-remote-run module list --format json
mage-remote-run module list --format xml
```
