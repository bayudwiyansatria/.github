# Configure Node Action

This action sets up Node.js with the specified version and configuration.

## Inputs

| Name           | Type   | Required | Default | Description                       |
| -------------- | ------ | -------- | ------- | --------------------------------- |
| `node-version` | string | Yes      | N/A     | The version of Node.js to install |

## Outputs

| Name           | Description                      |
| -------------- | -------------------------------- |
| `node-version` | The version of Node.js installed |

## Example Usage

```yaml
name: Configure Node

on: [push]

jobs:
  setup-node:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure Node
        uses: bayudwiyansatria/.github/actions/configure-node@master
        with:
          node-version: "16"
```
