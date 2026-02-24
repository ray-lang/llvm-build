# LLVM Build Workflows

This repository hosts GitHub Actions workflows and composite actions that build, package, and publish precompiled LLVM bundles for Ray's CI needs across all supported platforms. Moving these into their own repo keeps the main Ray project lighter while allowing LLVM artifacts to be versioned independently.

## Supported platforms

| Target | Runner | Asset |
|---|---|---|
| linux-x86_64 | `ubuntu-24.04` | `linux-x86_64-llvm-21.1.8.zip` |
| linux-arm64 | `ubuntu-24.04-arm` | `linux-arm64-llvm-21.1.8.zip` |
| macos-arm64 | `macos-15` | `macos-arm64-llvm-21.1.8.zip` |
| macos-x86_64 | `macos-15-intel` | `macos-x86_64-llvm-21.1.8.zip` |
| windows-x64 | `windows-2025` | `windows-x64-llvm-21.1.8.zip` |
| windows-arm64 | `windows-11-arm` | `windows-arm64-llvm-21.1.8.zip` |

All artifacts are published to a single GitHub release tagged `llvm-21.1.8`.

## Repository structure

```
.github/
  actions/
    linux-llvm-build/action.yml      # Composite action for Linux builds
    macos-llvm-build/action.yml      # Composite action for macOS builds
    windows-llvm-build/action.yml    # Composite action for Windows builds
  workflows/
    build-llvm-linux.yml             # Linux x86_64 + arm64
    build-llvm-macos.yml             # macOS arm64 + x86_64
    build-llvm-windows.yml           # Windows x64 + arm64
```

## What it does

Each workflow downloads the LLVM 21.1.8 source tarball, configures CMake with Ninja (enabling `lld`, all targets, release mode, static libs, no tests/examples/benchmarks), builds, installs, zips the install tree, and uploads the archive as a GitHub release asset.

Builds are cached per-platform per-architecture via `actions/cache` so subsequent workflow runs skip the build when the cache is present.

## Triggering a build

1. Go to **Actions** in this repository.
2. Select the workflow for the platform you want to build (Linux, macOS, or Windows).
3. Click **Run workflow** to launch a manual (`workflow_dispatch`) build.
4. When the jobs finish, check the repository's **Releases** page for the updated artifacts under the `llvm-21.1.8` tag.

## Customization tips

- Override LLVM version, target arch, install prefix, or asset naming by editing the workflow files or by reusing the composite actions from another workflow.
- Each composite action accepts `llvm-version`, architecture, `install-prefix`, and release metadata inputs documented in its `action.yml`.
