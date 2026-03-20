# Connection Management & Configuration

## Connection profiles

Manage multiple targets through named profiles. The active profile is used by all API commands.

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

# Add a profile with Bearer token
mage-remote-run connection add \
  --name "Staging" \
  --type magento-os \
  --url "https://staging.example.com" \
  --token "integration_access_token"

# Add a profile with OAuth 1.0a
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

**Profile types:** `magento-os`, `mage-os`, `ac-on-prem`, `ac-cloud-paas`, `ac-saas`

## Configuration file locations

```
macOS/Linux: ~/.config/mage-remote-run/config.json
Windows:     %APPDATA%\mage-remote-run\Config\config.json
```

## Token cache locations

```
macOS:   ~/Library/Caches/mage-remote-run/token-cache.json
Linux:   ~/.cache/mage-remote-run/token-cache.json
Windows: %LOCALAPPDATA%\mage-remote-run\Cache\token-cache.json
```

## Environment variables

| Variable | Description |
|---|---|
| `DEBUG` | Enable debug output when set |
| `MAGE_REMOTE_RUN_DEFAULT_COUNTRY` | Default country for company create (default: US) |
| `MAGE_REMOTE_RUN_MCP_TOKEN` | Auth token for MCP HTTP transport |
| `NODE_ENV` | Runtime mode; `test` disables spec cache |
