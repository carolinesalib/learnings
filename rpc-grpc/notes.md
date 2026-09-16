# RPC & gRPC (Ruby)

## Goal

Understand RPC as a concept, then move to gRPC + Protocol Buffers — the version actually used in industry. Motivated by needing to update a `.proto` file at work without understanding what was going on.

## Plan

1. `01-plain-rpc-drb/` — build a tiny RPC service with Ruby's built-in DRb (Distributed Ruby) over a socket, no protobuf, to see the raw mechanism: client calls a method → serialized → sent over the wire → executed on a remote object → result comes back.
2. `02-grpc-unary/` — write a `.proto` file, generate Ruby stubs, build a working unary (single request/response) gRPC server + client.
3. `03-grpc-streaming/` — extend to server-streaming, client-streaming, and bidirectional streaming RPCs.

## Log

- 2026-09-15: Repo set up. Starting with `01-plain-rpc-drb`.
