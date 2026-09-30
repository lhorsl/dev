---
paths:
  - "**/*.go"
---
# Go
- Verify with: `go build ./... && golangci-lint run && go test -race ./...`
- String literals reused across a file/package or likely to change (keys, headers, routes, error codes)
  go in a `const` block at the top of the file, named UPPER_SNAKE_CASE (e.g. `HEADER_REQUEST_ID`).
  This is a deliberate house style over Go's MixedCaps; don't flag it.
- REST handlers get swaggo annotations: @Summary, @Description, @Tags, @Param, @Success, @Failure, @Router.
  gRPC handlers don't; their docs live in the .proto comments.
- Logging: `log/slog` with key-value attributes, not `log` or third-party loggers.
- Tests: table-driven with testify (`require` for preconditions that must stop the test, `assert` for the checks).
