# OCD CICD Python Workflow

This is a reusable composite GitHub Action designed to streamline the build, test, and security scanning process for Python applications. It encapsulates common CI/CD steps into a single, easy-to-use action.

## Description

The "Build and Test" action performs the following operations:

1.  **Checkout**: Checks out the repository code.
2.  **Setup Python**: Sets up the Python environment using the version specified in `.python-version`.
3.  **Install Dependencies**: Upgrades `pip` and installs dependencies from `requirements.txt`.
4.  **Secrets Check**: Scans the codebase for hardcoded secrets using `orbitcluster/oc-cicd-secretscanner-workflow` (Runs on `push` and `pull_request`).
5.  **SonarQube Check**: Performs a SonarQube code quality analysis using `orbitcluster/oc-cicd-sonarscan-workflow` (Runs only on `pull_request`).

## Inputs

| Input            | Description                | Required | Default |
| :--------------- | :------------------------- | :------- | :------ |
| `test-directory` | Directory containing tests | `true`   | `test`  |

## Usage

To use this action in your workflow, map the `uses` keyword to the location of this action.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: orbitcluster/oc-cicd-python-workflow@v1
        with:
          test-directory: "tests"
```

## Workflow Steps Details

### 1. Checkout

Uses `actions/checkout@v6` to fetch the source code.

### 2. Set up Python

Uses `actions/setup-python@v4`.

- **Version**: Reads the python version from the `.python-version` file in your repository root.

### 3. Install Dependencies

Executes a bash script to:

- Upgrade `pip`.
- Install requirements from `requirements.txt`.

### 4. Security & Quality Checks

- **Secrets Check**: Triggers on `push` and `pull_request` events to ensure no sensitive data is committed.
- **SonarQube Check**: Triggers only on `pull_request` events to analyze code quality in the `src` directory.
