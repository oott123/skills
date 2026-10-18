---
name: go-react-practice
description: Use this skill when the user is working in a Go + React project.
---

# Go + React practice

When creating a new project, adopt the rules into your AGENTS.md or CLAUDE.md as a long-term reminder.

## Go

- Router: huma
- Entry: cobra
- Config: viper
- DB: sqlx + sqlc + goose, NO RAW SQLS IN CODE
- Lint: golangci-lint, with [maratori's config](https://github.com/maratori/golangci-lint-config/blob/main/.golangci.yml)
- Error: fmt.Errorf, ensure all errors that cross package boundaries are wrapped. Check <go-error-handling.md> for details.
- Logging: logrus, adding requestid into your own context and try pass it when logging anything related to a request. Create a package to avoid duplicate code.
- JSON parsing for unknown structure: gjson
- JWT: jose
- Session handling: gorilla/sessions with stateless, encrypted cookies
- SQLite: modernc.org/sqlite
- Postgres: pgx
- CSRF protection: http.CrossOriginProtection
- Unique ID: xid/ulid
- Local S3 or mock: adobe/s3mock
- Asserting: go-cmp/cmp, testify
- Validator: go-playground/validator
- Embedding SPA: //go:embed

## React

- Bundler: vite
- Package manager: pnpm
- Linter & formatter: biome
- Router: tanstack router with file based routing and loader prefetch
  - Always use <Link> for navigation instead of navigate()
- Fetcher: tanstack query with openapi-fetch & openapi-typescript
  - Every query option lives in `src/queries` (`xxxQuery`) as queryOptions wrapper
  - Every failed query or mutation should show a toast
- Typescript: tsgo (typescript@latest)
- Virtual scrolling: tanstack virtual
- Devtools: tanstack devtools
- UI: shadcn/ui if not specified; baseui if headless / design required
- Styles: tailwind css & cva
- datetime: date-fns
- State management: jotai
- Forms: tanstack form
- Validator: zod
- Tables: tanstack table
- Performance monitor: react-scan
