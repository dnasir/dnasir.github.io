---
title: "How To Auto-publish GitHub Pages Using Azure Functions"
description: "A guide on how to automatically publish GitHub Pages using Azure Functions."
tags: 
  - github
  - github pages
  - azure functions
  - azure
  - jekyll
---

If you're reading this, and it's past 24th November 2018 at 10:00 EET, it means my small experiment worked.

As you have probably noticed, I've moved away from Wordpress to GitHub Pages, which runs on Jekyll, a static site generator. The one thing I miss from my Wordpress days is the ability to schedule publishing new posts.

GitHub Pages requires you to manually add posts to the master branch of the repository, which means that if I wanted to publish the post I wrote last night at 9am the next morning, I would have to physically push or merge said post to the repository at 9am the next morning. Needless to say, this is far from ideal.

So I decided to use Azure Functions to call GitHub's API to instruct it to build my GitHub Pages project, which should automatically publish my posts based on their publish time.

<!-- more -->

## The setup

### Configuring Jekyll

In order to do this, you'll need to add the following to your Jekyll `_config.yaml` file.

```yaml
future: false
```

This tells Jekyll not to publish pages that have `date` values that are greater than the time at which the build was executed.

### Setting up the Azure Function

Head over to your Azure portal so we can set up an Azure Function that will call the GitHub API endpoint.

Create an Azure Function, and select the Timer trigger template. 

[![alt text][figure 1]][2]

Give it a name, and set a valid cron expression to set the schedule. I have mine set up for 10:00 every day.

`0 0 8 * * *`

Keep in mind that the cron expression is evaluated against UTC, and since my timezone is +2, I had to set the time to 08:00 in order to have it fire at 10:00 local time.

Then, insert the following code in the `index.js` section.

```js
const https = require('https');

// Replace these values with your own
const params = {
  USERNAME: 'USERNAME',
  REPO: 'REPO',
  TOKEN: 'TOKEN'
};

const options = {
    hostname: 'api.github.com',
    path: `/repos/${params.USERNAME}/${params.REPO}/pages/builds`,
    method: 'POST',
    headers: {
        'Authorization': `token ${params.TOKEN}`,
        'Accept': 'application/vnd.github.mister-fantastic-preview+json',
        'User-Agent': 'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)'
    }
};

module.exports = async (context, myTimer) => 
    new Promise((resolve, reject) => {
        https
            .request(options, (res) => {
                context.log.info(`${res.statusCode} ${res.statusMessage}`);
                resolve();
            })
            .on('error', (e) => {
                context.log.error(`Problem with request: ${e.message}`);
                reject();
            })
            .end();
});
```

That's pretty much it. You can click `Run` to test your Azure Function to see if it works. You should see a success message in the log.

## Cost

I'm running my Function under the Consumption plan, and according to their [pricing plan][1], I should get one million free executions every month. Cool.

However, the documentation also states that standard storage rates still apply. I'll update this post when I get my first bill next month, although it has to be noted that I'm currently on the Free Trial plan ;)


[1]: https://azure.microsoft.com/en-gb/pricing/details/functions/
[2]: /assets/img/2018/11/azure-functions-new.png
[figure 1]: /assets/img/2018/11/azure-functions-new.png "New timer trigger Azure Function dialog"