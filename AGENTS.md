# AGENTS.md

## Cursor Cloud specific instructions

OPALX is a C++20 MPI-parallel particle accelerator simulation framework. It is **not** a web service — it is a compiled scientific simulation tool that produces the `opalx` CLI executable.

### Building

Build follows standard CMake workflow. For CPU-only (Serial) development build with unit tests:

```bash
mkdir -p build_serial && cd build_serial
cmake .. -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ \
    -DBUILD_TYPE=Debug -DPLATFORMS=SERIAL \
    -DOPALX_ENABLE_UNIT_TESTS=ON -DOPALX_ENABLE_TESTS=ON
make -j$(nproc)
```

**Gotcha**: The default system compiler may be Clang, which can fail to link due to missing `-lstdc++`. Always explicitly set `-DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++` to use GCC.

The build fetches most dependencies automatically via CMake FetchContent (IPPL, HDF5, H5hut, GoogleTest, Kokkos, HeFFTe). Only MPI, ZLIB, and basic build tools need to be pre-installed.

Initial CMake configure + full build takes ~4 minutes. Subsequent incremental builds are fast.

### Running tests

- **Unit tests**: `cd build_serial && ctest --output-on-failure -j $(nproc)` (31 tests, ~5 seconds)
- **Regression tests**: Clone `https://github.com/rammann/regression-tests-x` and `https://github.com/rammann/NightlyBuildX`, then use the runner scripts. Example for a single test:
  ```bash
  bash /path/to/NightlyBuildX/scripts/run_only_input \
    --build /workspace/build_serial \
    --input /path/to/regression-tests-x \
    Drift-open
  ```
  For full suite with HTML report: `bash /path/to/NightlyBuildX/scripts/run_tests --build /workspace/build_serial --tests /path/to/regression-tests-x`

### Linting

Code style uses `clang-format` with the `.clang-format` config at repo root:
```bash
clang-format --dry-run --Werror src/SomeFile.cpp
```

### Running the application

The `opalx` executable reads `.in` simulation input files:
```bash
mpirun -np 1 /workspace/build_serial/src/opalx MySimulation.in --info 5
```
Use `--help` for CLI options. Use `--version-full` for build details.

### Key paths

- Executable: `build_serial/src/opalx`
- Unit tests: `build_serial/unit_tests/`
- Source: `src/`
- CMake options reference: see `README.md` and `cmake/OPALXOptions.cmake`
