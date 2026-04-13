# jpcortesp.github.io

Static blog scaffold for GitHub Pages.

This repo deploys as a plain static site via GitHub Actions. No local Jekyll installation is required.

## Structure

```text
.
├── .github/
│   └── workflows/
│       └── deploy-pages.yml
├── .nojekyll
├── assets/
│   └── css/
│       └── blog.css
└── posts/
```

No HTML pages are currently included in this repository.

## Local Preview

From the repository root, run a simple static server:

```bash
python -m http.server 8000
```

Then open `http://localhost:8000`.

## GitHub Pages Deployment (No Jekyll)

The workflow at `.github/workflows/deploy-pages.yml` deploys this site on every push to `main`.

### One-time GitHub settings

1. Go to repository **Settings** > **Pages**.
2. Under **Build and deployment**, set **Source** to **GitHub Actions**.
3. Push to `main` (or run the workflow manually from the **Actions** tab).

### Notes

- `.nojekyll` is included to bypass Jekyll processing on GitHub Pages.
- This is a pure static HTML/CSS setup, so your local workflow stays simple.