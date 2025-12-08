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

    # The path to decisions-file.
    # Optional. Default is '.check-licenses.yml'
    policy_file:
```

### Approving Dependencies

`license_finder` will inform you whenever you have an unapproved dependency.
If your business decides this is an acceptable risk, the easiest way to approve
the dependency is by running `license_finder approvals add`.

For example, let's assume you've added the `awesome_gpl_gem`
to your Gemfile, which `license_finder` reports is unapproved:

```sh
$ license_finder
Dependencies that need approval:
awesome_gpl_gem, 1.0.0, GPL
```

Your business tells you that in this case, it's acceptable to use this
gem. You now run:

```sh
$ license_finder approvals add awesome_gpl_gem
```

If you rerun `license_finder`, you should no longer see
`awesome_gpl_gem` in the output.

To approve specific version

```sh
$ license_finder approvals add awesome_gpl_gem --version=1.0.0
```

To record who approved the dependency and why:

```sh
$ license_finder approvals add awesome_gpl_gem --who CTO --why "Go ahead"
```

This command will create a file `doc/dependency_decisions.yml` with the following contents:
```yaml
---
- - :approve
  - awesome_gpl_gem
  - :who: CTO
    :why: Go ahead
    :versions: []
    :when: 2025-12-08 13:41:02.773057400 Z

```

You can also manually create such a file with a list of approved dependencies.

> [!IMPORTANT]
> By default `license_finder` search policy file by path `./doc/dependency_decisions.yml`. But this action has a
> `policy_file` parameter with which you can specify a different path to the policy file, by default, it is
> `.check-licenses.yml`.

For more info see: [License Finder - README](https://github.com/pivotal/LicenseFinder/tree/master?tab=readme-ov-file#license-finder).

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
```

### Example configurations

This configuration will run `license_finder` for license `MIT` in the root of the repository and will use
`.check-licenses.yml` as a policy file or `./doc/dependency_decisions.yml` if `.check-licenses.yml` don`t exist.
```yaml
...
- name: Check Licenses
  uses: ONLYOFFICE/check-licenses@v1
  with:
    project_license: "MIT"
...
```

This configuration will run `license_finder` for license `MIT` in the root of the repository and will
use `.approved_dependencies.yml` as a policy file or `./doc/dependency_decisions.yml` if `.approved_dependencies.yml`
don`t exist.
```yaml
...
- name: Check Licenses
  uses: ONLYOFFICE/check-licenses@v1
  with:
    project_license: "MIT"
    policy_file: ".approved_dependencies.yml"
...
```

This configuration will run `license_finder` for license `Apache-2.0` in the `./backend` directory of the repository
and will use `./backend/.approved_dependencies.yml` as a policy file or `./backend/doc/dependency_decisions.yml` if
`./backend/.approved_dependencies.yml` don`t exist.
```yaml
...
- name: Check Licenses
  uses: ONLYOFFICE/check-licenses@v1
  with:
    project_license: "Apache-2.0"
    working_directory: "./backend"
    policy_file: ".approved_dependencies.yml"
...
```
This configuration will run `license_finder` for license `Apache-2.0` in the `./backend` directory of the repository
and will use `.approved_dependencies.yml` as a policy file or `./backend/doc/dependency_decisions.yml` if
`.approved_dependencies.yml` don`t exist.
```yaml
...
- name: Check Licenses
  uses: ONLYOFFICE/check-licenses@v1
  with:
    project_license: "Apache-2.0"
    working_directory: "./backend"
    policy_file: "./.approved_dependencies.yml"
...
```
