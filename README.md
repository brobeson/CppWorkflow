# C++ Workflow

[![Workflow Quality](https://github.com/brobeson/CppWorkflow/actions/workflows/workflow_quality.yaml/badge.svg)](https://github.com/brobeson/CppWorkflow/actions/workflows/workflow_quality.yaml)
![GitHub Release](https://img.shields.io/github/v/release/brobeson/CppWorkflow?logo=github)

This is a reusable workflow to build, test, and lint a C++ project.

> [!WARNING]  
> This workflow is in beta state.
> It is subject to breaking changes.

## Getting Started

To use this workflow, just set it in the `uses` key of a job in your project's workflow file.
Here is an example workflow snippet that calls this workflow:

```yaml
jobs:
  build:
    name: C++ Build
    uses: brobeson/CppWorkflow/.github/workflows/cpp_build.yaml@main
```

## Steps

The workflow runs the following steps.

1. **Configure the Build System**  
   The workflow runs CMake to configure the build system.
1. **Build the Project**  
   The workflow run CMake to invoke the underlying build tool (GCC, MSVC, etc.).
1. **Run Tests**  
   The workflow runs tests via CTest.

## Issue Tracking

[![GitHub Issues or Pull Requests by label](https://img.shields.io/github/issues/brobeson/CppWorkflow/bug?logo=github&label=Bugs)](https://github.com/brobeson/CppWorkflow/issues?q=is%3Aissue+is%3Aopen+label%3Abug)
[![GitHub Issues or Pull Requests by label](https://img.shields.io/github/issues/brobeson/CppWorkflow/enhancement?logo=github&label=Enhancements)](https://github.com/brobeson/CppWorkflow/issues?q=is%3Aissue+is%3Aopen+label%3Aenhancement)
[![GitHub Issues or Pull Requests by label](https://img.shields.io/github/issues/brobeson/CppWorkflow/new%20step?logo=github&label=New%20Steps)](https://github.com/brobeson/CppWorkflow/issues?q=is%3Aopen+is%3Aissue+label%3A%22new+step%22)
[![GitHub milestone details](https://img.shields.io/github/milestones/progress/brobeson/CppWorkflow/1?logo=github)](https://github.com/brobeson/CppWorkflow/milestone/1)

[Report a bug](https://github.com/brobeson/CppWorkflow/issues/new?assignees=brobeson&labels=bug&projects=&template=bug.yaml) |
[Request a new step](https://github.com/brobeson/CppWorkflow/issues/new?assignees=brobeson&labels=new+step&projects=&template=new_step.yaml) |
[Update an existing step](https://github.com/brobeson/CppWorkflow/issues/new?assignees=brobeson&labels=enhancement&projects=&template=enhancement.yaml)
