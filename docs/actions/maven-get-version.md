# Maven Get Version Action

This action retrieves the version of a Maven project from the `pom.xml` file.

## Inputs

| Name             | Type    | Required | Default | Description                        |
| ---------------- | ------- | -------- | ------- | ---------------------------------- |
| `java-version`   | string  | No       | `17`    | The version of Java to use         |
| `configure-java` | string  | No       | `true`  | Wheter to configure the java       |
| `checkout`       | boolean | No       | `true`  | Whether to checkout the repository |

## Outputs

| Name      | Description                      |
| --------- | -------------------------------- |
| `version` | The version of the Maven project |

## Example Usage

```yaml
name: Get Maven Version

on: [push]

jobs:
  get-version:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Get Maven Version
        id: get-version
        uses: bayudwiyansatria/.github/actions/maven-get-version@master
        with:
          pom-file: "pom.xml"

      - name: Display Version
        run: echo "Maven project version is ${{ steps.get-version.outputs.version }}"
```
