# Read the Docs deployment

The repository is configured for MkDocs Material. Read the Docs reads `.readthedocs.yaml`, creates a Python 3.12 environment, installs `requirements.txt`, and builds `mkdocs.yml` in strict mode.

## 1. Review project metadata

Before the first public build:

1. Replace the web-application URL note in `docs/index.md` with the deployed CRISPREvo URL.
2. Replace the placeholder citation in `docs/citation.md` when the preferred citation is available.
3. Confirm the author, copyright year, repository links, and license.
4. Make sure every currently public tool is represented in `mkdocs.yml` navigation.

## 2. Build locally

From the documentation repository root:

=== "Windows PowerShell"

    ```powershell
    py -m venv .venv
    .\.venv\Scripts\Activate.ps1
    python -m pip install -r requirements.txt
    python -m mkdocs serve
    ```

=== "Linux or macOS"

    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    python -m pip install -r requirements.txt
    python -m mkdocs serve
    ```

Open `http://127.0.0.1:8000/` and check navigation, code blocks, tables, light/dark themes, internal links, and images. Then run:

```bash
python -m mkdocs build --strict
```

The generated `site/` directory is local build output and should not be committed.

## 3. Commit and push to GitHub

```bash
git add .
git commit -m "Add CRISPREvo user documentation"
git push -u origin main
```

The configured repository is `https://github.com/zwxyo/CRISPREvo-docs`.

## 4. Import the project

1. Sign in at [readthedocs.org](https://readthedocs.org/) with GitHub.
2. Install or authorize the Read the Docs GitHub App for `zwxyo/CRISPREvo-docs`.
3. On the Read the Docs dashboard, select **Add project**.
4. Search for and select `CRISPREvo-docs`.
5. Keep `main` as the default branch and choose a unique project name/slug such as `crisprevo` if available.
6. Continue and confirm that `.readthedocs.yaml` exists at the repository root.
7. Open **Builds** and inspect the first build log.

With GitHub App integration, pushes trigger builds automatically and no manual webhook is needed.

## 5. Verify the published site

Check these URLs using the slug selected during import:

```text
https://<project-slug>.readthedocs.io/en/latest/
https://<project-slug>.readthedocs.io/en/latest/quickstart/
https://<project-slug>.readthedocs.io/en/latest/tools/spacermap/
```

Confirm that search works, the version selector shows `latest`, the workflow image loads, and **Edit on GitHub** points to the documentation repository.

## 6. Enable review and version workflows

Read the Docs enables pull-request builds by default for new GitHub-connected projects. Verify this under **Settings → Pull request builds**. Each documentation pull request can then receive a hosted preview before merge.

`latest` follows the default branch. To publish stable documentation, create semantic Git tags such as `v1.0.0`, activate the corresponding version in Read the Docs, and set `stable` as the default when appropriate.

## 7. Diagnose a failed build

| Build stage | Likely cause | Fix |
| --- | --- | --- |
| Configuration validation | Invalid `.readthedocs.yaml` key or indentation | Compare with the Read the Docs v2 configuration reference. |
| Dependency installation | Unavailable or incompatible pinned version | Test `pip install -r requirements.txt` in Python 3.12 and update pins deliberately. |
| MkDocs configuration | Missing page in `nav`, extension, or theme | Run `mkdocs build --strict` locally. |
| Image/link warnings | File path or filename case mismatch | Use paths relative to the current Markdown page and match case exactly. |
| Repository clone | GitHub App cannot access the repository | Re-authorize the app or reconnect the repository in project settings. |

Do not add application runtime dependencies to this repository. Read the Docs builds only the static user guide.

