# Create Changelogs

An action can create changelogs between tags



### Outputs

- `release_type`: `prerelease` or `release`
- `changelogs`: Changelogs

### Example workflow - create changelogs
On every `push` to a tag matching the pattern `v*`, [create a release](https://developer.github.com/v3/repos/releases/#create-a-release):

```yaml
on:
  push:
    # Sequence of patterns matched against refs/tags
    tags:
      - 'v*' # Push events to matching v*, i.e. v1.0, v20.15.10

name: Create Release

jobs:
  build:
    name: Create Release
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      
      - run: |
        git fetch --prune --unshallow

      - name: Create changelogs
        id: changelogs
        uses: heineiuo/create-changelogs@master

      - name: Create Release
        id: create_release
        uses: actions/create-release@latest
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: ${{ github.ref }}
          body: ${{ steps.changelogs.outputs.changelog_text }}
          draft: false
          prerelease: ${{ steps.changelogs.outputs.release_type == 'prerelease' }}


```