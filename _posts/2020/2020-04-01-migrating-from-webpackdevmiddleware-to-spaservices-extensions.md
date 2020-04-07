---
layout: post
title: "Migrating from WebpackDevMiddleware to SpaServices.Extensions"
description: "A quick guide on migrating the front-end build process when upgrading to .Net Core 3.x."
comments: true
tags:
  - webpackdevmiddleware
  - spaservices
  - netcore
  - vue
---

Upgrading to .Net Core 3.x resulted in some breaking changes for my front-end development process, in particular the Hot Module Reloading (HMR) feature that I use in all my projects.

If you use the `WebpackDevMiddleware`, it's likely you would have seen this message pop up after upgrading to .Net Core 3.

![Sad day]({{ "/assets/img/2020/webpackdevmiddleware-obsolete.png" | absolute_url }}){: .center-image }

So, what do we do now?

<!--more-->

The good news is, Microsoft has implemented support for Angular and React, in the form of `AngularCliMiddleware` and `ReactDevelopmentServerMiddleware` middlewares, in their `SpaServices.Extensions` package. The bad news is, Vue developers like me are left out in the cold.

Fortunately, it's not the end of the world for us, and a quick look at the [source code for the `ReactDevelopmentServerMiddleware` middleware][0] shows that it's basically just a wrapper that starts an NPM task via `NodeScriptRunner`, attaches it to the middleware pipeline, and creates a proxy that allows HMR to work.

Once we understand this, we can repurpose the code to work with the Vue CLI.

I came across a [Gist][1] created by a GitHub user by the name of Alexey Zimarev that does exactly this, as I was writing my own solution. It goes to show that you should definitely Google before you start writing your own code ;)

Once you have added the code to your project, you can proceed to use it in your `Startup.cs`.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // all your other startup code

        app.UseSpa(spaBuilder =>
        {
            if (_env.IsDevelopment())
            {
                spaBuilder.Options.SourcePath = Path.Combine(_env.ContentRootPath, "src");
                spaBuilder.UseVueDevelopmentServer("dev");
            }
        });
    }
}
```

Run the app, and HMR should be back in business.

I hope this helps anyone else running into this problem.

## Update

If you're like me, and you're running your app in HTTPS mode locally, you will run into some issues when trying to get the dev server to work properly. For whatever reason, the `webpack-dev-server` team saw it fit to switch the request protocol for the SockJS request to connect to the dev server. So you'll see something like this when your application loads up.

```
GET https://localhost:56670/sockjs-node/info?t=1585758023901 net::ERR_SSL_PROTOCOL_ERROR
```

Accessing that URL resulted in a "This site can’t provide a secure connection" error.

However, when I navigated to `http://localhost:56670/sockjs-node/info?t=1585758023901` (notice the HTTP protocol), I was met with the usual response when everything is working correctly.

```json
{
    "websocket": true,
    "origins": [
        "*:*"
    ],
    "cookie_needed": false,
    "entropy": 437867818
}
```

I've tried a number of things in my attempt to get around this problem.

I've tried running the dev server in HTTPS mode, which resulted in the app to completely die on me with the `VueDevelopmentServerMiddleware` complaining that `UseProxyToSpaDevelopmentServer` is not able to connect to the dev server. This makes sense, since the dev server doesn't have a valid SSL certificate, and I couldn't be bothered to set one up for development purposes.

I then came across this [GitHub Pull Request][2], that prevents `webpack-dev-server` from changing the request protocol if the host name is set to `127.0.0.1`.

I updated my `vue.config.js` with the following settings:

```js
module.exports = {
    devServer: {
        host: '127.0.0.1'
    }
};
```

And the `VueDevelopmentServerMiddleware` code with the following:

```csharp
var targetUriTask = portTask.ContinueWith(
    task => new UriBuilder("http", "127.0.0.1", task.Result).Uri);
```

I stopped seeing requests being made to the HTTPS endpoint, but I started getting a different error instead.

```
Uncaught Error: SecurityError: An insecure SockJS connection may not be initiated from a page loaded over HTTPS
```

Damned if I HTTPS. Damned if I HTTP.

/tableflip

I was just about ready to give up, when I came across this option in the `webpack-dev-server` documentation to switch the transport mode from the default one, `sockjs` to regular Websockets. As soon, as I switched to Websockets, everything clicked into place, and I wasn't getting any more errors, and HMR was working.

/yeay!

To be honest, I don't quite know why it works, and since I'm too tired at this point to figure it out, I'm just going to take the win and call it a day.

To sum up, the final `vue.config.js` is as follows:

```js
module.exports = {
    devServer: {
        host: '127.0.0.1',
        port: process.env.PORT,
        transportMode: 'ws'
    }
};
```

The `process.env.PORT` environment variable is set by `VueDevelopmentServerMiddleware`, and I had to set this up here because when the app first fires up, it first tries to connect to the port that the app is running on for some reason.

I'm pretty sure I'm not the first person to run into this issue, and I really do hope that my experience today can help other developers who run into the same problem.

## Another update

My colleague brought [this NuGet package][3] to my attention, which does pretty much everything you would need in order to run HMR in a .Net Core Vue.js project. The setup is much simpler and the code looks cleaner. However, you will need to specify `host` and `transportMode` as described above is you're running your project in HTTPS locally.

[0]: https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/SpaServices.Extensions/src/ReactDevelopmentServer/ReactDevelopmentServerMiddleware.cs
[1]: https://gist.github.com/alexeyzimarev/f0262426aa38e2c1ed2913252ceb5e7a
[2]: https://github.com/webpack/webpack-dev-server/pull/2303
[3]: https://github.com/EEParker/aspnetcore-vueclimiddleware
