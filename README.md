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
jobs:
  release:
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@8e0d6d2a7171206b9d95b3b59fe74f8333b1be1b # v0.1.0
    with:
      homebrew: true
      aqua_version: v1.32.3
      go-version: 1.19.5
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
- GoReleaser

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser
```

## LICENSE

[MIT](LICENSE)
