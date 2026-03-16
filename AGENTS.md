# AGENTS.md

## Cursor Cloud specific instructions

**feishu-cli** is a pure Go CLI tool for the Feishu (Lark) Open Platform. No databases, Docker, or background services are needed for development.

### Build, Test, Lint

Standard commands are documented in the `Makefile` and `README.md`. Quick reference:

- **Build:** `make build` (outputs to `bin/feishu-cli`)
- **Test:** `go test ./...`
- **Lint:** `go vet ./...` (or `golangci-lint run ./...` if installed)
- **Format:** `gofmt -w -s .`

### Runtime requirements

All CLI operations call Feishu cloud APIs over HTTPS. To run any command beyond `--help` / `--version`, you need:

- `FEISHU_APP_ID` and `FEISHU_APP_SECRET` environment variables (or `~/.feishu-cli/config.yaml`)
- Network access to `open.feishu.cn`

Without these credentials, the build/test/lint cycle works fully, but actual API commands will fail.

### Hello world verification

To verify credentials work end-to-end: `./bin/feishu-cli doc create --title "Test"`. A successful response returns a document ID and link.

### Gotchas

- Go 1.21+ is required (`go.mod` specifies `go 1.21`). The VM has Go 1.22.2.
- The project has no hot-reload mechanism; after code changes, rebuild with `make build`.
- Tests in `internal/client/` take ~12 seconds due to HTTP retry/timeout tests; this is normal.
- `make lint` requires `golangci-lint` which is not pre-installed; use `go vet ./...` as the baseline lint check.
- `doc import` with `--verbose` shows a 3-phase pipeline; table filling (phase 2) can take 10+ seconds per table due to API latency — this is expected behavior, not a bug.
