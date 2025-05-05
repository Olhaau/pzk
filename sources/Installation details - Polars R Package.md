---
title: Installation details - Polars R Package
---

# Installation details - Polars R Package

url:: https://pola-rs.github.io/r-polars/vignettes/install.html
up: [[sources]]

#source

# Installation details

The `polars` package can be installed from R-universe or GitHub.

Some platforms can install pre-compiled binaries, and others will need to build from source.

## How to install

### From R-universe (recommended)

Installing the latest release version.  

    #__codelineno-0-1Sys.setenv(NOT_CRAN = "true") # Enable installation with pre-built Rust library binary, or enable Rust caching
    #__codelineno-0-2install.packages("polars", repos = "https://community.r-multiverse.org")
* On supported platforms, binary R package will be installed.
* On the other platforms, the pre-built Rust library binary will be downloaded while building the R source package.
* If the pre-built Rust library binary is not available, the Rust library will be built from source (provided that Rust is installed).

### From GitHub releases

Binary packages for the amd64 architecture are available.

#### Linux (x86_64)

    #__codelineno-1-1install.packages(
    #__codelineno-1-2  "https://github.com/pola-rs/r-polars/releases/latest/download/polars__x86_64-pc-linux-gnu.gz",
    #__codelineno-1-3  repos = NULL
    #__codelineno-1-4)
#### Windows (x86_64)

    #__codelineno-2-1install.packages(
    #__codelineno-2-2  "https://github.com/pola-rs/r-polars/releases/latest/download/polars.zip",
    #__codelineno-2-3  repos = NULL
    #__codelineno-2-4)
#### macOS (x86_64, a.k.a. Intel)

    #__codelineno-3-1install.packages(
    #__codelineno-3-2  "https://github.com/pola-rs/r-polars/releases/latest/download/polars__x86_64-apple-darwin20.tgz",
    #__codelineno-3-3  repos = NULL
    #__codelineno-3-4)
#### macOS (aarch64, a.k.a. Apple Silicon)

    #__codelineno-4-1install.packages(
    #__codelineno-4-2  "https://github.com/pola-rs/r-polars/releases/latest/download/polars__aarch64-apple-darwin20.tgz",
    #__codelineno-4-3  repos = NULL
    #__codelineno-4-4)
### From GitHub commits

Installing from GitHub commits or GitHub releases can be done using the `{remotes}` package’s functions.

For example, installing the version 0.9.0 from the GitHub release.  

    #__codelineno-5-1Sys.setenv(NOT_CRAN = "true") # Enable installation with pre-built Rust library binary, or enable Rust caching
    #__codelineno-5-2remotes::install_github("pola-rs/r-polars@v0.9.0")
## Details of installation

### Pre-built Rust library binaries

If one of the following environment variables is set, a pre-built Rust library binary will be tried to be used before building the Rust source.

* `NOT_CRAN="true"`
* `MY_UNIVERSE` is not empty (This environment variable is set by R-universe)
* `LIBR_POLARS_BUILD="false"` (This is prioritized, so if you want to force a source build, set `LIBR_POLARS_BUILD="true"`)

By default, the pre-built Rust library binary is downloaded from the URL recorded in `tools/lib-sums.tsv`. If `tools/lib-sums.tsv` does not exist, or the corresponding URL does not exist, or the hash of the downloaded file does not match the recorded one, it falls back to building from source.

If you want to use a pre-built Rust library binary that exists locally, set the `LIBR_POLARS_PATH` environment variable to the path to the binary. For example (on Bash):  

    #__codelineno-6-1export LIBR_POLARS_BUILD="false"
    #__codelineno-6-2export LIBR_POLARS_PATH="/tmp/libr_polars.a"
    #__codelineno-6-3Rscript -e 'install.packages("polars", repos = "https://community.r-multiverse.org", type = "source")'
### Rust build time options

r-polars Rust library has some [feature flags](https://doc.rust-lang.org/cargo/reference/features.html) that affect the R package features. These flags are set at compile time, and cannot be changed at runtime.

The features enabled in the compiled Rust library are shown by the `polars_info()` function.  

    #__codelineno-7-1library(polars)
    #__codelineno-7-2polars_info()
    #__codelineno-7-3#> Polars R package version : 0.22.4.9000
    #__codelineno-7-4#> Rust Polars crate version: 0.45.1
    #__codelineno-7-5#> 
    #__codelineno-7-6#> Thread pool size: 4 
    #__codelineno-7-7#> 
    #__codelineno-7-8#> Features:                               
    #__codelineno-7-9#> default                    TRUE
    #__codelineno-7-10#> full_features              TRUE
    #__codelineno-7-11#> disable_limit_max_threads  TRUE
    #__codelineno-7-12#> nightly                    TRUE
    #__codelineno-7-13#> sql                        TRUE
    #__codelineno-7-14#> rpolars_debug_print       FALSE
    #__codelineno-7-15#> 
    #__codelineno-7-16#> Code completion: deactivated
At this time, the following environment variables can be used to change the Rust library build time options.

#### Features

The feature flags can be set by the `LIBR_POLARS_FEATURES` environment variable. For example, to enable the `full_features` feature, set the environment variable as like `LIBR_POLARS_FEATURES="full_features"`.

Currently `full_features` would work as a combination of the following features.

* The `default` feature - `sql` for enable `pl$SQLContext()`.
* Features for CRAN compatibility - `disable_limit_max_threads`, this feature disables the automatic limit of the maximum number of threads to 2 for CRAN compatibility, and the maximum number of threads is used by default. See `?pl_thread_pool_size` for details.
* Features for nightly toolchain - `nightly` for nightly toolchain features and SIMD.

Note that nightly features requires the Rust nightly toolchain nightly-2024-11-28.

#### Profile

The profile can be set by the `LIBR_POLARS_PROFILE` environment variable. For example, to build with more optimization, set the environment variable as like `LIBR_POLARS_PROFILE="release-optimized"`.

### Minimum Supported Rust Version (MSRV)

The minimum supported Rust version (MSRV) is declared in the `src/rust/Cargo.toml` file’s `package.rust-version` field.

This MSRV is for the default features. For other features, the nightly Rust toolchain is required.
