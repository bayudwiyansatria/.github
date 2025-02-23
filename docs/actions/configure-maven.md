# Configure Maven Action

This action sets up Maven with the specified version and configuration.

## Inputs

| Name            | Type   | Required | Default | Description                     |
| --------------- | ------ | -------- | ------- | ------------------------------- |
| `maven-version` | string | Yes      | N/A     | The version of Maven to install |

## Outputs

| Name            | Description                    |
| --------------- | ------------------------------ |
| `maven-version` | The version of Maven installed |

## Example Usage

```yaml
name: Configure Maven

on: [push]

jobs:
  setup-maven:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure Maven
        uses: bayudwiyansatria/.github/actions/configure-maven@master
        with:
          maven-version: "3.8.1"
```
