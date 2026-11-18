# ci-actions

Shared GitHub Actions for Plugin Circuit's C++ CMake projects (the plugin, libplugincircuit, the ports). Modelled on
tanh-lab/ci-actions. Pin a tag (`@v0.1.0`), not `main`.

## Actions

| Action | What |
| --- | --- |
| `setup-build-tools` | Ninja per platform; with `LLVM: "true"` also LLVM + wasm-ld (the test WCLAPs); on Windows the MSVC environment, so CMake can use Ninja (sccache doesn't work with the Visual Studio generator) |
| `cache-deps` | Caches FetchContent sources (`build/_deps/*-src`), keyed on the CMake files that pin them |
| `cmake-build` | Configure with Ninja and sccache, then build (preset or manual mode) |
| `cmake-test` | CTest (preset or manual mode), `EXCLUDE_LABELS` to skip e.g. GUI tests |

## Usage

```yaml
jobs:
  build_test:
    name: ${{ matrix.name }}
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        include:
          - { name: macOS-arm64, os: macos-15 }
          - { name: Windows-x86_64, os: windows-2022 }
    steps:
      - uses: actions/checkout@v4
      - uses: plugin-circuit/ci-actions/setup-build-tools@v0.1.0
      - uses: plugin-circuit/ci-actions/cache-deps@v0.1.0
      - uses: plugin-circuit/ci-actions/cmake-build@v0.1.0
        with:
          BUILD_TYPE: RelWithDebInfo
      - uses: plugin-circuit/ci-actions/cmake-test@v0.1.0
        with:
          EXCLUDE_LABELS: gui
```

## Matrix naming convention

The actions pick the platform from `matrix.name`: `Linux-x86_64`, `macOS-arm64`, `macOS-x86_64`, `Windows-x86_64`,
`Windows-arm64`.
