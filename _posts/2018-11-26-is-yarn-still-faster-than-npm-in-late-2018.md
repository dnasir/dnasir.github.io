---
title: "Is Yarn still faster than NPM in late 2018?"
date: "2018-11-26 10:00:00 +2"
tags: [yarn, npm, 'package managers']
---

If you're like me, chances are you've heard about Yarn, an alternative to the Node Package Manager (NPM). You may have even heard how much faster it is compared to NPM.

The dust has settled since Yarn first entered the scene, and NPM has punched back with quite a few updates. So it's time to see if anything has changed.

<!--more-->

## The setup

I created a small project for this experiment. You can see the `package.json` below.

```json
{
  "devDependencies": {
    "@babel/core": "^7.1.2",
    "@babel/plugin-proposal-class-properties": "^7.1.0",
    "@babel/plugin-proposal-object-rest-spread": "^7.0.0",
    "@babel/polyfill": "^7.0.0",
    "@babel/preset-env": "^7.1.0",
    "autoprefixer": "^9.3.1",
    "babel-loader": "^8.0.4",
    "css-loader": "^1.0.1",
    "happypack": "^5.0.0",
    "mini-css-extract-plugin": "^0.4.4",
    "node-sass": "^4.9.4",
    "postcss-loader": "^3.0.0",
    "postcss-preset-env": "^6.3.0",
    "sass-loader": "^7.1.0",
    "style-loader": "^0.23.1",
    "ts-loader": "^5.3.0",
    "typescript": "^3.1.5",
    "vue-loader": "^15.4.2",
    "vue-template-compiler": "^2.5.17",
    "webpack": "^4.23.1",
    "webpack-bundle-analyzer": "^3.0.3",
    "webpack-cli": "^3.1.2",
    "webpack-merge": "^4.1.4"
  },
  "dependencies": {
    "vue": "^2.5.17",
    "vue-property-decorator": "^7.2.0"
  }
}
```

I used Powershell's [`Measure-Command`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/measure-command?view=powershell-6) feature to measure the time it takes to execute the given command.

I used the following commands to measure how long it took each package manager to complete the install. I also included the `--production` flag for production runs.

```powershell
Measure-Command { npm install }

Measure-Command { yarn install }
```

The package managers used in this test are **NPM 6.4.1**, and **Yarn 1.12.3**, which are the latest version from each camp at the time of writing.

I cleaned out the cache for both package managers before each clean runs using `npm cache clean -f` and `yarn cache clean`. I skipped doing this for the cached runs.

## The results

<table>
  <thead>
    <tr>
      <th>Scenario</th>
      <th>NPM</th>
      <th>Yarn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Clean</td>
      <td>31.93s</td>
      <td style="background: green; color: white">29.55s</td>
    </tr>
    <tr>
      <td>Clean (--production)</td>
      <td style="background: green; color: white">6.19s</td>
      <td>12.55s</td>
    </tr>
    <tr>
      <td>Cached</td>
      <td>20.52s</td>
      <td style="background: green; color: white">15.67s</td>
    </tr>
    <tr>
      <td>Cached (--production)</td>
      <td>5.29s</td>
      <td style="background: green; color: white">1.56s</td>
    </tr>
  </tbody>
</table>

No, that's not a typo. Yarn really did install those two packages in **one and a half seconds**.

Next, I was curious to find out how much of a role the lock files play in terms of performance, so I did another set of tests where I would run the install command with the relevant flags.

```powershell
Measure-Command { npm install --no-package-lock }

Measure-Command { yarn install --no-lockfile }
```

Obviously you wouldn't do this under normal circumstances, but I was curious how much performance can be attributed to the lock files.

<table>
  <caption>Lock file disabled</caption>
  <thead>
    <tr>
      <th>Scenario</th>
      <th>NPM</th>
      <th>Yarn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Clean</td>
      <td style="background: green; color: white">42.12s</td>
      <td>46.94s</td>
    </tr>
    <tr>
      <td>Clean (--production)</td>
      <td style="background: green; color: white">15.40s</td>
      <td>25.53s</td>
    </tr>
    <tr>
      <td>Cached</td>
      <td>28.09s</td>
      <td style="background: green; color: white">25.10s</td>
    </tr>
    <tr>
      <td>Cached (--production)</td>
      <td style="background: green; color: white">8.70s</td>
      <td>16.32s</td>
    </tr>
  </tbody>
</table>

The results are quite interesting, as NPM seems to have the upper hand in all but one scenario. It's evident the lock files play a huge role in enhancing performance for both package managers.

All the runs were consistently faster with the lock files present, and during a cached run, Yarn took about a quarter of the time NPM did for downloading the same packages.

## Conclusion

Over the last few versions, NPM has managed to close the performance gap, and as it stands today, both NPM and Yarn are neck and neck in terms of speed - a stark contrast to when Yarn was first introduced.

So if speed is your thing, Yarn will perform marginally better than NPM, though that may still change given how much improvements NPM has made over the past couple of months.

Let me know if you're still using Yarn, and why, in the comments.
