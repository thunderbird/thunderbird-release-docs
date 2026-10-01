# Thunderbird Release Docs

Processes, guides and notes for Thunderbird release engineering.

- [Release Processes](processes/) — how to perform the standard release
  operations.
- [DIY Guides](diy/) — how to manually perform tasks that are normally
  automated.
- [For Your Information](fyi/) — build system and release process
  idiosyncrasies, plus everyday tips.

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
