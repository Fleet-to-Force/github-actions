# Fleet-to-Force GitHub Actions

Reusable GitHub Actions for Fleet-to-Force AssemblyUnit workflows.

This repository is no longer treated as a single-purpose radare2 installer. It is a governed CI action family for enforcing fold-in discipline, zero-trust provenance, and reusable setup/build apparatuses across H_Global-Factory and related AssemblyUnits.

## Current actions

| Action | Path | Purpose |
|---|---|---|
| Install Radare2 | `./` | Install radare2 from release packages or build from git source. Useful only after pinning and zero-trust gates are satisfied. |
| AssemblyUnit Zero-Trust Guard | `./assemblyunit-zero-trust` | Enforce fold-density, generated-authority, normalization, governance, and promotion-blocking invariants. |

## AssemblyUnit Zero-Trust Guard

Use this guard in repos that contain AssemblyUnit fold maps.

```yaml
name: AssemblyUnit zero-trust

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  guard:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: Fleet-to-Force/github-actions/assemblyunit-zero-trust@v1
        with:
          assembly-root: H_Global-Factory/.pixi/L3-assembly/.pixi/L3-radare2-sublation-map
```

The guard enforces:

- no hand-authored canonical `*.cue`, `*.ncl`, or `*.jsonld` unless computed/generated provenance is present
- no manual source-intake result without computed provenance
- every `*.fold.yamlld` declares sublation density
- every fold has a matching normalization file
- no governance conflation such as `Go/CUE` or `Rust/Nickel`
- no premature build, self-witness, external witness, or promotion claims

## Install Radare2 Action

Composite GitHub Action to install radare2 in CI workflows. Supports Linux, macOS and Windows runners, with options to install from release packages or build from git source.

### Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `version` | Radare2 version to install (e.g., `6.1.2`). Empty for latest. | `""` |
| `from-git` | Build from git source instead of release packages. | `false` |
| `prefix` | Installation prefix. | `/usr` |

### Outputs

| Output | Description |
|--------|-------------|
| `version` | Installed radare2 version |
| `prefix` | Installation prefix path |

### Install latest release

```yaml
steps:
  - uses: Fleet-to-Force/github-actions@v1
```

### Install a specific version

```yaml
steps:
  - uses: Fleet-to-Force/github-actions@v1
    with:
      version: '6.1.2'
```

### Build from source

```yaml
steps:
  - uses: Fleet-to-Force/github-actions@v1
    with:
      version: '6.1.2'
      from-git: true
```

## Zero-trust usage rule

The radare2 installer action is capability, not authority.

Use it only after the fold-in system has resolved or generated:

- computed source-intake provenance
- source CIDs
- license atoms
- plugin/source/capability atoms
- `dm:go` governed CUE shapes
- `dm:rust` governed Nickel contracts
- generated JSON-LD embedded machine authority

The install/build output should later become an observed witness candidate, not semantic authority.

## How the radare2 installer works

The operating system is auto-detected from the runner:

- **Linux** - Downloads and installs `.deb` packages (`radare2` + `radare2-dev`). Architecture is detected via `dpkg --print-architecture`.
- **macOS** - Downloads and installs `.pkg` package. Detects `arm64` vs `x86_64` automatically.
- **Windows** - Downloads and extracts the `.zip` release using PowerShell, handles nested subdirectories automatically, then adds `bin/` to `PATH`.

When `from-git` is `true`, the action clones the radare2 repository and builds from source using `sys/install.sh` on Unix or `meson`/`ninja` on Windows.

## Current hardening direction

Planned next upgrades:

- require pinned action refs in H_Global-Factory workflows
- require pinned radare2 versions or refs before install/build
- emit computed provenance for install/build inputs and outputs
- emit machine-readable witness candidates for CI setup
- split dynamic latest-release resolution behind an explicit denied-by-default profile
- add reusable source-intake computation actions
- add reusable license/capability atom computation actions

## Notes

- Windows git builds use meson/ninja and require a Visual C++ environment. Add `ilammy/msvc-dev-cmd@v1` before this action if building from source on Windows.
- Both `radare2` and `radare2-dev` packages are installed on Linux so headers and pkg-config files are available for building plugins.
- The installer verifies the installation by running `radare2 -v` at the end.
- Use `concurrency` with `cancel-in-progress: true` to avoid wasting CI minutes on superseded pushes.
- Use `fail-fast: false` in matrix builds so a failure on one platform does not cancel the others.
