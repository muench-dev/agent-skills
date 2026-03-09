# agent-skills

muench.dev Agent Skills

## Skills

- `docker-setup-fixer` - Lints `docker-compose.yml` / `docker-compose.yaml` files with `dclint` and fixes detected issues. See `skills/docker-setup-fixer/SKILL.md`.
- `mage-remote-run` - Guidance for using the Mage Remote Run CLI to manage Adobe Commerce, Magento Open Source, and Mage-OS instances remotely. See `skills/mage-remote-run/README.md`.

## Install

Use `npx skills` to discover and install the skills from this repository.

```bash
# List available skills
npx skills add muench-dev/agent-skills --list

# Install all skills from this repository
npx skills add muench-dev/agent-skills

# Install only one skill
npx skills add muench-dev/agent-skills --skill mage-remote-run

# Install globally
npx skills add muench-dev/agent-skills -g

# Install for a specific agent
npx skills add muench-dev/agent-skills -a opencode
```
