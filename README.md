# flightlab-zensical-theme

The Bristol Flight Lab theme for [Zensical](https://zensical.org) teaching
sites: it brands the stock `default` and `slate` colour schemes to match the
[Marp slide template](https://github.com/BristolFlightLab/flightlab-marp-template),
so a unit's slides and its site look like the same course.

## Using it

Zensical serves a theme's files from `theme.custom_dir`, which is resolved
relative to the config file. Add this repository as a submodule and point at it:

```bash
git submodule add https://github.com/BristolFlightLab/flightlab-zensical-theme theme
cd theme && git checkout v1.0.0 && cd ..
git add theme && git commit -m "Take the site theme from flightlab-zensical-theme"
```

```toml
extra_css = ["stylesheets/flightlab.css", "stylesheets/course.css"]

[project.theme]
custom_dir = "theme/dist"
```

Everything in `custom_dir` is copied to the site root, which is why it points at
`dist/` rather than the repository root: this repository's own README, licence
and scripts would otherwise be published on your site, and a visitor to
`yoursite/README.md` would get *this* README. Inside `dist/`, `stylesheets/` and
`assets/` land beside the ones from `docs/`. **A file in `docs/` wins over the
same path here**, so a unit can override one file without forking the theme.

> **v1.0.0 put its files at the repository root** and therefore leaked them onto
> the site. Use v2.0.0 or later, and point `custom_dir` at `theme/dist`.

Pin to a tag. The submodule records the exact commit, so the site builds the
same in a year, and upgrading is a deliberate `git -C theme checkout vX.Y.Z`.

### Continuous integration

A submodule is not fetched by a plain checkout:

```yaml
- uses: actions/checkout@v4
  with:
    submodules: true
```

### Two gotchas

- **A filtered or copied build must carry the theme with it.** If your build
  copies `docs/` somewhere else and writes a config there, `custom_dir` is
  resolved against the *new* config's directory, and a bare `theme` will not be
  found. Rewrite it to point back, e.g. `../theme`.
- **`flightlab.css` refers to `../assets/brand/…`**, relative to itself. Keep
  `stylesheets/` and `assets/` as siblings wherever they end up.

## What it does and doesn't cover

Provided: the colour palette, the red header band and its slanted edge, the
University crest in the header, link and admonition colours, and the print
adjustments.

Not provided: a unit's own logo, favicon and nav mark. Keep those in your
`docs/`, set `theme.logo` and `theme.favicon` to them, and — if you want your
own mark beside **Home** in place of the stock plane icon — set one variable:

```css
:root { --fl-nav-mark: url('data:image/svg+xml;utf8,<svg …>'); }
.md-nav__link svg.lucide-plane { display: none; }
```

Set nothing and the stock icon stays.

## The artwork

`dist/assets/brand/` is vendored from
[`flightlab-brand`](https://github.com/BristolFlightLab/flightlab-brand), which
is the single source. `BRAND-VERSION` records the tag it came from. To take a
newer one:

```bash
scripts/sync-brand.sh v1.1.0
```

It is vendored rather than nested as a submodule so that this repository is
usable by any mechanism, including ones that do not fetch submodules.

## Licence

The CSS is MIT; see [LICENSE](LICENSE). The artwork and the visual design are
not — see [BRAND.md](BRAND.md).
