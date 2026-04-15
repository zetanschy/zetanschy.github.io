# zetanschy.github.io

Personal academic site for **Jans Solano**, built with [Jekyll](https://jekyllrb.com/) and the [al-folio](https://github.com/alshedivat/al-folio) theme.

## Local build

```bash
bundle install
bundle exec jekyll serve
```

Then open the URL Jekyll prints (usually `http://127.0.0.1:4000`).

## Deploy

GitHub Pages is configured to build via **GitHub Actions** (`.github/workflows/deploy.yml`). In the repository **Settings → Pages**, set the source to **GitHub Actions** if prompted.

Upstream documentation: [INSTALL.md](INSTALL.md), [CUSTOMIZE.md](CUSTOMIZE.md).
