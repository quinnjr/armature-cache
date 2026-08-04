# Changelog — `armature-cache`

All notable changes to this crate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this crate adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Earlier changes are recorded in the workspace [`CHANGELOG.md`](../CHANGELOG.md).

## [Unreleased]

### Added

- Adopted the `cache` criterion benchmark (keys, in-memory and tiered stores, TTL, concurrent access) from the root package's `benches/`. Run it with `cargo bench -p armature-cache --bench cache`. The crate now sets `autobenches = false`, so a new file under `benches/` needs an explicit `[[bench]]` entry. `criterion` also gains the `async_tokio` feature: the store benchmarks drive an async API through `Bencher::to_async`, which is feature-gated, so without it this bench does not compile outside the workspace.

### Fixed

- **Breaking:** an explicit "no TTL" is distinguishable from an unspecified one, so `remember_forever` stops silently inheriting `default_ttl` — on the documented configuration there was no way to store a non-expiring entry.
- Memcached expirations over 30 days are sent as absolute timestamps. The protocol reads any larger value that way, so a 40-day TTL stored an item already expired and `set_json` still returned `Ok(())`.
- The tag index no longer outlives the values it points at, and its writes are batched instead of costing three sequential round-trips per tag.
- L1 eviction is no longer a full scan under the write lock on every insert once full — which every L2 promotion went through.
- `warm_cache` bounds its concurrency instead of issuing one simultaneous factory call per key, the stampede its sibling single-flight exists to prevent.

## [0.4.1] - 2026-08-04

### Fixed

- Requirements on sibling armature crates name a minor instead of `0`. Under
  Cargo's 0.x rules `version = "0"` matches any release ever made, and edition
  2024 selects the MSRV-aware resolver, so a consumer declaring an older
  `rust-version` was handed the oldest version satisfying it — resolving
  `armature-core = "0"` on Rust 1.89 produced `armature-core 0.2.3` while an
  explicit `armature-core = "0.8"` elsewhere in the same graph pulled 0.8.2.
  Two copies of core, and a build failing on symbols the older one lacks. Each
  0.x minor in this family is a breaking change, so the requirement now names
  one. No API change.
