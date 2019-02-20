---
title: "Working around the annoying package-lock.json updates"
description: "A description of how I worked around the issue I had with package-lock.json constantly being modified whenever npm install is run."
tags:
  - npm
  - package-lock.json
  - npm install
  - npm ci
---

I use NPM for pretty much all my projects, and I appreciate the introduction of the `package-lock.json` file as a means to unify package versions across multiple machines. This helps me avoid the infamous "works on my machine" issue that plagued our team projects in the past.

But there's one problem I've been having ever since I first started committing the lock file into our project repositories - my `package-lock.json` file kept being modified almost every time the `npm install` command is called.

So what happens now is that whenever one of my team members pull the repo and runs `npm install`, 

What's the point of having a lock file if it keeps being modified by anyone who runs the `npm install` command?