# go-release-workflow

GitHub Actions Reusable Workflow for Go Application

## How to use

```yaml
---
name: Release
on:
  push:
    tags: [v*]
permissions: {}
env:
  AQUA_POLICY_CONFIG: ${{ github.workspace }}/aqua-policy.yaml
jobs:
  release:
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@main
    with:
      homebrew: true
    secrets:
      gh_app_id: ${{ secrets.APP_ID }}
      gh_app_private_key: ${{ secrets.APP_PRIVATE_KEY }}
    permissions:
      contents: write
      id-token: write
      actions: read
```

## Requirements

- Cosign

```sh
aqua g -i sigstore/cosign
```

## LICENSE

[MIT](LICENSE)
