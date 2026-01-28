# OCD CICD Python Workflow

This is a reusable composite GitHub Action designed to streamline the build, test, and security scanning process for Python applications. It encapsulates common CI/CD steps into a single, easy-to-use action.

## Description

The "Build and Test" action performs the following operations:

1.  **Checkout**: Checks out the repository code.
2.  **Setup Python**: Sets up the Python environment using the version specified in `.python-version`.
3.  **Install Dependencies**: Upgrades `pip` and installs dependencies from `requirements.txt`.
4.  **Secrets Check**: Scans the codebase for hardcoded secrets using `orbitcluster/oc-cicd-secretscanner-workflow` (Runs on `push` and `pull_request`).
5.  **SonarQube Check**: Performs a SonarQube code quality analysis using `orbitcluster/oc-cicd-sonarscan-workflow` (Runs only on `pull_request`).
6.  **SAST Scan**: Performs Static Application Security Testing using `orbitcluster/oc-cicd-sast-workflow` (Runs on `push` and `pull_request`).
7.  **Docker Build**: Builds a Docker image using `orbitcluster/oc-cicd-docker-build-workflow`.
8.  **Docker Push**: Pushes the Docker image to a registry using `orbitcluster/oc-cicd-docker-push-workflow`.

## Inputs

| Input               | Description                                | Required | Default                    |
| :------------------ | :----------------------------------------- | :------- | :------------------------- |
| `app-name`          | Name of the image                          | `false`  | `${{ github.repository }}` |
| `appid`             | Application ID                             | `false`  | `"0"`                      |
| `orgid`             | Organization ID                            | `false`  | `"0"`                      |
| `buid`              | Business Unit ID                           | `false`  | `"0"`                      |
| `test-directory`    | Directory containing tests                 | `false`  | `"test"`                   |
| `sonar-source-path` | Path to the source code for SonarQube scan | `false`  | `"src"`                    |
| `role-to-assume`    | AWS Role to assume                         | `true`   | N/A                        |
| `region`            | AWS Region                                 | `false`  | `"us-east-1"`              |

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
          role-to-assume: "arn:aws:iam::123456789012:role/my-role"
```

## Workflow Steps Details

### 1. Checkout

Uses `actions/checkout@v6` to fetch the source code.

### 2. Set up Python

Uses `actions/setup-python@v5`.

- **Version**: Reads the python version from the `.python-version` file in your repository root.

### 3. Install Dependencies

Executes a bash script to:

- Upgrade `pip`.
- Install requirements from `requirements.txt`.

### 4. Security & Quality Checks

- **Secrets Check**: Triggers on `push` and `pull_request` events to ensure no sensitive data is committed.
- **SonarQube Check**: Triggers only on `pull_request` events to analyze code quality in the `src` directory.
- **SAST Scan**: Triggers on `push` and `pull_request` events for static application security testing.

### 5. Docker Build & Push

- **Docker Build**: Builds the docker image.
- **Docker Push**: Pushes the docker image to the configured registry.
