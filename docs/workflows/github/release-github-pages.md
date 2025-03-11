# Release GitHub Pages Workflow

This workflow is designed to automate the deployment of a static site to GitHub Pages.

## Workflow Configuration

The workflow is triggered on push events to the main branch or a specific branch used for GitHub Pages.

### Inputs

| Name                | Type   | Required | Default         | Description                                    |
| ------------------- | ------ | -------- | --------------- | ---------------------------------------------- |
| `build-command`     | string | Yes      | `npm run build` | The command to build the static site           |
| `publish-directory` | string | Yes      | `dist`          | The directory containing the built static site |

## Secrets

| Name           | Required | Description                                          |
| -------------- | -------- | ---------------------------------------------------- |
| `GITHUB_TOKEN` | Yes      | The GitHub token to authenticate with the repository |

## Jobs

### Deploy to GitHub Pages

This job handles the process of building and deploying the static site to GitHub Pages.

#### Steps

1. **Checkout Repository**:
   - Uses `actions/checkout@v3` to checkout the repository.
2. **Setup Node.js**:
   - Sets up Node.js using `actions/setup-node@v3`.
3. **Install Dependencies**:
   - Installs the project dependencies.
4. **Build Static Site**:
   - Runs the build command to generate the static site.
5. **Deploy to GitHub Pages**:
   - Deploys the built static site to GitHub Pages.

### Matrix Strategy

- Runs on the `ubuntu-24.04` operating system.

## Example Usage

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    name: Deploy to GitHub Pages
    runs-on: ubuntu-24.04
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "16"

      - name: Install Dependencies
        run: npm install

      - name: Build Static Site
        run: ${{ inputs.build-command }}

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ${{ inputs.publish-directory }}
```
