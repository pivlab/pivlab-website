# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The Pividori Lab website (pivlab.org), a Jekyll 4 site built on the Greene Lab
[Lab Website Template](https://greene-lab.gitbook.io/lab-website-template-docs) v1.4.0
(see `CITATION.cff`). Deployed to GitHub Pages via GitHub Actions; the `gh-pages` branch is
build output and is never edited by hand.

Most day-to-day edits are content, not code: Markdown pages, YAML in `_data/`, member profiles
in `_members/`, and images. The template's Liquid includes, SCSS, JS, and Ruby plugins are
upstream template code; change them only when the template genuinely needs customizing.

## Commands

There is no test suite. The two things to run are the Jekyll build/serve and the citation
process.

### Serve locally (Docker, recommended, matches CI exactly)

```sh
.docker/run.sh
```

Builds `lab-website-renderer`, runs `_cite/cite.py`, then `jekyll serve` with live reload on
http://localhost:4000. It also re-runs the cite process whenever `_data/sources*`, `orcid*`,
`pubmed*`, or `google-scholar*` files change, and restarts Jekyll when `_config.yaml` changes
(Jekyll hot-reload does not pick up `_config.yaml`).

### Serve locally (native)

```sh
bundle install                      # Ruby 3.1 per CI; Gemfile.lock pins bundler version
bundle exec jekyll serve --livereload
JEKYLL_ENV=production bundle exec jekyll build   # what CI runs; output in _site/
```

`bundle` is not currently installed on this machine, so Docker is the practical path.

### Regenerate citations

```sh
python3 -m pip install -r _cite/requirements.txt   # Python 3.11 in CI
python3 _cite/cite.py                               # run from repo root; writes _data/citations.yaml
```

`_cite/cite.py` must be run from the repo root (it globs `_data/` relative to cwd and imports
`plugins/` relative to the script). Network results are memoized in `_cite/.cache/` (diskcache),
which is committed to git. Manubot results expire after 90 days, PubMed/ORCID after 1 day.
Exit code is 1 if any error occurred, after printing all errors at the end.

The optional Google Scholar plugin needs `GOOGLE_SCHOLAR_API_KEY` (SerpAPI) in the environment
or a `.env` file (gitignored). There is no `_data/google-scholar.yaml` right now, so it is a no-op.

### Link checking

`html-proofer` runs as a Jekyll `post_write` hook (`_plugins/misc.rb`) unless `proofer: false`
in `_config.yaml`. It is currently disabled. Flip it on temporarily to check links after a build.

## Architecture

### Content model

- `index.md`, `research/`, `projects/`, `team/`, `blog/`, `join/`, `contact/`: top-level pages.
  A page appears in the header nav only if its front matter has a `nav:` block with `order`
  (and optionally `tooltip`); `_includes/header.html` sorts by `nav.order`.
- `_members/*.md`: one file per person, `layout: member` by default. Front matter `role` must
  match a key in `_data/types.yaml` for the icon/description to resolve, except `pi`, which the
  team page filters on directly (`role == 'pi'`) even though `types.yaml` only defines
  `principal-investigator`. `group: alum` moves a person to the Alumni section on `team/index.md`;
  each role is listed by its own `list.html` call there, so a new role value needs a new line on
  that page or the person will not appear anywhere.
- `_posts/YYYY-MM-DD-slug.md`: blog posts. `author:` is a comma-separated list of member file
  slugs (filename without `.md`), resolved by `_includes/portrait.html` via `member.slug`.
- `_data/projects.yaml`: project cards. `group: featured` goes in the Featured section;
  entries with no `group` go under More.
- `images/`: `team/` for portraits, `articles/` for citation thumbnails, `projects/` for cards,
  `blog/` for post figures.

### Citation pipeline

Publications on the Research page come from `_data/citations.yaml`, which is **generated; do
not edit it by hand** (it carries a header saying so and CI will overwrite it).

Inputs, processed in this order by `_cite/cite.py` with the matching `_cite/plugins/*.py`:

1. `_data/google-scholar*.yaml` (absent)
2. `_data/pubmed*.yaml`: author search term, expanded to `pubmed:<id>` sources
3. `_data/orcid*.yaml`: ORCID id, expanded to one Manubot-citable id per work (DOI preferred)
4. `_data/sources*.yaml`: hand-written entries keyed by `id`

Sources with the same `id` are merged with later-plugin fields overriding earlier ones, so
`_data/sources.yaml` is where you add images, descriptions, buttons, and `type` to a paper that
PubMed/ORCID already found, or set `remove: true` to drop one (used to hide preprints once the
journal version exists). Every source with an `id` is then run through `manubot cite` to fill in
title/authors/publisher/date/link; fields present in the source override Manubot's. A Manubot
failure is an error for a hand-written source but only a warning for a metasource-derived one.

The Research page pins specific papers with
`{% include citation.html lookup="<id>" style="rich" %}`, where `lookup` matches `citation.id`
exactly or is a substring of the title, then lists everything via `list.html` with a Ruby filter
expression.

### Liquid `filter=` expressions

`_includes/list.html` passes `filter` to the `data_filter` Liquid filter in `_plugins/misc.rb`,
which `eval`s it as **Ruby** with each item's fields bound as local variables (non-alphabetic
characters in field names become `_`). So filters look like `role == 'phd' and group != 'alum'`
or `title =~ /regex/`; undefined fields evaluate to `nil`. This is the main custom mechanism in
the template and is used on the team, projects, and research pages.

### Other custom Ruby plugins

`_plugins/{array,hash,regex,file}.rb` register small Liquid filters (`array_filter`,
`array_carve`, `regex_scan`, `regex_strip`, `file_exists`, `file_read`, etc.) used throughout
`_includes/`. The template disables the Jekyll theme (`theme: null`) and compiles SCSS from
`_styles/` itself.

## CI / deployment

- **Push to `main`** (`on-push.yaml`): runs `update-citations` (regenerates and auto-commits
  `_data/citations.yaml` if changed) and then `build-site`, which builds with
  `JEKYLL_ENV=production` and pushes `_site/` to `gh-pages`.
- **Pull requests** (`on-pull-request.yaml` + `deploy-preview.yaml`): builds citations from the
  PR's `_data/`, commits them to the PR branch if it is in-repo, and deploys a preview to
  `gh-pages` under `preview/pr-<N>/` with a sticky comment linking it. Closing the PR removes the
  preview.
- **Weekly** (`on-schedule.yaml`, Mondays 00:00 UTC): regenerates citations and opens a PR on
  branch `citation-update` if anything changed. Commits titled "Update citations" in history are
  this automation.
- `GOOGLE_SCHOLAR_API_KEY` is a repo secret used only by the citation workflow.

Because CI regenerates `_data/citations.yaml` on every push to `main`, local edits to that file
are pointless; change the inputs in `_data/sources.yaml` or the metasource files instead.
