# LLVM Windows Build Workflow

This repository hosts the GitHub Actions workflow and composite action that build, package, and publish precompiled LLVM bundles for Ray's Windows CI needs. Moving it into its own repo keeps the main Ray project lighter while allowing Windows LLVM artifacts to be versioned independently.

## What it does
- `Build Windows LLVM` workflow (`.github/workflows/build-llvm-windows.yml`) builds LLVM 21.1.8 for both `x64` and `arm64` on dedicated Windows runners.
- The composite action at `.github/actions/windows-llvm-build` downloads the LLVM sources, configures CMake (Ninja generator, no tests/examples/benchmarks), installs to `C:\llvm-<arch>`, zips the install tree, and uploads the archive as a GitHub release asset.
- Builds are cached via `actions/cache` so subsequent workflow runs only rebuild when the cache is missing.

## Triggering a build
1. Go to **Actions › Build Windows LLVM** in this repository.
2. Click **Run workflow** to launch a manual (`workflow_dispatch`) build.
3. When the job finishes, check the repository’s Releases page for the updated artifacts (e.g. `windows-x64-llvm-21.1.8.zip`).

## Customization tips
- Override LLVM version, target arch, install prefix, or asset naming by editing `.github/workflows/build-llvm-windows.yml` or by reusing the composite action from another workflow.
- The composite action accepts `llvm-version`, `msvc-arch`, `install-prefix`, and release metadata inputs documented in `.github/actions/windows-llvm-build/action.yml`.
