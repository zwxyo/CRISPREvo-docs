# Documentation checklist

Use this checklist when the web application changes.

## Before editing

- Identify the deployed application version or source commit.
- Test the affected page with a minimal valid input and one invalid input.
- Record any new input limits, defaults, file formats, buttons, and downloads.

## Update the right pages

- User steps and parameter meaning: the matching page under `docs/tools/`.
- Cross-tool hand-off: `docs/workflow.md`.
- File schema or parsing behavior: `docs/reference/input-formats.md`.
- New or changed output: `docs/reference/downloads.md`.
- Size, count, retention, or timeout change: `docs/reference/limits.md`.
- Recoverable failure: `docs/troubleshooting.md`.
- Public tool added or removed: `docs/index.md` and `mkdocs.yml`.

## Verify

1. Run `python -m mkdocs build --strict`.
2. Preview the site on desktop and mobile widths.
3. Follow every changed procedure in the deployed web app.
4. Confirm exact UI labels, defaults, links, and downloaded filenames.
5. Confirm that no unpublished sample IDs, sequence data, local paths, tokens, or API keys appear in the repository.
6. Open a pull request and inspect the Read the Docs preview before merging.

## Release

- Merge the reviewed documentation change to `main`.
- Confirm the `latest` Read the Docs build succeeds.
- For a software release, create/activate the matching documentation tag and verify `stable`.
- Update the web application's Documentation links to the final Read the Docs URL.

