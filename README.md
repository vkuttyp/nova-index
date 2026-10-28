# The Nova package index

The registry of [Nova](https://vkuttyp.github.io/Nova/) packages (`docs/tooling.md` §4.6–§4.8, ADR 0017). Each package
has a file `packages/<name>.toml`, with one entry per published version:

```toml
[[version]]
version = "1.4.0"
git = "https://github.com/someone/geometry.git"
commit = "3f2a9c0d…"                      # the full commit id
hash = "sha256:…"                         # of the commit's manifest and sources
dependencies = { shapes = "2.1" }         # its registry requirements
```

The code itself stays in each package's own repository: an entry only names a commit and the hash of what it holds.
`novac fetch` reads this index, chooses versions, and records each commit and hash in the user's `nova.lock`.

## Publishing a version

1. In the package: commit and push the version (its `nova.toml` has `version = "…"` and `kind = "library"`, and
   every dependency is a registry dependency), then run `novac publish`. It checks the package and prints the entry.
2. Append the entry to `packages/<name>.toml` here (create the file for a new package), in a pull request.

Entries are only ever appended. To withdraw a version, set `yanked = true` on it: locks that already use it keep
working, and no new resolution chooses it.

## Review

The `check` workflow runs `novac index-check` on every pull request. It fetches each entry's commit, and checks:

- the commit's manifest has the entry's name and version;
- its hash and its registry dependencies are the entry's;
- each package's versions increase down the file.

A new package name is reviewed by a maintainer before it is merged. A later version from the package's repository
merges once the check passes.
