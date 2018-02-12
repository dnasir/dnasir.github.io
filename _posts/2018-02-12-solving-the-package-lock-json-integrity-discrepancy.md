---
layout: post
title: "Solving the annoying package-lock.json integrity discrepancy"
date: 2018-02-12 20:40
comments: true
tags: [node, npm, package-lock.json]
---

One of the most annoying feature, in my opinion, added to the Node Package Manager (NPM) is the automatically generated 
[`package-lock.json`](https://docs.npmjs.com/files/package-lock.json) file, which is used to "describes the 
exact tree that was generated, such that subsequent installs are able to generate identical trees, regardless 
of intermediate dependency updates".

As described in the documentation, one of the benefits of using `package-lock.json` is that it "describes a single representation of a dependency tree such that teammates, deployments, and continuous integration are guaranteed to install exactly the same dependencies."

Ironically enough, the `package-lock.json` has created more annoyance within the team than anything else.

<!--more-->

The issue is that when working in a team, with multiple different machines, and potentially different setups such as different
Node and NPM versions, the generated `package-lock.json` can differ from machine to machine, triggering the source control
system to mark the file as having been modified, even if the developer merely installed NPM packages via the `npm install` command.

What happened on two of the machines I work with was that one machine was using SHA-1, while the other was
using SHA-512, despite the fact **both machines were running the same versions of Node and NPM**.

As you can imagine, this is an annoying problem to have. So here's the solution.

1. Ensure all team members use the same Node and NPM versions.
2. Revert any changes made to the `package-lock.json` file.
3. Delete the `node_modules` folder.
4. Run `npm cache clean --force` in terminal.
5. Run `npm install`.

If all went as expected, you should not see any change made to the `package-lock.json` file after package installation completes.

I hope this helps anyone else who ran into this problem.

Thanks to [BrendanFDMoore](https://github.com/BrendanFDMoore){:target="_blank"} for the solution to this problem. [https://github.com/npm/npm/issues/16938#issuecomment-339863980](https://github.com/npm/npm/issues/16938#issuecomment-339863980){:target="_blank"}

*Wassalam*