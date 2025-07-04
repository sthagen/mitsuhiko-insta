# Changelog

All notable changes to insta and cargo-insta are documented here.

## Unreleased

- Bumped `libc` crate to `0.2.174`, fixing building on musl targets, and increasing the MSRV of
  `insta` to `1.64.0` (released Sept 2022)

## 1.43.1

This release in identical in rust code to 1.43.0, but reruns the GitHub Actions
workflows, which failed to create a release within GitHub for 1.43.0.

## 1.43.0

- Add uppercase keyboard shortcuts for bulk operations in `cargo insta review`:
  `A` to accept all, `R` to reject all, and `S` to skip all remaining snapshots.
  #745
- `--unreferenced=auto` (or other relevant values) no longer cleans up pending
  snapshots. A bug where `cargo insta test --unreferenced=auto` would
  incorrectly pass on new pending snapshots has been fixed.
- Support specifying `cargo-nextest` bin with `INSTA_CARGO_NEXTEST_BIN`.  #721 (Louis Fruleux)
- Allow setting `INSTA_WORKSPACE_ROOT` at compile time. This is useful for reproducible binaries
  so they don't contain references to `CARGO_MANIFEST_DIR`. #726 (Pascal Bach)
- Qualify all references in macros to avoid name clashes. #729 (Austin Schey)
- Remove `linked-hash-map` and `pin-project` dependencies.  #742, #741, #738
- `cargo insta review` fails with a helpful error message when run in a non-TTY environment.

## 1.42.2

- Support other indention characters than spaces in inline snapshots.  #679
- Fix an issue where multiple targets with the same root would cause too many pending snapshots to be reported.  #730
- Hide `unseen` option in CLI, as it's pending deprecation.  #732
- Stop `\t` and `\x1b` (ANSI color escape) from causing snapshots to be escaped.  #715
- Improved handling of inline snapshots within `allow_duplicates! { .. }`.  #712

## 1.42.1

- Improved handling of control characters in inline snapshots.  #713
- Add pending deprecation warning for `--accept-unseen`. We've left an issue
  open at <https://github.com/mitsuhiko/insta/issues/659> eliciting feedback on
  whether anyone uses this for a few months.  A warning will now be printed when
  `--accept-unseen` is used, and we'll eventually remove the feature unless we
  get some feedback that it's useful.  #668

## 1.42.0

- Text snapshots no longer contain `snapshot_type: text` in their metadata.  For
  context, we originally added this in the prior release (1.41.0) to support
  binary snapshots, but some folks disliked the diff noise on any snapshot
  changes, and the maintainers' weighted votes favored reverting.  I apologize
  that this will cause some additional churn for those who used `cargo insta test
  --force-update-snapshots` to update their snapshots to the 1.41 format;
  running this again with 1.42 will remove those metadata entries.  To confirm:
  this doesn't affect whether snapshot tests pass or fail — the worst impact is
  some additional diffs in metadata.  #690
- Pending snapshots are no longer removed throughout the workspace by
  `cargo-insta` before running tests.  Instead, running a test will overwrite or
  remove its own pending snapshot.  To remove all pending snapshots, use `cargo
  insta reject` or run tests with `--unreferenced=delete`.  #651
- `insta::internals::SettingsBindDropGuard` (returned from
  `Settings::bind_to_scope`) no longer implements `Send`. This was incorrect and
  any tests relying on this behavior where not working properly. Fixes #694 in
  #695 by @jalil-salame

## 1.41.1

- Re-release of 1.41.1 to generate release artifacts correctly.

## 1.41.0

- Experimental support for binary snapshots.  #610 (Florian Plattner)

- `--force-update-snapshots` now causes `cargo-insta` to write every snapshot, regardless of whether
  snapshots fully match, and now implies `--accept`.  This
  allows for `--force-update-snapshots` to update inline snapshots'
  delimiters and indentation.

  For the previous behavior of `--force-update-snapshots`, which limited writes to
  snapshots which didn't fully match, use `--require-full-match`.
  The main difference between `--require-full-match` and the existing behavior of `--force-update-snapshots`
  is a non-zero exit code on any snapshots which don't fully match.

  Like the previous behavior of `--force-update-snapshots`, `--require-full-match`
  doesn't track inline snapshots' delimiters or
  indentation, so can't update if those don't match.  #644

- Inline snapshots only use `#` characters as delimiters when required.  #603

- Warnings for undiscovered snapshots are more robust, and include files with
  custom snapshot extensions.  #637

