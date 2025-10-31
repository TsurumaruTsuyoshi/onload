# Repository Guidelines

## Project Structure & Module Organization
OpenOnload pairs kernel drivers with user-space acceleration libraries. Core source lives in `src/`, with `src/driver` hosting kernel modules, `src/lib` shared components, `src/onload` user tooling, and `src/tests` unit and integration suites. Helper utilities stay under `scripts/`, while `mk/` and `rakelib/` define reusable build fragments. Build outputs accumulate in `build/`; keep that tree out of version control and stash large artifacts elsewhere.

## Build, Test, and Development Commands
Set up helper scripts first: `export PATH="$PWD/scripts:$PATH"`. Initialize build trees with `mmakebuildtree --driver` and `mmakebuildtree --user`. Build from the generated trees using `make -C "$(mmaketool --toppath)/build/$(mmaketool --driverbuild)"` for the kernel modules and the equivalent `--userbuild` path for libraries. For a quick driver-only rebuild, `make kernel` at the repo root is sufficient. Packaging helpers include `scripts/onload_mkdist` for release tarballs and `scripts/onload_mkpackage --install` for end-to-end DEB/RPM builds; to generate Debian source artefacts directly call `scripts/debian/onload_mksrcdeb.sh --tarball <tgz> --out <dir> [--debian-revision <n>]`.

## Coding Style & Naming Conventions
Match the surrounding file’s style; most C sources expect 79-character lines, two-space indentation, C-style comments, and braces on the same line as the control statement. Keep keywords tight to parentheses (`if( condition )`). Prefer descriptive snake_case for functions and locals, and upper-case constants for macros. Avoid C++-style comments unless the file already uses them. Run with `MMAKE_LIBERAL=1` only when temporarily relaxing `-Werror`; do not commit with warnings enabled.

## Testing Guidelines
Unit tests reside under `src/tests/onload` and related subtrees. After building the user tree, run the suite with `scripts/run_unit_tests.sh`, which rebuilds and executes the available binaries. Add new tests alongside the code they cover, mirroring existing naming (`*_unit.c` for C tests). Aim to cover new control paths and device compatibility toggles; document any gaps in the pull request. For kernel-sensitive changes, build against an older supported kernel listed in `README.md` to flush out compatibility regressions.

## Commit & Pull Request Guidelines
Author commits on short-lived topic branches. Follow the existing log format—ticket-prefixed (`ON-12345: brief imperative summary`) or lowercase verbs such as `fix:` and `cleanup:`—and keep the subject under ~72 characters with details in the body. Each commit should compile independently and note the relevant testing (for example, “Tests: scripts/run_unit_tests.sh”). Pull requests must include a concise change description, testing evidence, and any linked issues, and they require sign-off from two maintainers before merge.
