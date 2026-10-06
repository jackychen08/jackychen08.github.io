# jackychen08.github.io

Personal academic website, built with Jekyll and hosted on GitHub Pages.
See [PLAN.md](PLAN.md) for the design plan.

## Editing content

| What | Where |
|---|---|
| Name, position, affiliation, photo | `author:` in `_config.yml` |
| Bio | `index.md` |
| Profile links (Email, Scholar, LinkedIn, GitHub) | `_data/links.yml` |
| News | `_data/news.yml` |
| Publications | `_data/publications.yml` |
| Top navigation | `nav:` in `_config.yml` |

### Adding a paper

Add an entry to `_data/publications.yml` (the file has a commented example).
The Publications page groups papers by `year`, newest first, and gives each year
an anchor such as `/publications#year-2025`. Set `selected: true` to also list
the paper on the homepage. Your name is bolded wherever it matches an entry in
`author.pub_names` in `_config.yml`.

## Local preview

Requires Ruby 3.x and Bundler.

```sh
bundle install
bundle exec jekyll serve
# open http://localhost:4000
```

## Deployment

GitHub Pages builds the site on every push to the branch selected under
**Settings → Pages → Build and deployment → Deploy from a branch**.
