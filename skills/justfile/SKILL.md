---
name: justfile
description: Creates, configures, maintains, and uses Just command runners (`justfile` / `Justfile`) in any project. Use when adding or updating a justfile, simplifying a project command interface, creating `just` recipes, or standardizing development commands. For technical projects, it checks for appropriate testing, linting or validation, and application-running recipes. Every justfile it creates or changes includes a default recipe that lists available recipes.
compatibility: Requires the `just` CLI; install it before creating or validating a justfile if it is missing.
---

# Justfile Management

Use `just` as a small, discoverable command interface for project workflows. Favor concise
recipes that delegate to the repository's existing scripts, task runners, containers, or
package-manager commands. Do not duplicate build logic or invent a new toolchain in the
justfile.

In user communication, call them **recipes**. A recipe is a named command in a justfile.

## Inspect First

Before creating or changing a justfile:

1. Look for `justfile` and `Justfile` at the project root. Respect the existing filename and
   style.
2. Read the project README, package manifests, Makefiles, task-runner configuration, container
   configuration, and CI workflows that are relevant to developer commands.
3. Run `just --version` to confirm the CLI is available. If it is unavailable, install it only
   with the user's permission or state the blocker.
4. Identify existing, documented commands for these technical-project concerns:
   - **test**: Unit, integration, end-to-end, or the project's standard complete test command.
   - **lint** or **validate**: Linting, formatting checks, static analysis, or the project's
     documented validation command.
   - **run** or **dev**: Starting the local application, service, or development environment.

Do not add a recipe when the corresponding workflow does not exist or cannot be established from
repository evidence. Report that absence briefly. Prefer the project's existing terminology: for
example, preserve `validate` when that is the established command rather than adding a duplicate
`lint` alias.

## Required Default Recipe

Every justfile created or modified through this skill needs this behavior:

```just
# List all available recipes.
default:
    just --list
```

Keep `default` as the first recipe. It must list all available recipes and must not start
services, modify files, install dependencies, or perform validation.

When an existing default recipe does something else, replace it only after confirming no
documented workflow depends on `just` with no arguments. If it is used externally and cannot
safely change, retain it and explain that the default-listing requirement conflicts with current
behavior.

## Recipe Design

- Use one recipe per developer-facing action and give it a `#` comment so `just --list` is useful.
- Delegate to the canonical command, such as `npm run test`, `composer test`, `ddev test`,
  `make test`, or a documented script.
- Keep commands relative to the project root, since that is where users invoke `just`.
- Use `set shell := ["bash", "-cu"]` only if bash features are actually required. Otherwise
  preserve just's default shell behavior.
- Use parameters only when they make a recurring workflow materially clearer. Document parameter
  defaults in comments.
- Do not use `@` to hide recipe commands by default. Visible commands make local troubleshooting
  easier.
- Do not create aliases or wrappers solely for command names unless they materially improve the
  project interface.
- Keep secrets out of the justfile. Read configuration from the environment or the project's
  established local configuration files.
- Do not make the justfile executable; `just` reads it directly.

## Technical Project Baseline

For technical projects, explicitly evaluate `test`, linting/validation, and `run`/`dev` recipes.
When supported by existing project workflows, create or retain them. A typical result is:

```just
# List all available recipes.
default:
    just --list

# Start the local development server.
run:
    npm run dev

# Run the full test suite.
test:
    npm test

# Lint and type-check the codebase.
validate:
    npm run lint
```

This is illustrative only. Replace commands and recipe names with repository-backed equivalents.
Do not assume Node, PHP, Python, Docker, DDEV, or Make.

## Validation

After editing:

1. Run `just --list` from the project root. Confirm it succeeds and displays `default` plus all
   documented recipes.
2. Run `just --dump` to validate parsing without executing project workflows.
3. Run a changed recipe only when it is safe and its dependencies are available. Prefer
   non-destructive validation commands. Do not start a long-running `run` recipe unless the user
   requested it.
4. Report the justfile path, the recipes added or changed, and any test, lint, or run workflow
   intentionally absent because the repository did not define one.

## Maintenance

When a project's scripts, CI commands, runtime, or development environment changes, update
dependent recipes in the justfile in the same change where practical. Preserve recipes unrelated
to the requested work and avoid reformatting the whole file.
