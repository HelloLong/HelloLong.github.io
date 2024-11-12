---
title: Share some pitfalls I encountered when using Hexo
date: 2024-04-30 22:45:44
tags:
---
## Integrate with github pages and use github actions to deploy
here is an example action config file.
yousitedirectory/.github/workflows/page.yml
```
name: Pages

on:
  push:
    branches:
      - master  # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          # token: ${{ secrets.HELLOLONG_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: false
      - name: Use Node.js 20
        uses: actions/setup-node@v4
        with:
          # Examples: 20, 18.19, >=16.20.2, lts/Iron, lts/Hydrogen, *, latest, current, node
          # Ref: https://github.com/actions/setup-node#supported-version-syntax
          node-version: '20'
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
      actions: read     # to download an artifact uploaded by `actions/upload-pages-artifact@v3`
    environment:
      name: github-pages
      url: https://xulonghua.com/
      # url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
``` 

## How to add a picture
config.yaml：
```
post_asset_folder: true
```
then hexo new command will create a dirctory as a source directory, then you can upload you img in this directory.
now you can refer to a img like this way.
```
{% asset_img image.jpg 这是一张图片 %}
```
if you want to change size, you may import the img file like this.
```
<img src="/asset/[your_image]" width="[width]" height="[height]" alt="[alternative_text]" title="[title]">
```

if you want to use the common markdown gramma, you need another nodejs plugin. 
```
![](1/image.jpg)
```
you need hexo-renderer-marked. 
```
npm install hexo-renderer-marked
```
config.yaml：
```
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
```
## Reference
how to modify size of asset_img https://github.com/hexojs/hexo/issues/2175
add img in hexo https://blog.csdn.net/qq_41907806/article/details/109064385