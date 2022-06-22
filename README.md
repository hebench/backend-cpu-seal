# SEAL CPU Reference Backend

## Table of Contents
1. [Requirements](#requirements1)
2. [Build Configuration](#build-configuration)
   1. [Build Type](#build-type)
   2. [Advanced Configuration](#advanced-configuration)
      1. [Third-Party Components](#third-party-components)
      2. [Configuring Pre-Installed API Bridge](#configuring-pre-installed-api-bridge)
      3. [Configuring Pre-Installed SEAL](#configuring-pre-installed-seal)
4. [Building](#building)
5. [Running the Benchmark](#running-the-benchmark)
6. [Contributing](#contributing)

## Requirements <a name="requirements1"></a>
Current build system uses Cmake.

- Ubuntu 16.04/18.04/20.04
- C++17 capable compiler (tested with GCC 9.3)
- CMake 3.12

For a quick guide on getting started with the SEAL CPU Reference Backend, please refer to the [Quickstart Guide](quickstart_guide.md). Otherwise, for a more detailed walkthrough on modifying the build configuration / running this backend, continue below.

## Build Configuration <a name="build-configuration"></a>

By default, the build system will pull the required third-party component versions from remote repos and build locally. Thus, this build system requires Internet access and being able to clone the required third-party repos using `git`.

The majority of warnings generated by third-party libraries are shown by default. If users want to hide these (e.g. to make debugging this project easier), set CMake flag `-DHIDE_EXT_WARNINGS=ON`. This will hide most warnings generated by third-party components built by this build system.

### Build Type <a name="build-type"></a>

If no build type is specified, the build system will build in <b>Debug</b> mode. Use `-DCMAKE_BUILD_TYPE` configuration variable to set your preferred build type:

- `-DCMAKE_BUILD_TYPE=Debug` : debug mode (default if no build type is specified).
- `-DCMAKE_BUILD_TYPE=Release` : release mode. Compiler optimizations for release enabled.
- `-DCMAKE_BUILD_TYPE=RelWithDebInfo` : release mode with debug symbols.
- `-DCMAKE_BUILD_TYPE=MinSizeRel` : release mode optimized for size.

### Advanced Configuration <a name="advanced-configuration"></a>

Users can pull, build and pre-install the required third-party components. This feature is mostly tailored for development, testing, and debugging. Otherwise, using the default behavior is recommended and this section can be skipped.

If no pre-installed version of a component is found, the correct version will be pulled from remote repo and build locally.

The build system will search for pre-installed third-party components in each of the locations below, in order, <b>until one is found</b>:

1. `-D{COMPONENT_NAME}_INCLUDE_DIR` and/or `-D{COMPONENT_NAME}_LIB_DIR` (lib dir is not needed for header only components).
2. `-D{COMPONENT_NAME}_INSTALL_DIR`
3. `/usr/local`

Note that if pre-installed versions are incompatible with the current version of the frontend, the build will fail.

Also note that specific details on how this backend is being configured in cmake can be found inside the top level CMakeLists.txt

See the following sections for the specific configuration variable names for each required third-party component.

#### Third-Party Components <a name="third-party-components"></a>
This backend requires the following third party components:

- [HEBench API Bridge](https://github.com/hebench/api-bridge)
- [Microsoft SEAL HE Library](https://github.com/microsoft/SEAL): SEAL (v3.6.5).

#### Configuring Pre-Installed API Bridge <a name="configuring-pre-installed-api-bridge"></a>
The <b>API Bridge</b> is the component in HEBench that allows communication between Test Harness and backends.

If API Bridge has been pre-built, users can point the build system to the pre-installed version of API Bridge using the following CMake config variables:

`-DAPI_BRIDGE_INCLUDE_DIR`: `include` directory for API Bridge.

`-DAPI_BRIDGE_LIB_DIR`: `lib` directory for API Bridge.

`-DAPI_BRIDGE_INSTALL_DIR`: base directory for API Bridge. Contains both `include` and `lib`. This will be used if any of the previous config variables for API Bridge have not been set.

#### Configuring Pre-Installed SEAL <a name="configuring-pre-installed-seal"></a>
If <b>SEAL</b> has been pre-built, users can point the build system to the pre-installed version using the following CMake config variables:

`-DSEAL_INCLUDE_DIR`: `include` directory for SEAL.

`-DSEAL_LIB_DIR`: `lib` directory for SEAL.

`-DSEAL_INSTALL_DIR`: base directory for SEAL. Contains both `include` and `lib`. This will be used if any of the previous config variables for SEAL have not been set (note: as SEAL creates a separate version-based directory for it's includes, by default users may have to use the above two variables instead of this single variable).

## Building <a name="building"></a>
Build from the top level of `seal-backend` with Cmake as follows:

```bash
# assuming seal-cpu is already cloned
cd ~/seal-cpu
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$INSTALL_LOCATION # change install location at will, and/or specify pre-installed third-party directories here
make -j
make install # install built components
```

The install step will copy the target backend library `libhebench_seal_backend.so` to `$INSTALL_LOCATION/lib`

If <b>not</b> using pre-installed frontend, `make install` will include the frontend components as part of the installation.

## Running the Benchmark <a name="running-the-benchmark"></a>

Once the backend has been built and installed successfully (assuming it has been installed to `$INSTALL_LOCATION`), the frontend <b>Test Harness</b> can run the benchmark as follows:

```bash
test_harness --backend_lib_path $INSTALL_LOCATION/lib/libhebench_seal_backend.so --report_root_path $REPORT_OUTPUT_PATH
```

The Test Harness will save the reports and summary of the run to the path specified in `$REPORT_OUTPUT_PATH`.

## Contributing <a name="contributing"></a>

This project welcomes external contributions. To contribute to HEBench, see [CONTRIBUTING.md](CONTRIBUTING.md). We encourage feedback and suggestions via [Github Issues](https://github.com/hebench/reference-seal-backend/issues) as well as discussion via [Github Discussions](https://github.com/hebench/reference-seal-backend/discussions).
