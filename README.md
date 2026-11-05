# Thunderbird Release Docs

Processes, guides, notes and reports for Thunderbird release engineering.

- [Release Processes](processes/) — how to perform the standard release
  operations.
- [DIY Guides](diy/) — how to manually perform tasks that are normally
  automated.
- [For Your Information](fyi/) — build system and release process
  idiosyncrasies, plus everyday tips.
- [Reports](reports/) — generated from release data and rebuilt daily, e.g. how
  often releases ship on schedule.

## Adding a doc

Drop a markdown file into the relevant directory, then add an entry for it to
[`_data/nav.yml`](_data/nav.yml) so it shows up in the site's sidebar.

## Formatting a doc

```sh
npx prettier --prose-wrap always --print-width 80 --write "**/*.md"
```

## Local preview

After setting up the prerequisites described
[here](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll#building-your-site-locally),
run the site locally with the below:

```
bundle install && bundle exec jekyll serve --livereload
```

Then open <http://localhost:4000>.

The [reports](reports/) are generated rather than committed. To include them in
a local preview, generate them first (requires
[uv](https://docs.astral.sh/uv/)):

```sh
cd reports
uvx --from git+https://github.com/thunderbird/thunderbird-release-tools \
    tb-release on-schedule --jekyll
```
