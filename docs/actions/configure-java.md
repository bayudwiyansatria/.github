# Configure Java Action

This action sets up Java with the specified version and configuration.

## Inputs

| Name           | Type   | Required | Default | Description                    |
| -------------- | ------ | -------- | ------- | ------------------------------ |
| `java-version` | string | Yes      | N/A     | The version of Java to install |

## Outputs

| Name           | Description                   |
| -------------- | ----------------------------- |
| `java-version` | The version of Java installed |

## Example Usage

```yaml
name: Configure Java

on: [push]

jobs:
  setup-java:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure Java
        uses: bayudwiyansatria/.github/actions/configure-java@master
        with:
          java-version: "11"
```
