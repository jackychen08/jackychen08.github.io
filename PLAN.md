# Plan: Personal Academic Website on GitHub Pages

A personal academic website hosted on GitHub Pages, modeled on
[iandunn.io/publications](https://www.iandunn.io/publications#year-2025). It has a
short homepage and a **Publications** page that groups papers by year. Each year
has its own anchor, so links like `/publications#year-2025` jump straight to that year.

---

## 1. The format we are matching

| Element | Reference site | Our site |
|---|---|---|
| Homepage | Name, role/affiliation, research summary (page title `Homepage - <Name>`) | Same: photo, name, title, affiliation, 1–2 paragraph bio, contact/profile links |
| Publications page | Separate page at `/publications` | `/publications` |
| Grouping | Sections by year, newest first | Same, generated from a data file |
| Year anchors | `#year-2025` style fragment IDs | `id="year-YYYY"` on every year section, plus a year jump bar at the top |
| Entry contents | Title, authors, venue, links | Title · authors (own name in bold) · venue + year · optional note (Oral, Spotlight, etc.) · link buttons `[Paper] [arXiv] [Code] [BibTeX]` |
| Domain | Custom domain | `jackychen08.github.io` to start, with a custom domain as an option (§3) |

> **Caveat:** The build environment blocked the reference site, so the
> structure above comes from its URL scheme, its public search listing, and
> standard academic site layouts. Before building, check these details against the live page:
> paper thumbnails, abstract toggles, venue badges, the nav items, and the footer.

---

## 2. Tech stack

**Jekyll, built natively by GitHub Pages, with no third-party theme.**

- GitHub Pages builds Jekyll on push. We don't need a CI workflow or a build step to maintain.
- All publications live in one YAML file (`_data/publications.yml`). Adding a
  paper means appending one entry. The year grouping and anchors are generated automatically.
- The only plugins are ones GitHub Pages already allows: `jekyll-seo-tag` and `jekyll-sitemap`.
- A small custom layout and one CSS file (about 150 lines) give full control to match the
  reference look. A theme like al-folio would bring a lot of code we'd need to strip out.

Alternatives considered:
- **al-folio**: built-in BibTeX publications grouped by year, but it's heavy, needs a
  GitHub Actions build, and is harder to restyle to a minimal look.
- **jekyll-scholar / BibTeX source**: GitHub Pages doesn't support it natively and it
  needs Actions. YAML is easier to hand-edit. We can add a `.bib` → YAML
  conversion script later if the list grows.

---

## 3. Hosting decision (needed before building)

This repo is `jackychen08/jackychen`. GitHub Pages serves **user sites** only
from a repo named exactly `<username>.github.io`.

| Option | URL | Notes |
|---|---|---|
| **A. Rename repo to `jackychen08.github.io`** (chosen) | `https://jackychen08.github.io/` | Clean root URL. `baseurl: ""` |
| B. Keep `jackychen` as a project site | `https://jackychen08.github.io/jackychen/` | Needs `baseurl: /jackychen`. All links must use `relative_url` |
| C. Either, plus a custom domain | e.g. `https://jackychen.io/` | Add a `CNAME` file and DNS records. Same setup as iandunn.io |

The templates use `{{ '/path' | relative_url }}` everywhere, so all three options work
without template changes.

---

## 4. Site map

| Path | Page | Contents |
|---|---|---|
| `/` | Home | Photo, name, position, affiliation, bio, links (Email, Google Scholar, GitHub, LinkedIn, CV), optional *News* list, optional *Selected Publications* (entries flagged `selected: true`) |
| `/publications` | Publications | Year jump bar → one `<section id="year-YYYY">` per year, newest first |
| `/cv` | CV (optional) | Embedded or linked PDF at `/assets/cv.pdf` |
| `/404.html` | Not found | Link back home |

Every page shares a top nav (`Home · Publications · CV`) and a footer
(name, year, "Last updated"). Page titles follow the reference format:
`Publications - Jacky Chen`.

---

## 5. Repository layout

```
.
├── _config.yml              # site title, author, nav, plugins, baseurl
├── _data/
│   ├── publications.yml     # source of truth for all papers
│   ├── links.yml            # profile links (Scholar, GitHub, ...)
│   └── news.yml             # optional dated news items
├── _includes/
│   ├── head.html            # meta, SEO tag, CSS, favicon
│   ├── nav.html
│   ├── footer.html
│   └── publication.html     # renders one publication entry
├── _layouts/
│   ├── default.html         # head + nav + {{ content }} + footer
│   └── page.html
├── assets/
│   ├── css/main.css
│   ├── img/profile.jpg
│   ├── papers/              # self-hosted PDFs (optional)
│   └── cv.pdf
├── index.md                 # Home
├── publications.html        # permalink: /publications
├── 404.html
├── Gemfile                  # local preview only (github-pages gem)
└── README.md                # how to add a paper / preview locally
```

---

## 6. Publication data model

`_data/publications.yml`: entries listed newest first. Order within a year is
the order in the file.

```yaml
- title: "Paper Title Goes Here"
  authors: ["Jacky Chen", "Second Author", "Advisor Name"]
  equal_contrib: ["Jacky Chen", "Second Author"]   # optional → renders "*"
  venue: "NeurIPS"                                  # short venue name
  venue_full: "Advances in Neural Information Processing Systems"  # optional tooltip
  year: 2025
  type: conference        # conference | journal | workshop | preprint | thesis
  note: "Spotlight"       # optional highlight
  selected: true          # also show on homepage
  links:                  # every key is optional; only present ones render
    paper: https://...
    arxiv: https://arxiv.org/abs/...
    code: https://github.com/...
    project: https://...
  bibtex: |
    @inproceedings{chen2025title,
      title={Paper Title Goes Here},
      author={Chen, Jacky and Author, Second and Name, Advisor},
      booktitle={NeurIPS},
      year={2025}
    }
```

---

## 7. Publications page rendering

`publications.html` (core logic):

```liquid
---
layout: page
title: Publications
permalink: /publications
---
{% assign years = site.data.publications | group_by: "year" | sort: "name" | reverse %}

<nav class="year-nav">
  {% for y in years %}<a href="#year-{{ y.name }}">{{ y.name }}</a>{% endfor %}
</nav>

{% for y in years %}
<section id="year-{{ y.name }}" class="pub-year">
  <h2><a href="#year-{{ y.name }}">{{ y.name }}</a></h2>
  <ol class="pub-list">
    {% for pub in y.items %}{% include publication.html pub=pub %}{% endfor %}
  </ol>
</section>
{% endfor %}
```

`_includes/publication.html` (per entry):

- **Title**, linked to `links.paper` if it exists.
- **Authors** joined with commas. The entry whose name equals `site.author.name` is
  wrapped in `<strong>`, and names in `equal_contrib` get a `*`.
- **Venue line**: `<em>{{ venue }}</em>, {{ year }}`, plus a note badge when `note` is set.
- **Link buttons**: one small pill per key in `links`, plus a `[BibTeX]` toggle
  built with `<details>` (no JavaScript). The toggle shows the BibTeX in a `<pre>`.

Anchor behavior:
- `html { scroll-behavior: smooth; }` and `.pub-year { scroll-margin-top: 4rem; }`
  so a sticky nav doesn't cover the year heading when you land on `#year-2025`.
- Each year heading links to itself, so you can copy a deep link.

---

## 8. Styling

- One stylesheet with a minimal, text-first academic look: system font stack,
  ~760px content column, generous line height, muted secondary text for venue lines.
- Light and dark mode via `prefers-color-scheme`, with colors defined as CSS variables.
- Responsive: the nav collapses to a single row that wraps, and the year jump bar scrolls
  horizontally on narrow screens.
- Optional: 120px paper thumbnails in a two-column entry layout, if the reference uses them.

---

## 9. Milestones

1. **Decide hosting** (§3). Rename the repo or set `baseurl`, then enable Pages
   (*Settings → Pages → Deploy from branch → `main` / root*).
2. **Scaffold**: `_config.yml`, layouts, includes, `main.css`, `Gemfile`.
   Check locally with `bundle exec jekyll serve`.
3. **Homepage**: bio, photo, links, optional news.
4. **Publications**: fill in `publications.yml` and build the year-grouped page and entry include.
5. **Polish**: CV page or PDF, 404, favicon, `jekyll-seo-tag` metadata, sitemap.
6. **Ship and verify**:
   - `/publications#year-2025` lands on the 2025 heading, not hidden behind the nav.
   - Every link button resolves (run `html-proofer` locally).
   - Layout works at 375px width and in dark mode.
   - Author self-bolding and the BibTeX toggles work.
7. **Optional**: custom domain (`CNAME` + DNS), Google Analytics or Plausible, a
   `.bib` → YAML import script, Projects/Talks/Teaching pages.

---

## 10. Content needed from Jacky

- [ ] Display name and preferred title/affiliation
- [ ] Short bio (1–2 paragraphs) and a headshot
- [ ] Profile links: email, Google Scholar, GitHub, LinkedIn, X/Bluesky, ORCID
- [ ] Publication list (a Google Scholar or BibTeX export is fine; it gets converted to YAML)
- [ ] CV PDF
- [ ] Optional: news items, project pages, paper thumbnails

## 11. Open questions

1. ~~Rename the repo to `jackychen08.github.io`, keep it as a project site, or use a custom domain?~~ Rename (option A).
2. Which pages beyond Home and Publications (CV, Projects, Talks, Teaching, Blog)?
3. Paper thumbnails and abstract toggles: include them or keep entries text-only?
4. Group by year only (like the reference), or add type filters (Conference / Journal / Preprint)?
