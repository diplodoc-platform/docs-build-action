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
- `use-ya-opensource` (default: `false`) - If `true`, do not install `@diplodoc/cli`. Instead download the opensource `ya` bootstrap from yandex/yatool and run the build via `ya tool yfm-docs`. If `ya tool yfm-docs` is not available (e.g. the platform mapping config does not include yfm-docs), the action automatically falls back to installing `@diplodoc/cli` from npm.
- `pre-build-args` (default: `""`) - Additional CLI arguments for an optional pre-build (md2md) step. If non-empty, sources will be transformed via md2md before the main build.
- `use-ya-opensource` (default: `false`) - If `true`, do not install `@diplodoc/cli` from npm. Instead download the opensource `ya` bootstrap from [yandex/yatool](https://github.com/yandex/yatool) and run the build via `ya tool yfm-docs`. If `ya tool yfm-docs` is not available (e.g. the tool is missing from `mapping.conf.json`), the action automatically falls back to installing `@diplodoc/cli` from npm so the build never fails.
- `pre-build-args` (default: `""`) - Additional CLI arguments for an optional pre-build (md2md) step. If non-empty, sources will be transformed via md2md before the main build. Example: `--config=.yfm-dltech --varsPreset=dltech --no-template-vars --no-hash-includes --remove-empty-toc-items`

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
          revision: "${{ github.event.pull_request.head.sha }}"
          src-root: "./docs"
```