- Insta runs correctly on packages which reference rust files in a parent path.  #626

- Warnings are printed when any snapshot uses a legacy format.  #599

- `cargo insta --version` now prints a version.  #665

- `insta` now internally uses `INSTA_UPDATE=force` rather than
  `INSTA_FORCE_UPDATE=1`.  (This doesn't affect users of `cargo-insta`, which
  handles this internally.)  #482

- `cargo-insta`'s integration tests continue to grow over the past couple of versions,
  and now offer coverage of most of `cargo-insta`'s interface.

## 1.40.0

- `cargo-insta` no longer panics when running `cargo insta test --accept --workspace`
  on a workspace with a default crate.  #532

- MSRV for `insta` has been raised to 1.60, and for `cargo-insta` to 1.64.

- Added support for compact debug snapshots (`assert_compact_debug_snapshot`).  #514

- Deprecate `--no-force-pass` in `cargo-insta`.  The `--check` option covers the
  same functionality and has a clearer name.  #513

- Inline snapshots now use the required number of `#`s to escape the snapshot
  value, rather than always using `###`. This allows snapshotting values which
  themselves contain `###`. If there are no existing `#` characters in the
  snapshot value, a single `#` will be used.  #540

- Inline snapshots can now be updated with `--force-update-snapshots`.  #569

- `cargo insta test` accepts multiple `--exclude` flags.  #520

- `test` `runner` in insta's yaml config works.  #544

- Print a warning when encountering old snapshot formats.  #503

- Group the options in `cargo insta --help`, upgrade to `clap` from `structopt`.  #518

- No longer suggest running `cargo insta` message when running `cargo insta test --check`.  #515

- Print a clearer error message when accepting a snapshot that was removed.  #516

- Mark `require-full-match` as experimental, given some corner-cases are currently difficult to manage.  #497

- Add a new integration test approach for `cargo-insta` and a set of integration tests.  #537

- Enable Filters to be created from `IntoIterator` types, rather than just `Vec`s.  #570

- Implemented total sort order for an internal `Key` type correctly.  This prevents potential
  crashes introduced by the new sort algorithm in Rust 1.81.  #586

## 1.39.0

- Fixed a bug in `require_full_match`.  #485

- Fixed a bug that caused snapshot and module names to sometimes be inaccurate.  #483

- Insta will no longer error when attempting to remove snapshots that were already removed.  #484

- Added support for trailing commas in inline snapshots.  #472

- Don't pass `--color` in all cases to `libtest` any more to work around limitations
  with custom test harnesses.  #491

## 1.38.0

- `Filters` is now constructible from `IntoIterator`.  #400

- Change `std` macro calls to be fully qualified.  This fixes issues where
  the prelude was not used or the macros were overridden.  #469

## 1.37.0

- All macros for file snapshots should now handle trailing commas (but not yet inline snapshots)

- Vendored old `yaml-rust` dependency to avoid rustsec warnings.  #465

## 1.36.1

- Fix an ownership issue introduced in 1.36 with snapshot assertions.  #453

## 1.36.0

- Deprecate `INSTA_FORCE_UPDATE_SNAPSHOTS` env-var for `INSTA_FORCE_UPDATE`.
  The latter was documented, the former was implemented.  #449

- Add `require_full_match` option.  #448

- Deprecate `assert_display_snapshot!`.  #385

## 1.35.1

- Fixed a bug with diffs showing bogus newlines.

## 1.35.0

