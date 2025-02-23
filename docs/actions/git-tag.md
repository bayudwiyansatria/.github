# Git Tag Action

This action creates a Git tag with the specified name and configuration.

## Inputs

| Name          | Type   | Required | Default | Description                 |
| ------------- | ------ | -------- | ------- | --------------------------- |
| `tag-name`    | string | Yes      | N/A     | The name of the Git tag     |
| `tag-message` | string | No       | N/A     | The message for the Git tag |
| `commit-sha`  | string | No       | N/A     | The commit SHA to tag       |

## Outputs

| Name       | Description                     |
| ---------- | ------------------------------- |
| `tag-name` | The name of the created Git tag |

## Example Usage

```yaml
name: Create Git Tag

on: [push]

jobs:
  create-tag:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Create Git Tag
        uses: bayudwiyansatria/.github/actions/git-tag@master
        with:
          tag-name: "v1.0.0"
          tag-message: "Release version 1.0.0"
          commit-sha: ${{ github.sha }}
```
