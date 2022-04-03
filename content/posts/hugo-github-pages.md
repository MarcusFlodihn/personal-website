---
title: "Host a Hugo webpage on Github"
date: 2022-03-28T20:10:16+02:00
draft: false
tags:
    - hugo
    - git
    - tutorial
    - static web site
    - hosting
---
This site is made with [Hugo](https://gohugo.io/), which is a static site generator. The site is hosted on Github pages which is free while staying under some user [limits](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#usage-limits).

The goal of the tutorial is to keep the static webpage in a git repository. When any changes are made and pushed to a specific branch, the site will be built and deployed automatically to Github pages.

## Getting started with Hugo

Hugo has a great getting started [tutorial](https://gohugo.io/getting-started/quick-start/) at their website. Once you have installed Hugo you can bootstrap a project with the following command:

```shell
hugo new site <name-of-your-project>
```
Then you can add some content to your website with:

```shell
hugo new posts/hello-world.md
```
If you add some content to the created markdown file, like so:

```plain
---
title: "Hello World!"
date: 2022-03-28T20:10:16+02:00
draft: true
---
This is my first content!
```
You can start a server locally that hosts the webpage at [localhost:1313](localhost:1313) and you can preview your content! 🎉

## Building site on pushes

In order to have the website built when a push is made, we need to create a Github workflow. Create a yaml-file in the `.github/workflows/` like so:

```sh
mkdir .github/workflows && touch .github/workflows/build.yaml
```

Put the following content into the file:

```yaml
name: github pages

on:
  push:
    branches:
      - <your-branch-name>
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## Setting up the repository

Let's push this to Github, create a public repository for our code at [https://github.com](https://github.com).

Then go to the repository settings and head to the "Pages" options, specify which branch that Github should host, and the root for the website.

I chose "master" for the branch and "/" for the website root.

If you have your own domain, you can also add it to the "Custom domain" setting. HTTPS might as well be enforced.

Then we can push our content to it:

```shell
git init
git add .
git commit -m "initial commit"
git remote add origin git@github.com:<your-username>/<your-repo-name>.git
git push -u origin master
```

