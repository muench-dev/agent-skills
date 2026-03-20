# Filtering Syntax

Many list endpoints support `--filter` with shorthand operators or explicit syntax.

## Shorthand operators

```bash
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
```

## Explicit operators

```bash
--filter "entity_id:in=1,2,3"         # In list
--filter "entity_id:!in=1,2,3"        # Not in list
--filter "category_ids@@3"            # FIND IN SET
--filter "created_at:from=2024-01-01" # Range start
--filter "created_at:to=2024-12-31"   # Range end
```

## Combining filters

```bash
# AND (multiple --filter flags)
mage-remote-run order list --filter "status=pending" --filter "grand_total>100"

# OR (pipe syntax within one --filter value)
mage-remote-run product list --filter "sku:like=DRONE-* || price>100"
```

## Examples

```bash
mage-remote-run product list --filter "type_id=simple" --filter "price>=100"
mage-remote-run customer list --filter "email~*@example.com*" --filter "group_id=1"
mage-remote-run customer group list --filter "code~*VIP*"
mage-remote-run cart list --filter "customer_email:user@example.com:like"
```
