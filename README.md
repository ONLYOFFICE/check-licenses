# Check Licenses

A GitHub Action that checks project dependencies for license compliance.

This Action uses [`license_finder`](https://github.com/pivotal/LicenseFinder) to analyze dependency licenses and verify that they align with your project’s license policy.

## Usage

### Inputs

```yaml
- uses: ONLYOFFICE/check-lincenses@v1
  with:
    # The project license to check against.
    # Required.
    project_license: 

    # The working directory where dependencies will be analyzed.
    # Optional. Default is '.'
    working_directory:
```

## Examples

### Check licenses in Node.js project

```yaml
name: License Compliance

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  check-licenses:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install NodeJS
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install Dependencies
        run: npm install

      - name: Check Licenses
        uses: ONLYOFFICE/check-licenses@v1
        with:
          project_license: "Apache-2.0"
          working_directory: "./frontend"
```

### Check licenses in Java project

```yaml
name: License Compliance

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  check-licenses:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install Java 8
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '8'

      - name: Check Licenses
        uses: ONLYOFFICE/check-licenses@v1
        with:
          project_license: "MIT"
          working_directory: "./backend"
```

> [!IMPORTANT]
> If you want to add exceptions to the standard license policies, add them to the file doc/dependency_decisions.yml.