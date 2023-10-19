---
title: New Site - From Jekyll to Eleventy
draft: false
description: Porting site from Jekyll to Eleventy
date: 2023-10-19
tags: intro
---

I've rebuilt this site using the [Eleventy](https://www.11ty.dev/) static site generator.

The previous version ran on [jekyll](https://jekyllrb.com/), whoose out-of-the-box integration
with [Github Pages](https://pages.github.com/) felt revolutionary when I made the switch
from WordPress years ago.

This site is based on the [eleventy-base-blog](https://github.com/11ty/eleventy-base-blog) template
(which appears to be an official release).

Source files are hosted on the [mattmakesmaps.github.com](https://github.com/mattmakesmaps/mattmakesmaps.github.com)
project repo's `master` branch, with [github actions](https://github.com/mattmakesmaps/mattmakesmaps.github.com/actions/runs/6556377877)
used to build and deploy content from the `gh-pages` branch. At Tableau, I used Travis CI and JetBrains' TeamCity for builds, and getting started with Github Actions felt very similar.

With the old Jekyll content also being markdown entries, I'll be able to migrate as much or as
little of my old content as I choose over the next few weeks.

Below is an example of a Github Actions run deploying the site. 35s for a free build
(since the repo is public) is perfect for my purposes.

{% image "./build-screenshot.png", "Screenshot showing the github actions build pipeline. Total runtime is 35 seconds." %}

With regards to images, I wasn't familiar with the current standard
[webp format](https://developers.google.com/speed/webp) nor the best practice of generating multiple sized
versions of each image, optimizing loading performance across a variety of potential screen sizes.
The latter is refered as [responsive images](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).

The Eleventy framework handles this well out-of-the-box, allowing you generate custom export functions
that handle all aspects of output resolution and formats. I found the following to be a
great tutorial: [https://www.aleksandrhovhannisyan.com/blog/eleventy-image-plugin/](https://www.aleksandrhovhannisyan.com/blog/eleventy-image-plugin/)