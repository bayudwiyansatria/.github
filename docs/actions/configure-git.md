# Configure Git Action

This action sets up Git with the specified configuration.

## Inputs

| Name         | Type   | Required | Default | Description                     |
| ------------ | ------ | -------- | ------- | ------------------------------- |
| `user-name`  | string | Yes      | N/A     | The Git user name               |
| `user-email` | string | Yes      | N/A     | The Git user email              |
| `gpg-key`    | string | No       | N/A     | The GPG key for signing commits |

## Outputs

| Name         | Description                   |
| ------------ | ----------------------------- |
| `user-name`  | The configured Git user name  |
| `user-email` | The configured Git user email |

## Example Usage

```yaml
name: Configure Git

on: [push]

jobs:
  setup-git:
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure Git
        uses: bayudwiyansatria/.github/actions/configure-git@master
        with:
          user-name: "Your Name"
          user-email: "your.email@example.com"
          gpg-key: ${{ secrets.GPG_KEY }}
```
