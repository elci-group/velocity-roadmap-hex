# Detailed Roadmap: Replacing `hex` in Velocity

## 1. Current usage audit

`hex` is used only in:

- `crates/velocity-registry/src/lib.rs`

Typical call patterns are `hex::encode(...)` and `hex::decode(...)` for converting registry identifiers or checksums to/from hexadecimal strings.

## 2. Replacement strategy

Implement a minimal internal hex utility module inside `velocity-registry` (or in `velocity-core` if other crates may need it later). The module should provide:

- `fn encode<T: AsRef<[u8]>>(bytes: T) -> String`
- `fn decode(s: &str) -> Result<Vec<u8>, HexError>`

A correct implementation is roughly 40–60 lines and does not require a table lookup larger than 16 bytes.

## 3. Step-by-step migration

1. **Add `velocity-registry/src/hex.rs`** with `encode`, `decode`, and a small `HexError` type.
2. **Replace call sites** in `lib.rs`:
   - `hex::encode(x)` → `crate::hex::encode(x)`
   - `hex::decode(s)?` → `crate::hex::decode(s)?`
3. **Add unit tests** for encode/decode round-trips, invalid input handling, and case insensitivity.
4. **Remove `hex` from `velocity-registry/Cargo.toml`** and from workspace dependencies if no other crate uses it.
5. **Run `cargo test` and `cargo clippy`**.

## 4. Risk and effort assessment

| Risk | Level | Mitigation |
|---|---|---|
| Implementation correctness | Low | Property-based round-trip tests; compare against `hex` output for a corpus of values. |
| Performance | Low | The internal implementation is O(n) and allocation-free in the hot path except for output buffers. |
| API breakage | None | `hex` is not exposed in public APIs. |

**Estimated effort:** 2–4 hours.

## 5. Acceptance criteria

- [ ] `hex` no longer appears in `Cargo.lock` for the workspace.
- [ ] `cargo test` passes.
- [ ] `cargo clippy` passes.
- [ ] Internal hex module has 100% branch coverage for encode/decode and error paths.
- [ ] No remaining `hex::` imports.

## 6. Test plan

1. Round-trip tests for random byte sequences.
2. Decoding tests for mixed-case and invalid-character inputs.
3. A regression test using the exact identifiers currently stored in `fixtures/` if applicable.
4. Benchmark (optional) to confirm no regression versus `hex`.
