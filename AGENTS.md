# AOCL-Sparse Agent Guide

**Project:** AMD Optimizing CPU Libraries (AOCL) — Sparse linear-algebra routines
**Build system:** CMake 3.22+
**Languages:** C/C++
**License:** MIT

## Overview

AOCL-Sparse provides sparse Basic Linear Algebra Subroutines (BLAS) optimized for AMD CPUs. It depends on AOCL-BLAS, AOCL-LAPACK, and AOCL-Utils. This repo is a CMake project with `library/`, `tests/`, and `tools/` directories.

## Repository Layout

```
.
├── CMakeLists.txt        # Top-level build configuration
├── CMakePresets.json     # CMake presets for common configurations
├── library/              # Core library source and public headers
│   ├── include/          # Public C/C++ headers (aoclsparse*.h)
│   └── src/              # Library implementation (analysis, conversion, level1/2/3, solvers)
├── tests/                # Samples, unit tests, and benchmarks
│   ├── examples/         # Sample programs (also installed as examples)
│   ├── unit_tests/       # STP and unit tests
│   └── benchmarks/       # Performance benchmarks
├── tools/                # Auxiliary tools
├── cmake/                # CMake modules and presets
└── docs/                 # Sphinx/Doxygen documentation source
```

## Build Commands

### Linux (default)

Requires AOCL-BLAS, AOCL-LAPACK, and AOCL-Utils installed, and `AOCL_ROOT` set.

```bash
export AOCL_ROOT=/opt/aocl

cmake -S . -B build -DCMAKE_INSTALL_PREFIX=/opt/aoclsparse \
  -DBUILD_UNIT_TESTS=ON -DBUILD_CLIENTS_BENCHMARKS=ON

cmake --build build --config Release --target install
```

### Non-AOCL_ROOT mode

```bash
cmake -S . -B build -DCMAKE_INSTALL_PREFIX=/opt/aoclsparse \
  -DAOCL_BLIS_LIB=/path/to/libblis-mt.so \
  -DAOCL_LIBFLAME=/path/to/libflame.so \
  -DAOCL_UTILS_LIB=/path/to/libaoclutils.so \
  -DAOCL_BLIS_INCLUDE_DIR=/path/to/include \
  -DAOCL_LIBFLAME_INCLUDE_DIR=/path/to/include \
  -DAOCL_UTILS_INCLUDE_DIR=/path/to/include/alci \
  -DBUILD_SHARED_LIBS=ON -DBUILD_ILP64=ON -DSUPPORT_OMP=ON

cmake --build build --config Release --target install
```

## Test Commands

Unit tests and STP are run from the build directory:

```bash
cmake --build build --target test
# or
cd build && ctest -V -R "<pattern>"
```

## Key Build Options

| Option | Default | Description |
|--------|---------|-------------|
| `BUILD_SHARED_LIBS` | ON | Build shared library |
| `BUILD_ILP64` | OFF | ILP64 index support |
| `SUPPORT_OMP` | ON | OpenMP multi-threading |
| `BUILD_CLIENTS_SAMPLES` | ON | Build example programs |
| `BUILD_UNIT_TESTS` | OFF | Build unit tests |
| `BUILD_CLIENTS_BENCHMARKS` | OFF | Build benchmarks (requires Boost 1.80-1.85) |
| `BUILD_DOCS` | OFF | Build Sphinx/Doxygen PDF docs |
| `USE_AVX512` | ON | AVX-512 instruction support |
| `COVERAGE` | OFF | GCC coverage (Debug only) |
| `ASAN` | OFF | AddressSanitizer (Debug/Linux only) |
| `VALGRIND` | OFF | Valgrind instrumentation (Debug only) |

## Documentation

- `docs/index.rst` — Sphinx documentation source
- `docs/AOCL-Sparse_API_Guide.pdf` — API guide
- Build docs with `-DBUILD_DOCS=ON`

## Conventions

- Public headers use `aoclsparse_` prefix.
- Source under `library/src/` is organized by BLAS level and solver type.
- Tests use CTest; benchmarks require Boost.
- Default install prefix on Linux is `/opt/aoclsparse/`.

## Common Gotchas

- `AOCL_ROOT` must point to a unified AOCL install containing `amd-blis`, `amd-libflame`, and `amd-utils`.
- CMake 3.22 through 3.29 is required.
- Boost 1.80 through 1.85 is required when `BUILD_CLIENTS_BENCHMARKS=ON`.
- Benchmarks and examples need the library path exported after install.
- ILP64 builds require matching ILP64 AOCL libraries.
