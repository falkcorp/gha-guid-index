<!-- file: README.md -->
<!-- version: 1.0.0 -->
<!-- guid: 1c7e9a2b-4d6f-4813-9a2e-7b5c0d3e6f9a -->
<!-- last-edited: 2026-07-14 -->

# gha-guid-index

Generate a title/path ↔ GUID index from file version headers.

Every file in a falkcorp-standard repo carries a `guid:` line in its
version header (see `file-headers.md` in the shared `.standards`
submodule), but nothing records those GUIDs anywhere — there's no way to
go from a document title back to its GUID. This action scans every
git-tracked file, pulls the header fields out of each, and writes:

- `.github/guid-index.json` — machine-readable, one record per file
- `docs/guid-index.md` — human-readable table, with duplicate/malformed
  GUIDs called out at the top

## Usage (GitHub Action)

```yaml
- uses: actions/checkout@v4
- uses: falkcorp/gha-guid-index@main
```

Fails the step by default if the committed index is stale (`fail-on-drift:
true`). Pair with an auto-commit step, or run it in a PR check and let a
human commit the regenerated files.

### Inputs

| Input | Default | Description |
| --- | --- | --- |
| `working-directory` | `.` | Root directory to scan |
| `json-output` | `.github/guid-index.json` | Path for the JSON index |
| `md-output` | `docs/guid-index.md` | Path for the Markdown index |
| `fail-on-drift` | `true` | Fail the step if the index changed |

### Outputs

| Output | Description |
| --- | --- |
| `duplicate-count` | GUID values shared by more than one file |
| `malformed-count` | `guid:` values that aren't valid UUIDs |

## Usage (pre-commit hook)

```yaml
repos:
  - repo: https://github.com/falkcorp/gha-guid-index
    rev: main
    hooks:
      - id: guid-index
```

Regenerates both output files on every commit; if they change, the hook
fails so you can stage the update and commit again (same pattern as
`prettier`/`end-of-file-fixer`).
