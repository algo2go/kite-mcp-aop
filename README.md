# kite-mcp-aop

[![Go Reference](https://pkg.go.dev/badge/github.com/algo2go/kite-mcp-aop.svg)](https://pkg.go.dev/github.com/algo2go/kite-mcp-aop)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Reflective Aspect-Oriented Programming (AOP) primitives for the
algo2go ecosystem. Generates dynamic proxies that wrap target
struct methods with cross-cutting aspects (audit, rate-limit,
authorization, retry, etc.) — all gated by the `research` build
tag for opt-in experimentation.

## Build gate — `research` tag only

**This package is gated behind the `//go:build research` tag.**
It is EXCLUDED from default `go build ./...` and `go test ./...`
runs. To compile/test it locally:

```bash
go build -tags=research ./...
go test -tags=research ./...
```

This gating is intentional — the package is research-grade and not
production-bound. Reflective dispatch incurs runtime overhead that
production paths shouldn't bear.

## Why a separate module?

AOP infrastructure is an orthogonal cross-cutting research primitive
— useful for prototyping cross-cutting concerns (audit, rate limit,
RBAC, retry, fallback) without touching the target struct's source.
Hosting as a module:

- Lets the `research` tag stay opt-in across consumers
- Enables independent experimentation versioning
- Keeps the dep-graph weight zero for production consumers (the
  package is excluded from non-research builds)

## Stability promise

**v0.x — unstable.** Reflective AOP signatures may evolve. Pin
`v0.1.0` deliberately. v1.0 ships only after the public API stabilizes
across at least 2 external research consumers.

## Install

```bash
go get github.com/algo2go/kite-mcp-aop@v0.1.0
```

## Public API (aop.go + proxy.go)

- `Proxy[T]` — generic dynamic proxy that wraps a target with
  before/after/around aspects
- `Aspect` interface — pluggable cross-cutting hooks
- `BindAspect(target, aspect) Proxy[T]` — proxy construction helper
- See `example_audit_riskguard.go` for a worked audit + riskguard
  aspect composition

## Reference consumer

[`Sundeepg98/kite-mcp-server`](https://github.com/Sundeepg98/kite-mcp-server)
— historical reference; package is unused in production paths
(verified by zero-import analysis at extraction time). Tests still
exercise the package under `-tags=research` as the F7 close-out
canary.

## License

MIT — see [LICENSE](LICENSE).

## Authors

Original design: [Sundeepg98](https://github.com/Sundeepg98) (Zerodha
Tech). Multi-module promotion (2026-05-10): algo2go contributors.
