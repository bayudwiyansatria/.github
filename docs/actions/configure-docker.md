# Configure Docker Action

This action sets up Docker with the specified version and configuration.

## Inputs

| Name             | Type   | Required | Default | Description                      |
| ---------------- | ------ | -------- | ------- | -------------------------------- |
| `docker-version` | string | Yes      | N/A     | The version of Docker to install |

## Outputs

| Name             | Description                     |
| ---------------- | ------------------------------- |
| `docker-version` | The version of Docker installed |

## Example Usage

```yaml
name: Configure Docker

on: [push]

jobs:
  setup-docker:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure Docker
        uses: bayudwiyansatria/.github/actions/configure-docker@master
        with:
          docker-version: "20.10"
```
