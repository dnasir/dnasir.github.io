---
layout: post
title: "Using Git hooks to enforce development policies"
description: "This post describes how we used the Git pre-commit hook to enforce development policies, such as linting and testing."
comments: true
tags:
  - git-hooks
  - lint-staged
  - pre-commit
---

I was recently asked to automate some front-end checks that ensures the code our developers commit to the repositories are in line with the project standards. This ensures that our code is uniform across all our projects, and prevents comments like "please fix linting" in pull requests.

One of the other teams have been using [Husky](https://github.com/typicode/husky) for some time, and while they seem to really like it, I wasn't convinced. I didn't understand why you would need to use a separate package to achieve what can already be done using [Git hooks](https://git-scm.com/docs/githooks). Especially when it doesn't really bring anything new to the table, and the setup is relatively laborious compared to Git hooks.

There are two tasks I want to run before allowing Git commits to take place - linting, and unit testing IF any of the staged files are "testable" - i.e. TypeScript files, Vue.js components. We didn't want anyone to commit anything that would just end up failing at the CI build step.

<!--more-->

## Setting up linters

We use a combination of [ESLint](https://eslint.org/) and [Prettier](https://prettier.io/) for code linting in our projects. One to check for potential functional issues, and the other to ensure uniform formatting across our codebase.

As for the runner itself, we went with [lint-staged](https://github.com/okonet/lint-staged), since it does the job well enough, and writing our own shell script is just a waste of time. So make sure you install that in your project.

```json
// src/MyProject/package.json
{
  "optionalDependencies": {
    
  },
  "lint-staged": {
    "*.{js,ts,vue}": [
      "prettier --write",
      "eslint"
    ],
    "*.{css,scss}": [
      "prettier --write"
    ]
  }
}
```

## Setting up the pre-commit Git hook

1. Create a folder in the root of your repository, and name it `git-hooks`.

2. Create a file in that new folder, and name it `pre-commit`. This is important, because the file name needs to match the naming convention defined in the Git hooks manual.

3. Paste the following code block as the content for the `pre-commit` file you just created. The comments describes what each step does.

```sh
# git-hooks/pre-commit
#!/bin/sh

# Set path for access to global executables like npm/npx
PATH="/usr/local/bin:$PATH"

# Helper for those using NVM so it points to the correct path
if [ -f $HOME/.nvm/nvm.sh ]
then
    . $HOME/.nvm/nvm.sh
    PATH="$HOME/.nvm/versions/node/$(nvm current)/bin:$PATH"
fi

# Check if we have anything to commit
staged_files=$(git diff --name-only --cached)
if [ -z "$staged_files" ]; then
    echo "Nothing to commit."
    exit 1
fi

# CD into folder with package.json - depends on project setup
cd src/MyProject

# Run lint and fix
npx lint-staged --allow-empty

lint_status=$?
if [ $lint_status -ne 0 ]; then
  echo "Linting failed. Fix and try again."
  exit 1
fi

# Check again in case changes were reverted due to linting
staged_files=$(git diff --name-only --cached)
if [ -z "$staged_files" ]; then
    echo "Nothing to commit."
    exit 1
fi

# Check if there are "testable" changes - i.e. JS, TS, Vue files
staged_testable_files=$(git diff --name-only --cached | grep -E '\.(js|ts|vue)$')
if [ -n "$staged_testable_files" ]; then
    # Run tests
    echo "Running tests"
    npm run test

    test_status=$?
    if [ $test_status -ne 0 ]; then
        echo "Tests failed. Fix and try again."
        exit 1
    fi
fi

exit 0
```

4. Make the file executable. If you're on Windows, you can do this using the Bash command line.

```console
chmod +x
```

5. Update the Git hooks path. This lets your Git client know where to look for hook files.

```console
git config core.hooksPath ./git-hooks
```

This command can also be added to the project's `package.json` file as a `postinstall` script. This way, other devs who pull the repo can have their project configured when they run `npm install`.

```json
// src/MyProject/package.json
{
  "scripts": {
    "postinstall": "git config core.hooksPath ./git-hooks"
  }
}
```

That's it. Now every time someone tries to commit anything to your repo, Git will automatically run the lint task, and if necessary, the unit tests.

## Potentially obvious questions

### Why not just use Husky?

Like I've mentioned above, Husky is yet another dependency that requires some elaborate setup to get going, and does not bring anything useful to the table. Git hooks is more than enough for the stuff I need done.

### Why use a `git-hooks` folder instead of `.git/hooks`?

I tried that, but immediately noticed that any changes made to the `.git` folder does not get picked up by Git. I didn't know how to get to work, and I didn't want to waste my time when this solution works just as well.
