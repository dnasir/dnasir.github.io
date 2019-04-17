---
title: "Azure Functions + Node.js + TypeScript + Webpack"
description: "My experience setting up an Azure Functions App project for Node.js in TypeScript using Webpack."
tags:
  - azure functions
  - node
  - node.js
  - nodejs
  - ts
  - typescript
  - javascript
  - webpack
---

I'm a big TypeScript fan. If my blog posts and GitHub projects haven't already made that clear, I'm now putting that on record.

So when I found out that the latest `azure-functions-core-tools` now comes with the ability to create a Functions app in TypeScript out of the box, I got excited.

In this post, I'd like to share my experience setting up an Azure Functions App project for Node.js in TypeScript, with Webpack for generating the app bundles.

<!--more-->

## The setup

You'll first need to install `azure-functions-core-tools` globally. This is not absolutely necessary though, since you can also install it to an empty folder, and execute commands using the `npx` helper. That's what I did, since my primary project was still running on the Azure Functions 1.x runtime, and I didn't want to change anything at the time.

1. `func init` to start the wizard. This will install the project to the current directory.
2. Set the worker runtime to `node`.
3. Set the language to `typescript`.

Congratulations. You have just created an Azure Functions project in TypeScript.

Under the hood, it's using `tsc` to compile the TypeScript files, which gets placed in the `dist` folder. The `function.json` has been set up to point to the compiled code using the recently added `scriptFile` property that tells the Azure Functions runtime where to find the code when it runs the Function app.

That's it, right? Job well done, right? Er, no.

## Paths/Aliases

As my Functions project got larger, I started having to share code across multiple Functions, and so I started using the `paths` property in my `tsconfig.json` file to handle module aliasing.

But as soon as I replaced `../../common/sharedModule` with `common/sharedModule`, my Functions apps stopped working. `Unable to find module`, it said.

Turns out, `tsc` does not handle module resolution. It does not transform aliases to their corresponding paths, and the TypeScript team has no intention to implement this feature, as described in this [old GitHub issue][0].

Solution?

## Webpack All The Things!

![Meme image of Webpack All The Things!]({{ "/assets/img/2019/webpack-all-the-things.jpg" | absolute_url }} "Webpack All The Things!")

You can use other bundlers too, if you want. I just like Webpack, so I'm going to use that for this project.

Firstly, you'll need [Webpack][1] (duh), and [`ts-loader`][2] to transpile TypeScript code to regular JavaScript.

Next, you'll need to add a `webpack.config.js` to your project.

Here's a sample configuration file.

```js
const path = require('path');
const TsconfigPathsPlugin = require('tsconfig-paths-webpack-plugin');

module.exports = {
  target: 'node', // IMPORTANT!
  entry: {
    QueueTrigger: path.resolve(__dirname, './QueueTrigger/index.ts')
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/
      }
    ]
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
    plugins: [
      new TsconfigPathsPlugin()
    ]
  },
  output: {
    filename: '[name]/index.js',
    path: path.resolve(__dirname, 'dist'),
    libraryTarget: 'commonjs' // IMPORTANT!
  }
};
```

### `target`

Ensure the output `target` is set to `node` to ensure that the code is compiled to work in a Node.js environment. The default value is `web`, and certain libraries, such as [Axios][3], depend on this to determine which version of the code it should load up during compilation, and using the wrong code has some dire consequences, as I have come to discover.

My project depended on Axios, and prior to setting the build target to `node`, my Functions app kept throwing this error about `XMLHttpRequest` not being defined. This is because Webpack built the project for the web environment where `XMLHttpRequest` actually exists, and not for the Node environment, where it doesn't.

Setting the build target to `node` fixed this problem.

### `libraryTarget`

You'll need to set the output `libraryTarget` to `commonjs` to ensure the entry point's default export is the output module's default export. Without this, you'll get the following error when you run your Azure Functions app.

```
[error] Worker was unable to load function QueueTrigger: 'Unable to determine function entry point. If multiple functions are exported, you must indicate the entry point, either by naming it 'run' or 'index', or by naming it explicitly via the 'entryPoint' metadata property.'
```

### `tsconfig-paths-webpack-plugin`

The next part is optional, but crucial for my situation. I wanted to use aliases, so I can avoid having to resolve module paths using relative paths. To this end, I went with [`tsconfig-paths-webpack-plugin`][4] to use the aliases I've set up in my `tsconfig.json` file.

Alternatively, you can also set this up in your `webpack.config.js` file like so:

```js
module.exports = {
  resolve: {
    alias: {
      'common': path.resolve(__dirname, './common')
    }
  },
};
```

But who would want to do that anyway, right?

### Update project NPM scripts

Finally, you will want to update the NPM scripts defined in the project `package.json` file, specifically the `build` and `watch` scripts.

```json
{
  "scripts": {
    "build": "webpack --mode=production",
    "watch": "webpack --mode=development --watch"
  }
}
```

So the next time you run `npm run start`, it will start up the Azure Functions project, and at the same time run the Webpack watcher.

It should be noted that in order for the changes to be picked up by the runtime, you'll need to make sure that the `dist` folder is added to the watch list. This is done in the `host.json` file.

```json
{
  "watchDirectories": [
    "dist"
  ]
}
```

Now every time Webpack rebuilds the code and updates the files in the `dist` folder, the Azure Functions runtime will detect this, and proceeds to restart the Functions app.

Stay tuned for the next part, where I'll describe my experiment with deploying the Azure Functions App I've created using this method.

[0]: https://github.com/Microsoft/TypeScript/issues/10866
[1]: https://www.npmjs.com/package/webpack
[2]: https://www.npmjs.com/package/ts-loader
[3]: https://www.npmjs.com/package/axios
[4]: https://www.npmjs.com/package/tsconfig-paths-webpack-plugin