# Dapr Blog Repo

[Dapr](https://dapr.io) is a portable runtime for building distributed applications, and this repo holds the source of its blog.

This repo contains the markdown files which generate the Dapr blog site over at https://blog.dapr.io. Head over there to read the blog and learn more about the latest Dapr news! Read on to get up and running with a local environment to contribute to the blog.

## Overview

The Dapr blog is built using [Hugo](https://gohugo.io/) with the [Docsy](https://docsy.dev) theme, hosted on an [Azure static web app](https://learn.microsoft.com/azure/static-web-apps/overview).

The [daprblog](./daprblog) directory contains the hugo project, markdown files, and theme configurations.

## Pre-requisites

- [Hugo extended version](https://gohugo.io/getting-started/installing)
- [Node.js](https://nodejs.org/en/)

## Environment setup

1. Ensure pre-requisites are installed
1. Clone repository
1. Change to the daprblog directory: `cd daprblog`
1. Update submodules: `git submodule update --init --recursive`
1. Install npm packages: `npm install`

## Run local server
1. Make sure you're still in the daprblog directory
1. Run `hugo server --disableFastRender`
1. Navigate to `http://localhost:1313/posts`

## Write a post

Posts live in `daprblog/content/posts/<year>/`. For a post with images, create a folder named after the post's slug and put the post in `index.md` inside it, for example `daprblog/content/posts/2025/kubecon-na/index.md`. A post without images can be a single Markdown file, such as `daprblog/content/posts/2025/v1.15-release.md`.

Start the post with front matter like this, taken from an existing post:

```yaml
---
date: "2025-11-08T07:00:00-07:00"
title: "Come meet us at the Project Pavilion at KubeCon North America!"
linkTitle: "Meet us at KubeCon North America"
author: Dapr project maintainers
type: blog
---
```

- `date`: the publish date and time, with a UTC offset.
- `title`: the full title shown on the post.
- `linkTitle`: a shorter title used in the post list and navigation.
- `author`: the name or names of the authors, for example `Jane Doe` or `Dapr project maintainers`.
- `type`: always `blog`.

Put images in the same folder as the post's `index.md` and reference them by file name:

```markdown
![Dapr at KubeCon North America 2025](kubecon-na-2025-dapr.png)
```

If the year folder doesn't exist yet, add it with an `_index.md` that sets `title`, `linkTitle` and `type: blog`, like `daprblog/content/posts/2026/_index.md`.

## Update blog
1. Create new branch
1. Commit and push changes to content
1. Submit pull request to `master`
1. Staging site will automatically get created and linked to PR to review and test
