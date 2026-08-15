# Theme provenance

This site used to pull its theme at build time with:

```yaml
remote_theme: YoussefRaafatNasry/portfolYOU
```

That meant every GitHub Pages build downloaded the theme's default branch HEAD, so the
site's appearance depended on a third-party repo that could change, break, or disappear
without any commit here. The theme is also effectively dormant (last code change
Sept 2023, last release v2.3.0 in Aug 2021), so there were no upstream fixes to gain.

The theme is now **vendored** — its files live in this repo and are edited like any
other source.

## What was vendored

Copied verbatim from portfolYOU at commit
[`7a3e795`](https://github.com/YoussefRaafatNasry/portfolYOU/commit/7a3e795d385e2dcd5bcd284f4862e1314efd1336)
(master HEAD, 2024-08-14 — the exact revision the live site was already building against):

- `docs/_layouts/` (4 files)
- `docs/_includes/` (20 files; the pre-existing `_includes/project_assets/` is ours, not the theme's)
- `docs/_sass/` (15 files)

Not copied: the theme's own `docs/` and `test/` directories (its demo site), its
`README`, `LICENSE`, and gemspec.

Already present locally and deliberately left alone:

- `docs/assets/css/style.scss` — identical to the theme's copy
- `docs/assets/js/theme.js` — identical to the theme's copy
- `docs/assets/favicon.ico` — ours, differs from the theme default

## Upstream license

portfolYOU is MIT licensed, © Youssef Raafat Nasry. The vendored files keep that
license; see https://github.com/YoussefRaafatNasry/portfolYOU/blob/master/LICENSE.
The theme's attribution link in `docs/_includes/footer.html` has been left in place.

## Re-syncing with upstream (optional, rarely needed)

```sh
git clone https://github.com/YoussefRaafatNasry/portfolYOU /tmp/portfolYOU
diff -ru /tmp/portfolYOU/_layouts  docs/_layouts
diff -ru /tmp/portfolYOU/_sass     docs/_sass
diff -ru /tmp/portfolYOU/_includes docs/_includes --exclude=project_assets
```

Review the diff and apply by hand whatever you want. Nothing forces you to stay in
sync — that is the point of vendoring.

## Build

GitHub Pages builds this repo with its legacy (classic) Jekyll builder from
`main` → `/docs`. The vendored theme needs no plugins; removing `remote_theme` also
removed the `jekyll-remote-theme` dependency and a network fetch from every build.

## Local preview

Requires **Ruby 3.3** specifically. Ruby 3.4+ dropped `csv`/`base64` from the standard
library and Ruby 3.2+ removed `String#tainted?`, both of which the Jekyll 3.10 /
Liquid 4.0.4 that `github-pages` pins still rely on. Ruby 3.3 is also what GitHub's
own builder runs, so a local build matches production exactly.

```sh
brew install ruby@3.3
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"

cd docs
bundle install                 # installs into docs/vendor/bundle (gitignored)
bundle exec jekyll serve       # http://127.0.0.1:4000
```

Two things differ from production and are expected:

- **CSS is unminified locally.** GitHub Pages compiles Sass with `style: compressed`;
  a local build does not. Same rules, different whitespace.
- **`site.github.public_repositories` is empty without a token**, so the remote project
  card on `/projects/` won't render. Fix with
  `export JEKYLL_GITHUB_TOKEN=$(gh auth token)` before building.
