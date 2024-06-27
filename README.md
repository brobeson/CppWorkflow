<!-- cspell:words msvc -->

# C++ Workflows

[![Workflow Quality](https://github.com/brobeson/CppWorkflow/actions/workflows/workflow_quality.yaml/badge.svg)](https://github.com/brobeson/CppWorkflow/actions/workflows/workflow_quality.yaml)
![GitHub Release](https://img.shields.io/github/v/release/brobeson/CppWorkflow?logo=github)

This repository contains three reusable workflows to build and test a C++ project.

- [C++ Build](#c-build)  
  This workflow implements one build & test pipeline.
  It requires inputs, such as the target operating system and the build type, to control the build.
- [Build All The Things](#build-all-the-things)  
  This workflow implements a matrix and runs the [C++ build workflow](#c-build) for each matrix cell.
- [Static Analysis](#static-analysis)  
  This workflow runs static analysis tools on the C++ code.

## C++ Build

### Getting Started

To use the C++ Build workflow, just set it in the `uses` key of a job in your project's workflow file.
Use the `with` key to set the workflow input values.
Here is an example workflow snippet that calls this workflow:

```yaml
jobs:
  build:
    name: C++ Build
    uses: brobeson/CppWorkflow/.github/workflows/build_all_the_things.yaml@v1
    with:
      build-type: Debug
      os: ubuntu-latest
```

### Inputs

- `build-type`
  - **Required** yes
  - **Type** string
  - **Description** This value is set as the `CMAKE_BUILD_TYPE` on the configuration command, the `--config` option on the build command, and the `--build-config` option on the test command.
    This must be a string that your CMake system understands.
- `configure-definitions`
  - **Required** no
  - **Type** string
  - **Description** This is CMake variable definitions to pass to the `cmake` configure command.
    This must be a single string, and must include the `-D` part.
    _Do not use this to set the `CMAKE_BUILD_TYPE`!_
    That is done by the `build-type` input.
  - **Example**
    ```yaml
    with:
      configure-definitions: -D BUILD_TESTING:BOOL=on
    ```
- `os`
  - **Required** yes
  - **Type** string
  - **Description** This is the runner to use for the build job.
    It must be a valid value for the GitHub workflow [`runs-on`](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idruns-on) key.

Here is an example using a build matrix in a calling workflow to control the C++ build inputs:

```yaml
jobs:
  build:
    name: C++ Build
    strategy:
      build-type: [Debug, MinSizeRel, Release, RelWithDebInfo]
      os: [ubuntu-latest, macos-lates]
    uses: brobeson/CppWorkflow/.github/workflows/build_all_the_things.yaml@v1
    with:
      build-type: ${{matrix.build-type}}
      os: ${{matrix.os}}
```

### Steps

The workflow runs the following steps.

1. **Configure the Build System**  
   The workflow runs CMake to configure the build system.
1. **Build the Project**  
   The workflow runs CMake to invoke the underlying build tool (GCC, MSVC, etc.).
1. **Run Tests**  
   The workflow runs tests via CTest.

## Build All The Things

This workflow uses the following [matrix](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstrategymatrix) to run the [build](#c-build) workflow for multiple configurations.
This is an easy way to get all these combinations without reimplementing the matrix in multiple C++ projects.

|                |       Debug        |      Release       |
| :------------- | :----------------: | :----------------: |
| macos-latest   | :white_check_mark: | :white_check_mark: |
| ubuntu-latest  | :white_check_mark: | :white_check_mark: |
| windows-latest | :white_check_mark: | :white_check_mark: |

### Getting Started <!-- markdownlint-disable-line -->

To use the Build All The Things workflow, just set it in the `uses` key of a job in your project's workflow file.
Here is an example workflow snippet that calls this workflow:

```yaml
jobs:
  build:
    name: C++ Build
    uses: brobeson/CppWorkflow/.github/workflows/build_all_the_things.yaml@v1
```

### Inputs <!-- markdownlint-disable-line -->

- `configure-definitions`  
  This is passed through to the [C++ Build](#c-build) workflow.
  See that workflow's [input documentation](#inputs) for details.

### Steps <!-- markdownlint-disable-line -->

This workflow does not implement any steps.
It invokes the [C++ Build](#c-build) workflow for each cell in the matrix described above.
See the [C++ Build steps](#steps) for details.

## Static Analysis

### Getting Started <!-- markdownlint-disable-line -->

To use the C++ static analysis workflow, just set it in the `uses` key of a job in your project's workflow file.
Here is an example workflow snippet that calls this workflow:

```yaml
jobs:
  build:
    name: C++ Code Quality
    uses: brobeson/CppWorkflow/.github/workflows/cpp_static_analysis.yaml@v1
```

### Inputs <!-- markdownlint-disable-line -->

- `configure-definitions`
  - **Required** no
  - **Type** string
  - **Description** This is CMake variable definitions to pass to the `cmake` configure command.
    The C++ static analysis workflow runs CMake to provide a compilation database to tools like clang-tidy.
    This must be a single string, and must include the `-D` part.
    _This workflow will force `CMAKE_EXPORT_COMPILE_COMMANDS` to `on` and `CMAKE_BUILD_TYPE` to `Debug`._
  - **Example**
    ```yaml
    with:
      configure-definitions: -D BUILD_TESTING:BOOL=on
    ```

### Steps <!-- markdownlint-disable-line -->

1. **Check C++ Code Format**  
   This step runs [clang-format](https://clang.llvm.org/docs/ClangFormat.html).
   It does not reformat and commit your code; it just reports files are incorrectly formatted.
1. **Run clang-tidy**  
   This step runs [clang-tidy](https://clang.llvm.org/extra/clang-tidy/) on your project.
   It configures a debug build and builds the software.
   The CMake configuration provides the _compile_commands.json_ database required by clang-tidy.
   The build ensures that clang can find any build-generated files, such as C++ protocol buffers.

## Issue Tracking

[![GitHub Issues or Pull Requests by label](https://img.shields.io/github/issues/brobeson/CppWorkflow/bug?logo=github&label=Bugs)](https://github.com/brobeson/CppWorkflow/issues?q=is%3Aissue+is%3Aopen+label%3Abug)
[![GitHub Issues or Pull Requests by label](https://img.shields.io/github/issues/brobeson/CppWorkflow/enhancement?logo=github&label=Enhancements)](https://github.com/brobeson/CppWorkflow/issues?q=is%3Aissue+is%3Aopen+label%3Aenhancement)
[![GitHub Issues or Pull Requests by label](https://img.shields.io/github/issues/brobeson/CppWorkflow/new%20step?logo=github&label=New%20Steps)](https://github.com/brobeson/CppWorkflow/issues?q=is%3Aopen+is%3Aissue+label%3A%22new+step%22)
[![GitHub milestone details](https://img.shields.io/github/milestones/progress/brobeson/CppWorkflow/1?logo=github)](https://github.com/brobeson/CppWorkflow/milestone/1)

[Report a bug](https://github.com/brobeson/CppWorkflow/issues/new?assignees=brobeson&labels=bug&projects=&template=bug.yaml) |
[Request a new step](https://github.com/brobeson/CppWorkflow/issues/new?assignees=brobeson&labels=new+step&projects=&template=new_step.yaml) |
[Update an existing step](https://github.com/brobeson/CppWorkflow/issues/new?assignees=brobeson&labels=enhancement&projects=&template=enhancement.yaml)
