---
name: init-mise
description: init a mise config
disable-model-invocation: true
---

# Init a mise config

Adds the following to your `mise.toml`:

```toml
[env]
_.file = [
  ".env.default",
  { path = ".env", optional = true, redact = true },
  { path = ".secrets.json", optional = true, redact = true }
]

[settings.sops]
strict = false

[tasks."dev:server"] # add as much as you need `dev:part` tasks
description = "Start dev backend" # use your own description for every task
run = "command to start a local server for backend"

[tasks."dev:web"]
run = "command to start a dev server for frontend"
dir = "{{ config_root }}/web"

[tasks.dev] # add a single `dev` command to start them all
run = [
  { tasks = ["dev:server", "dev:web"] }
]

[tasks.build] # add a single `build` command to build them all
run = "command to build a whole project"
# you can split in into multiple `build:whatever` steps and `depends` here
# sources = [] # adds sources for build steps
# outputs = [] # adds outputs for build steps
```

Rules:

- `mise run dev` will start a local dev server contains all of the parts, including database using docker compose or external dependencies if needed
- `mise run build` will build the whole project, with local cache if exists
- Write a short description less than 80 chars
- Use TOML tasks instead of file tasks

## Migrate from Makefile

If there are any Makefile exists in this project, and the project is not C, C++, or need Makefile for necessary features, migrate it into mise tasks.
