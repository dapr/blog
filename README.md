# Dapr Blog Repo

This repo contains the markdown files which generate the Dapr blog site over at https://blog.dapr.io. Head over there to read the blog and get learn more about the latest Dapr news! Read on to get up and running with a local environment to contribute to the blog.

## Overview

The Dapr blog is built using [Hugo](https://gohugo.io/) with the [Docsy](https://docsy.dev) theme, hosted on an [Azure static web app](https://docs.microsoft.com/en-us/azure/static-web-apps/overview).

The [daprblog](./daprblog) directory contains the hugo project, markdown files, and theme configurations.

## Pre-requisites

- [Hugo extended version](https://gohugo.io/getting-started/installing)
- [Node.js](https://nodejs.org/en/)

## Environment setup

1. Ensure pre-requisites are installed
1. Clone repository
1. Change to daprdocs directory: `cd daprblog`
1. Add Docsy submodule: `git submodule add https://github.com/google/docsy.git themes/docsy`
1. Update submodules: `git submodule update --init --recursive`
1. Install npm packages: `npm install`

## Run local server
1. Make sure you're still in the daprblog directory
1. Run `hugo server --disableFastRender`
1. Navigate to `http://localhost:1313/blog`

## Update blog
1. Create new branch
1. Commit and push changes to content
1. Submit pull request to `master`
1. Staging site will automatically get created and linked to PR to review and test
