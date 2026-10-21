# zetanschy.github.io

Personal academic site of Jans Solano (robotics engineer at Robot.com; previously research at Yale, MPI-IS, Bosch AI; B.Sc. Mechatronics, PUCP). Built on the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme and served at https://zetanschy.github.io.

## Deploying

Pushing to `master` is the deploy. `.github/workflows/deploy.yml` runs `JEKYLL_ENV=production bundle exec jekyll build`, purges unused CSS, and publishes `_site/` to the `gh-pages` branch, which GitHub Pages serves. The live site updates a few minutes after the push.

1. **Preview locally and show the user before pushing** (see below). They review content changes before anything goes live.
2. Make the Prettier check pass. `.github/workflows/prettier.yml` runs `npx prettier . --check` on every push and emails the user when it fails, so run the same check across the whole repo, not just your files. CI installs Prettier from `package-lock.json`, so the local `node_modules` gives the same result.
   ```bash
   npx prettier . --check
   npx prettier --write <files it lists>
   ```
   Don't reformat generated HTML (such as eval reports) or the TOC-managed al-folio docs (`CUSTOMIZE.md`, `INSTALL.md`, …). The TOC bot rewrites them in a style Prettier rejects, so a reformat would just fail again on the bot's next commit. Add such paths to `.prettierignore` instead.
3. Stage specific files (never `git add -A`), then commit with a short lowercase subject such as `add yale research` or `update cv`, no body:
   ```bash
   git add <files>
   git commit -m "add yale research" -m "Co-Authored-By: Claude <noreply@anthropic.com>"
   ```
4. Push. GitHub Actions also commits to `master` (Google Scholar citations on Mon/Wed/Fri, markdown TOC updates), so rebase first if behind:
   ```bash
   git pull --rebase origin master && git push origin master
   ```

The `gh` CLI is not installed. Check workflow results after a push through the public API (or https://github.com/zetanschy/zetanschy.github.io/actions):

```bash
curl -s "https://api.github.com/repos/zetanschy/zetanschy.github.io/actions/runs?head_sha=$(git rev-parse HEAD)" \
  | python3 -c "import sys,json; [print(r['name'], r['status'], r['conclusion']) for r in json.load(sys.stdin)['workflow_runs']]"
```

## Local preview

Port 8080 is usually held by another process on this machine, so plain `docker compose up` fails with `address already in use`. Run on 8091 instead:

```bash
docker compose pull                                              # first time only
docker compose run --rm --name folio-preview -p 8091:8080 jekyll  # run in background
```

The site is ready when the log prints `Server running`, at http://localhost:8091. It regenerates on every file save, which takes about 10 s; editing `_config.yml` restarts Jekyll. Stop it with `docker stop folio-preview`.

To show the user a screenshot:

```bash
google-chrome --headless=new --hide-scrollbars --window-size=1300,2600 \
  --virtual-time-budget=6000 --screenshot=/path/to/out.png http://localhost:8091/
```

## Where content lives

| What                              | File                                                                                                                                           |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| About page bio                    | `_pages/about.md`                                                                                                                              |
| "Research" list on the about page | `_bibliography/papers.bib` entries with `selected = {true}`, in file order. All entries also appear on `/publications/`, grouped by year.      |
| Publication thumbnails            | `assets/img/publication_preview/` (`preview` field), with optional hover video (`video` field)                                                 |
| Projects                          | `_projects/*.md`, ordered by the number prefix and `importance`                                                                                |
| Teaching                          | `_pages/teaching.md`, `_teachings/`                                                                                                            |
| CV                                | `/cv/` only links `assets/pdf/cv.pdf`; update the CV by replacing that PDF. The RenderCV workflow writes to `assets/rendercv/`, not this file. |
| Other PDFs                        | `assets/pdf/`: `transcripts.pdf`, `poster.pdf` (Yale REPU Symposium), `poster_ai_yale.pdf` (AI at Yale Symposium)                              |
| Site settings                     | `_config.yml`: favicon emoji (`icon`), name highlighting (`scholar.last_name`/`first_name`), `max_author_limit`                                |
| Nav bar                           | Pages with `nav: true`: Publications (1), Projects (2), Teaching (3), CV (4)                                                                   |

Standalone pages served as-is:

- `evals-agent101/`: static HTML eval reports for the SO-ARM101 pick-and-place agent.
- `_pages/love_u_vane.html`: a personal hidden page (`nav: false`). Leave it alone unless asked.

## Changes from upstream al-folio

The al-folio docs referenced below describe upstream behavior. These parts differ here:

- `_layouts/bib.liquid` renders at most five buttons: Abs, one PDF link (`arxiv`, else `html`, else `doi`), Website, and up to two posters (`poster`/`poster2`, labelled by `poster_label`/`poster2_label`, as a file in `assets/pdf/` or a full URL). Upstream fields such as `code`, `slides`, `supp` and `blog` are not rendered.
- Write the user as `Solano, J.` in bib author lists so they get highlighted. Name prefixes (`van Dijk, D.`) render correctly.
- The about page section heading is "Research" (`_layouts/about.liquid`), not "Publications".

## al-folio reference

@AGENTS.md
