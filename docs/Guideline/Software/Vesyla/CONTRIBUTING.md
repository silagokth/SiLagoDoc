# Contributing to Vesyla

:+1::tada: First off, thanks for taking the time to contribute! :tada::+1:

## Overview

The following is a set of guidelines for contributing to Vesyla, which are hosted in the [Silago KTH team](https://github.com/silagokth) on GitHub. These are mostly guidelines, not rules. Use your best judgment, and feel free to propose changes to this document in a pull request. This page assumes [familiarity with the Vesyla toolchain](https://silago.eecs.kth.se/docs/ToolChain/Vesyla/).

## GitHub Issues

We provide two templates for reporting GitHub issues: **Bug Report** and **Feature Request**.
Both templates are accessible when [submitting a new issue](https://github.com/silagokth/vesyla/issues/new/choose).

!!! warning
    Before submitting an issue, please make sure to search the existing issues, both open and closed, to see if your issue has already been reported or addressed.

## Contribution Workflow

### 1. Install Vesyla

Before contributing, please install [vesyla](https://github.com/silagokth/vesyla) and --- if necessary --- install [drra-components](https://github.com/silagokth/drra-components) and make sure the env variable `VESYLA_SUITE_PATH_COMPONENTS` points to the built [drra-components](https://github.com/silagokth/drra-components) library.

### 2. Make and test changes

Make the necessary changes and write good tests for them.
Provide some comprehensive way to reproduce your tests.
For which testsuite to use for unit tests, refer to the [corresponding stylguide](../Styleguides/index.md) of the language you use.

Vesyla (and drra-components) integration testing flow relies on running the testcases from [drra-testcase](https://github.com/silagokth/drra-testcase).
Follow the steps described in the [drra-testcase](https://github.com/silagokth/drra-testcase) repo to run the testcases.

### 3. Commit changes and submit a pull request

When committing your changes, keep the included changes to a reasonable size, ideally keeping related changes together.
When writing commit messages, try to follow the [conventional commit](https://www.conventionalcommits.org/en/v1.0.0/) style.

When you are ready to submit your changes, please create a pull request (PR) against the `master` branch of the [Vesyla repository](https://github.com/silagokth/vesyla).
Please respect the PR template and provide a clear description of the changes you made, including any relevant issue numbers.
