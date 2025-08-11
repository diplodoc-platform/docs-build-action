# docs-build-action GitHub action

This GitHub action does two things:
- Builds project documentation using [@diplodoc/cli](https://github.com/diplodoc-platform/cli).
- Uploads the build files, logs, and inputs as artifacts.

## Inputs

- `revision` (default: `default_revision`) - The revision or version identifier for the documentation to be built.
- `src-root` (default: `./`) - The root directory where the source documentation files are located. The action will use this directory as the base location to look for the source files that need to be built.
- `lint-root` (default: `./_docs-lint`) - The root directory for the linting process. This is an optional parameter, and if not specified, the default value will be used.
- `build-root` (default: `./_docs-build`) - The root directory for the built documentation. This is an optional parameter, and if not specified, the default value will be used.
- `cli-version` (default: `latest`) - The @diplodoc/cli version used to build documentation. Can be precise version like `4.58.0` or some dist tag like `latest` or `stable`.

## Usage

Create a file named `.github/workflows/build.yml` in your repo.
This workflow is triggered every time a pull request is opened or updated.

```yaml
name: Build

on:
  pull_request:
    paths: 'docs/**'
    types: [opened, synchronize]

jobs:
  build-docs:
    runs-on: ubuntu-latest
    permissions: write-all
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build
        uses: diplodoc-platform/docs-build-action@v3
        with:
          revision: "pr-${{ github.event.pull_request.number }}"
          src-root: "./docs"
```
