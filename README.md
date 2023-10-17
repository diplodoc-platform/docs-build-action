# docs-build-action GitHub action

## Inputs

- `revision` (required) - The revision or version identifier for the documentation to be built.
- `project-name` (required) - The documentation's project name, as specified in the configuration file's field with the corresponding name.
- `storage-bucket` (required) - The name of the storage bucket where the built documentation will be stored. After the documentation is successfully built, it will be uploaded to this specified bucket.
- `storage-endpoint` (required) - The endpoint URL of the storage service.
- `storage-access-key-id` (required) - The access key ID associated with the account that has permission to access and upload to the specified storage bucket.
- `storage-secret-access-key` (required) - The secret access key associated with the account.
- `storage-region` (required) - The region where the specified storage bucket is located.
- `src-root` (default: `./`) - The root directory where the source documentation files are located. The action will use this directory as the base location to look for the source files that need to be built.
- `lint-root` (default: `./_docs-lint`) - The root directory for the linting process. This is an optional parameter, and if not specified, the default value will be used.
- `build-root` (default: `./_docs-build`) - The root directory for the built documentation. This is an optional parameter, and if not specified, the default value will be used.

## Example

```yaml
name: Build

on:
  pull_request:

jobs:
  build-docs:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Build
        uses: diplodoc-platform/docs-build-action@v1
        with:
          revision: "pr-${{ github.event.pull_request.number }}"
          project-name: ${{ secrets.DIPLODOC_PROJECT_NAME }}
          src-root: "./docs"
          storage-bucket: ${{ secrets.DIPLODOC_STORAGE_BUCKET }}
          storage-endpoint: ${{ vars.DIPLODOC_STORAGE_ENDPOINT }}
          storage-access-key-id: ${{ secrets.DIPLODOC_ACCESS_KEY_ID }}
          storage-secret-access-key: ${{ secrets.DIPLODOC_SECRET_ACCESS_KEY }}
          storage-region: ${{ vars.DIPLODOC_STORAGE_REGION }}
```
