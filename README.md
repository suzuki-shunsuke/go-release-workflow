# go-release-workflow

GitHub Actions Reusable Workflow for Go Application

[workflow](.github/workflows/release.yaml)

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
    uses: suzuki-shunsuke/go-release-workflow/.github/workflows/release.yaml@d5b30f148d2f6fb207c58aee61fab4d3a3021421 # v0.4.5
    with:
      homebrew: true
      aqua_version: v2.21.0
      go-version: 1.19.5
    secrets:
      gh_app_id: ${{ secrets.APP_ID }}
      gh_app_private_key: ${{ secrets.APP_PRIVATE_KEY }}
    permissions:
      contents: write
      id-token: write
      actions: read
      attestations: write
```

.gitignore

```
third_party_licenses
```

## Requirements

- Cosign
- GoReleaser
- [go-licenses](https://github.com/google/go-licenses)

```sh
aqua g -i sigstore/cosign goreleaser/goreleaser google/go-licenses
```

## LICENSE

[MIT](LICENSE)
