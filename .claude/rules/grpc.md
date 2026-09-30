---
paths:
  - "**/*.proto"
  - "**/buf*.yaml"
  - "**/*grpc*.go"
---
# gRPC / Protobuf
- Toolchain: buf for linting, breaking-change checks and codegen (not raw protoc); grpc-go
  (`google.golang.org/grpc`) for servers and clients, not connect-go.
- Verify proto changes with `buf lint` and `buf breaking --against '.git#branch=main'`, then `buf generate`.
  Never hand-edit generated `*.pb.go` / `*_grpc.pb.go`; change the .proto and regenerate.
- Wire compatibility: never renumber, reuse or retype a shipped field. When removing one, add its number
  and name to `reserved`. Deployed clients decode by field number, so a reused number corrupts data silently.
- Naming (buf STANDARD lint): messages, services and RPCs PascalCase; fields lower_snake_case; enum values
  UPPER_SNAKE_CASE prefixed with the enum name, with `<ENUM>_UNSPECIFIED = 0` as the zero value.
- Every RPC gets its own `<Rpc>Request` / `<Rpc>Response` message, even when empty, so fields can be
  added later without breaking callers.
- Document services, RPCs and fields with proto comments; they are the API docs.
- Handlers return `status.Error(codes.X, msg)`. Map domain errors to codes in one place at the transport
  boundary, and never put internal error text in a `codes.Internal` message sent to clients.
- Pass the request `ctx` down to DB and outbound calls so client deadlines and cancellation propagate.
- Auth, logging, panic recovery and metrics go in interceptors, not in individual handlers.
- Convert between proto messages and domain types in the gRPC layer; business logic doesn't import generated `pb` packages.
