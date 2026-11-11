# Website

The landing page and blog live in `docs/` and use GitHub Pages' built-in Jekyll support. Paths below are relative to this folder.

## Edit and preview

- Edit `index.md` for the homepage.
- Write articles in `_drafts/` and store article images in `assets/blog/`.
- The scanned notes in `blog/formal_methods/` are excluded from the website.

With a current Ruby installation (rather than macOS's system Ruby) and Bundler, run:

```sh
cd docs
bundle install
bundle exec jekyll serve
```

Open `http://127.0.0.1:4000`. Add `--drafts` to preview unpublished articles.

## Publish a post

Start with `_drafts/formal-methods.md`:

```markdown
---
layout: post
title: Formal methods
description: A short summary of the article.
---

Article text in Markdown.
```

When ready, move it to `_posts/YYYY-MM-DD-formal-methods.md`, using the publication date. Its URL will be `/blog/formal-methods/`; it appears automatically on the blog page. Keep post filename slugs unique.

Drafts are omitted from normal site builds. Files committed to a public repository remain visible on GitHub.

## GitHub Pages

After merging the website changes into `main`:

1. In **Settings → Pages**, choose **Deploy from a branch**, `main`, and `/docs`.
2. Set the custom domain to `airspec.wiki` and configure its DNS for GitHub Pages.
3. Enable **Enforce HTTPS** once the certificate is available.

`CNAME` and `_config.yml` already target `airspec.wiki`. If using the default project URL instead, remove `CNAME`, set `url` to `https://airspec-aero.github.io` and `baseurl` to `/airspec`.

No custom Actions workflow is required. GitHub builds and publishes updates pushed to the selected branch.