- Fixed a crash when a file named `.config` was in the root.
- Added new alternative `match .. { ... }` syntax to redactions for better
  `rustfmt` support.  (#428)
- The `--package` parameter can be supplied multiple times now.  (#427)
- Leading newlines in snapshots are now ignored to resolve issues with
  inline snapshots that were never able to match.  (#444)
- `cargo insta test` now accepts the `--test` parameter multiple times.  (#437)

## 1.34.0

- Snapshots are now sorted in the UI on review.  (#413)
- Re-organized repository to move `cargo-insta` into a workspace.  (#410)
- Fixed handling of `--manifest-path` with regards to virtual workspaces.  (#409)

## 1.33.0

- Added `--all-targets` parameter support to `cargo insta test`.  (#408)

## 1.32.0

- Added `--profile` parameter support to `cargo insta test`.

## 1.31.0

- Fixed a bug that caused `cargo insta test` not to report test failures.
- Suppress `needless_raw_string_hashes` clippy lint on inline snapshots.  (#390)

## 1.30.0

- Resolved a bug on Windows that caused `input_file` not to be written into the
  snapshots.  (#386)
- Snapshots are accepted when running with `--accept` even if a test outside
  insta fails.  (#358)
- Mark settings drop guard as `#[must_use]`.
- Write inline snapshots with atomic rename to avoid some rare races.  (#373)
- Pass `--color=...` to libtest to propagate color choices in more situations.  (#375)

## 1.29.0

- Fixed a rendering bug with snapshot display (lines were not
  rendered to the terminal width).
- Added `--exclude` option to `cargo insta test`. (#360)
- Inherit `color` option from a `CARGO_TERM_COLOR` environment variable (#361)

## 1.28.0

- Added `allow_duplicates!` to enable multiple assertions for a
  single snapshot. (#346)
- Ensure that expressions formatted with `rustfmt` use unix newlines.
- Added a way to disable diffing in review. (#348)
- Added three argument version of `glob!` to set a different base
  path. (#347)
- Added `rounded_redaction` to truncate floating point values. (#350)

## 1.27.0

- Fix an issue where the inline snapshot patcher could panic in
  certain situations. (#341)
- `cargo insta test` now correctly detects CI environments like
  `cargo test` does. In that case it will by fail rather than
  create snapshot update files. (#345)
- Added `cargo insta test --check` to force check runs. (#345)

## 1.26.0

- Make canonicalization in `glob!` optional to better support WASI.

## 1.25.0

- Added a way to disable the undiscoverable snapshots warning. By
  setting the `review.warn_undiscovered` config key to `false` a
  warning is never printed. (#334)
- Force updating snapshots will now not overwrite files that did not
  change. This improves the behavior for situations if that behavior
  is preferred as a default choice. (#335)

## 1.24.1

- Fix non working `--include-hidden` flag (#331)
- Fix incorrect mapping of `review.include_ignored` (#330)

## 1.24.0

- Added an insta tool config (`.config/insta.yaml`) to change the
  behavior of insta and cargo-insta. (#322)
- Renamed `--no-ignore` to `--include-ignored`.
- Added `--include-hidden` to instruct insta to also walk into
  hidden paths.
- Added new `--unreferenced` option to `cargo-insta test` which allows
  fine tuning of what should happen with unreferenced files. It's now
  possible to ignore (default), warn, reject or delete unreferenced
  snapshots. (#328)
- Resolved an error message about doc tests when using nextest with
  test targeting. (#317)

## 1.23.0

- Add a hint if snapshots might be skipped. (#314)
- Avoid extra newline in YAML snapshots. (#311)

## 1.22.0

- Added support for rendering some invisibles in diffs. This now also
  should make sure that ANSI sequences in strings are no longer screwing
  up the terminal output. (#308)
- Prevent inline snapshots to be used in loops. (#307)
- Support the `--target` option to `cargo insta test`. (#309)
- Globbing now adds directories as disambiguators into the snapshot
  suffixes. This allows patterns such as `foo/*/*.txt` without
  creating conflicts. (#310)

## 1.21.2

- Added missing parameters to `cargo insta test`. (#305)
- Fixed a sorting issue in hash maps for compound keys. (#304)

## 1.21.1

- Fix incorrect handling of extra args to `cargo insta test`.

## 1.21.0

- Fixed an issue that broke support for older rust versions. (#292)
- Added `cargo insta show` command to render a snapshot.
- Added support for compact JSON snapshots. (#288)

## 1.20.0

- `cargo insta` now supports nextest as test runner. (#285)
- The `glob!` macro now defers failures by default. (#284)

## 1.19.1

- Added support for numeric keys in JSON which regressed in 0.18.0. (#281)

## 1.19.0

- Removed `backtrace` feature.
- Removed `serialization` feature.
- `assert_json_snapshot!` and `assert_yaml_snapshot!` now require
  the `json` and `yaml` feature respectively.
- Doctests now emit a warning that inline snapshot updating is
  not supported (#272)
- Added support for `INSTA_GLOB_FILTER` to skip over tests expanded
  from a glob. (#274)

## 1.18.2

- Avoid the use of `#[allow(unused)]` in the macro. (#271)

## 1.18.1

- Fixed a regression in the JSON serialization format with newtypes and
  tuple variants. (#270)

## 1.18.0

- `Settings::bind` now can return a result.
- Expose the drop guard type of `bind_to_scope`.
- The `serde` dependency is now optional. While still enabled by default
  users need to opt into `yaml` and `json` features explicitly to regain
  support for it. To avoid the default `serde` dependency the default
  features just need to be disabled. (#255)
- Deprecated unused `serialization` features.
- Deprecated unused `backtrace` feature.
- Removed deprecated `Settings::bind_to_thread`.

**Breaking Changes / Upgrading:** If you are upgrading to serde 1.18.0 you will
receive deprecating warnings if you are using the `assert_yaml_snapshot!` and
`assert_json_snapshot!` macros. These macros will continue to function in the
future but they will require explicit opting into the `yaml` and `json` features.
To silence the warning add them to your `insta` dependency. Additionally the
`backtrace` feature was deprecated. It is no longer needed so just remove it.

## 1.17.2

- Remove an accidentally debug print output.

## 1.17.1

- Added support for nextest. (#242)
- Resolved an issue where inline snapshot tests in doctests refused to
  work. (#252)

## 1.17.0

- Fixed an issue in `cargo-insta` where sometimes accepting inline snapshots
  would crash with an out of bounds panic.
- Added new `filters` feature. (#245)
- Disallow unnamed snapshots in doctests. (#246)
- `with_settings!` macro now inherits the former settings rather than resetting. (#249)
- Added support for `Settings::bind_to_scope` and deprecated
  `Settings::bind_to_thread`. (#250)
- Added support for `minimal-versions` builds.

## 1.16.0

- Added `--no-quiet`/`-Q` flag to `cargo insta test` to suppress the
  quiet flag. This works around limitations with custom test harnesses
  such as cucumber.
- Update RON to 0.7.1.
- Improved ergonomics around `with_settings!`. It's now a perfect match to
  the settings object's setter methods.
- Added `description` and `info` to snapshots. (#239)
- Added `omit_expression` setting. (#239)
- Added improved support for running insta from doctests. (#243)

## 1.15.0

- Bump minimum version of Rust to 1.56.1. This was done because the used
  serde-yaml dependency no longer supports older versions of Rust.

## 1.14.1

- Update uuid crate to 1.0.0. (#228)
- Use inline block format also for strings of form `"foo\n"`. (#225)

## 1.14.0

- Fixed a bug that caused insta to panic if inline snapshot assertions
  moved since the time of the snapshot creation. (#220)
- `cargo insta test` now returns non zero status code when snapshots
  are left for review. (#222)
- Assertion failures now mention `cargo insta test`. (#223)

## 1.13.0

- Fixed a bug where an extra newline was emitted following the snapshot header.
- `assertion_line` is no longer retained in snapshots. (#218)

## 1.12.0

- Add support for sorting redactions (`sorted_redaction` and `Settings::sort_selector`). (#212)
- Changed snapshot name detection to no longer use thread names but function names. (#213)

**Upgrade Notes:**

Insta used to detect the current test name by using the current thread name. This
appeared to work well but unfortunately ran into various limitations. In particular
in some cases the thread name was truncated, missing or did not point to the current
test name. To better support different platforms and situations insta now uses the
function name instead.

This however changes behavior. In particular if you are using a helper function to
assert, a different snapshot name will now be used. You can work around this issue
by using a helper macro instead or to explicitly pass a snapshot name in such
situations.

## 1.11.0

- Trim down some unnecessary dependencies and switch to `once_cell`. (#208)

## 1.10.0

- Update internal dependencies for console and ron.

## 1.9.0

- `cargo-insta` now correctly handles the package (`-p`) argument
  on `test` when deleting unreferenced snapshots. (#201)

## 1.8.0

- Added the ability to redact into a key. (#192)
- Insta now memorizes assertion line numbers in snapshots. While these
  will quickly be old, they are often useful when reviewing snapshots
  immediately after creation with `cargo-insta`. (#191)

## 1.7.2

- Fixed an issue where selectors could not start with underscore. (#189)
- Allow passing arguments to `cargo test`. (#183)
- Avoid the use of `Box::leak`. (#185)
- When `INSTA_WORKSPACE_ROOT` is set, the value is used as the manifest
  directory rather than whatever `CARGO_MANIFEST_DIR` was set to at compile
  time. (#180)

## 1.7.1

- Removed an accidental debug print. (#175)

## 1.7.0

- Added support for u128/i128. (#169)
- Normalize newlines to unix before before asserting. (#172)
- Switch diffing to patience. (#173)

## 1.6.3

- Fix a bug with empty lines in inline snapshots. (#166)

## 1.6.2

- Lower Rust support to 1.41.0 (#165)

## 1.6.1

- Bump similar dependency to reintroduce support for Rust 1.43.0 (#162)
- Fixed custom extension support in cargo-insta (#163)

## 1.6.0

- Change CSV serialization format to format multiple structs as
  multiple rows. (#156)
- Improvements to diff rendering.
- Detect some snapshot name clashes. (#159)

## 1.5.3

- Replace [difference](https://crates.io/crates/difference) with
  [similar](https://crates.io/crates/similar).

## 1.5.2

- API documentation updates.

## 1.5.1

- Fixed glob not working correctly.
- Fail by default if glob is not returning any matches. Fixes #151.

## 1.5.0

- Add `pending-snapshots` parameter to `cargo-insta`.
- `cargo-insta` now honors ignore files. This can be overridden
  with `--no-ignore`.
- `cargo-insta` now supports the vscode extension.

## 1.4.0

- Add `--delete-unreferenced-snapshots` parameter to `cargo-insta`.
- Switch to the `globset` crate for the `glob` feature.
- When `INSTA_UPDATE` is set to `always` or `unseen` it won't
  fail on execution.
- Changed informational outputs also show on pass.

## 1.3.0

- Expose more useful methods from `Content`.
- Fixes for latest rustc version.

## 1.2.0

- Fix invalid offset calculation for inline snapshot (#137)
- Added support for newtype variant redactions. (#139)

## 1.1.0

- Added the `INSTA_SNAPSHOT_REFERENCES_FILE` environment variable to support
  deletions of unreferenced snapshot files. (#136)
- Added support for TOML serializations.
- Avoid diff calculation on large input files. (#135)
- Added `prepend_module_to_snapshot` flag to disable prepending of module
  names to snapshot files. (#133)
- Made `console` dependency optional. The `colors` feature can be disabled now
  which disables colored output.

## 1.0.0

- Globs now follow links (#132)
- Added CSV Support (#134)
- Changed globs to also include directories not just files.
- Support snapshots outside source folder. (#70)
- Update RON to 0.6.

## 0.16.1

- Add `Settings::bind_async` when the `async` feature is enabled. (#121)
- Bumped `console` dependency to 0.11. (#124)
- Fixed incorrect path handling for `glob!`. (#123)
- Remove `cargo-insta` from workspace and add `Cargo.lock`. (#116)

## 0.16.0

- Made snapshot names optional for inline snapshots. (#106)
- Remove legacy macros. (#115)
- Made small improvements to cargo-insta's messaging and flags (#114)
- Added new logo.
- Added `glob` support. (#112)
- Made `MetaData` fields internal. (#111)

## 0.15.0

- Added test output control (`INSTA_OUTPUT` envvar). (#103)

## 0.14.0

- Dependency bump for `console` (lowers total dependency count)
- Change binary name to `cargo insta` in help pages.

## 0.13.1

- Added support for `INSTA_UPDATE=unseen` to write out unseen snapshots without review (#96)
- Added the `backtrace` feature which adds support for test name (and thus snapshot name)
  recovery from the backtrace if rust-test is not used in concurrent mode (#94, #98)

## 0.13

- Add support for deep wildcard matches (#92)
- Use module paths for test names (#87)
- Do not emit useless indentations for empty lines (#88)

## 0.12

- Improve redactions support (#81)
- Deprecated macros are now hidden
- Reduce number of dependencies further.
- Added support for newtype struct redactions.
- Fixed bugs with recursive content operations (#80)

## 0.11

- redactions are now an optional feature that must be turned on to be used (`redactions`).
- RON format is now an optional feature that must be turned on to be used (`ron`).
- added support for sorting maps before serialization.
- added settings support.
- added support for overriding the snapshot path.
- correctly handle nested macros that might contain inline snapshots.
- use thread name as snapshot name for inline snapshots.
- use leading whitespace normalization for inline snapshots.
- removed `creator` and `created` field from snapshot metadata.
- removed the `_matches` suffix from all macros.
- added an `--accept` option to `cargo insta test`
- added `--force-update-snapshots` option to `cargo insta test`
- added `--jobs` and `--release` argument to `cargo insta test`.

To upgrade to the new insta macros and snapshot formats you can use
[`fastmod`](https://crates.io/crates/fastmod) and `cargo-insta` together:

```sh
cargo install fastmod
cargo install cargo-insta
fastmod '\bassert_([a-z]+_snapshot)_matches!' 'assert_${`}!' -e rs --accept-all
cargo insta test --all --force-update-snapshots --accept
```
