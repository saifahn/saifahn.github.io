---
layout: post
title:  "Setting up Jekyll on a new computer"
date:   2018-08-06 20:46 +0100
author: "Sean"
categories: 
---

Migrating to a new computer or starting from a fresh install is always a bit of a chore. You have to get all your software and development environment set up.
It's a huge amount of friction to getting started on projects again. I wanted to write this short blog post to get me started and motivated to do some spare time projects.

## Process

1. I cloned the repo for this blog from GitHub
2. I ran `gem install jekyll bundler` to install bundler and jekyll as I didn't have them installed
  - Ruby was automatically installed alongside xcode-tools when I was first setting up the laptop
3. I used `bundle install --path vendor/bundle` to install gems for the project separately to have the dependencies in an isolated environment instead of globally
4. After that it was all good to go with `bundle exec jekyll serve`
5. Time to upload this post
