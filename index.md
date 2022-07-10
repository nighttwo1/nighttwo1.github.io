---
layout: default
title: Home
nav_order: 1
description: "Home"
permalink: /
---

# _Keep your silver, give me that Gold_
![sunrise-gd26934312_1920](https://user-images.githubusercontent.com/43779571/178147605-cc876dda-0ff3-429d-9132-faa04c8f7fc7.jpg)

{: .fs-6 .fw-300 }

<!-- [Get started now](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }  -->
[View it on GitHub](https://github.com/nighttwo1){: .btn .fs-5 .mb-4 .mb-md-0 }

---

## Getting started

### Dependencies

Just the Docs is built for [Jekyll](https://jekyllrb.com), a static site generator. View the [quick start guide](https://jekyllrb.com/docs/) for more information. Just the Docs requires no special plugins and can run on GitHub Pages' standard Jekyll compiler. The [Jekyll SEO Tag plugin](https://github.com/jekyll/jekyll-seo-tag) is included by default (no need to run any special installation) to inject SEO and open graph metadata on docs pages. For information on how to configure SEO and open graph metadata visit the [Jekyll SEO Tag usage guide](https://jekyll.github.io/jekyll-seo-tag/usage/).

### Quick start: Use as a GitHub Pages remote theme

1. Add Just the Docs to your Jekyll site's `_config.yml` as a [remote theme](https://blog.github.com/2017-11-29-use-any-theme-with-github-pages/)

```yaml
remote_theme: just-the-docs/just-the-docs
```

<small>You must have GitHub Pages enabled on your repo, one or more Markdown files, and a `_config.yml` file. [See an example repository](https://github.com/pmarsceill/jtd-remote)</small>

### Local installation: Use the gem-based theme

1. Install the Ruby Gem

```bash
$ gem install just-the-docs
```

```yaml
# .. or add it to your your Jekyll site’s Gemfile
gem "just-the-docs"
```

2. Add Just the Docs to your Jekyll site’s `_config.yml`

```yaml
theme: "just-the-docs"
```

3. _Optional:_ Initialize search data (creates `search-data.json`)

```bash
$ bundle exec just-the-docs rake search:init
```

3. Run you local Jekyll server

```bash
$ jekyll serve
```

```bash
# .. or if you're using a Gemfile (bundler)
$ bundle exec jekyll serve
```

4. Point your web browser to [http://localhost:4000](http://localhost:4000)

If you're hosting your site on GitHub Pages, [set up GitHub Pages and Jekyll locally](https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll) so that you can more easily work in your development environment.