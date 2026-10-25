# CRISPREvo documentation

This repository contains the user documentation for the CRISPREvo web platform. The site is built with MkDocs Material and is ready to publish on Read the Docs.

## Preview locally

```bash
python -m venv .venv
```

Activate the environment, then run:

```bash
python -m pip install -r requirements.txt
python -m mkdocs serve
```

Open `https://crisprevo.com/` in a browser. Run a strict production build before committing:

```bash
python -m mkdocs build --strict
```

## Publish on Read the Docs

1. Push this repository to `https://github.com/zwxyo/CRISPREvo-docs`.
2. Sign in to Read the Docs with GitHub and install the Read the Docs GitHub App for this repository.
3. Choose **Add project**, select `CRISPREvo-docs`, keep `main` as the default branch, and continue.
4. Confirm that `.readthedocs.yaml` exists at the repository root.
5. Open the first build log and verify that `mkdocs build` succeeds.
6. Add the published documentation URL and the deployed CRISPREvo web-app URL to `docs/index.md`.

See `docs/maintainers/read-the-docs.md` for the full release and versioning workflow.
