# HappyFeet Application Documentation — MkDocs Site

This repository exists for one purpose: to build and serve the **HappyFeet Product Manual** as a documentation site via MkDocs (Material theme).

## Contents

- [`docs/index.md`](./docs/index.md) — the Product Manual (the only page)
- [`mkdocs.yml`](./mkdocs.yml) — site configuration
- [`requirements.txt`](./requirements.txt) — build dependencies (`mkdocs`, `mkdocs-material`)
- Deploy config: [`vercel.json`](./vercel.json) (Vercel) and [`.github/workflows/deploy.yml`](./.github/workflows/deploy.yml) (GitHub Pages)

## Build locally

```bash
pip install -r requirements.txt
mkdocs serve     # preview at http://127.0.0.1:8000
mkdocs build     # output to site/ (gitignored)
```

## Deployment

- **GitHub Pages** — `.github/workflows/deploy.yml` runs `mkdocs gh-deploy` on every push to `main`.
- **Vercel** — builds with `mkdocs build` and serves the generated `site/` directory.

---

*Source documents and earlier domain docs are kept outside this repo, in the sibling `Happyfeet-Archive/` folder.*
