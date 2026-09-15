# Changelog — `armature-cache`

All notable changes to this crate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this crate adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Earlier changes are recorded in the workspace [`CHANGELOG.md`](../CHANGELOG.md).

## [Unreleased]

## [0.4.2] - 2026-09-15

### Changed

- Bumped `memcache` 0.19 → 0.21, `redis` 1.3 → 1.7, and `tokio` 1.52 → 1.53. No source changes were needed: the `memcache::Client`/`gets`/`increment`/`decrement`/`touch` surface and the `redis::aio::ConnectionManager`/`AsyncCommands` surface this crate uses are unchanged across those bumps, confirmed by a clean `clippy -D warnings` and a full pass of the Docker-backed memcached/redis integration tests against the new versions.

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
