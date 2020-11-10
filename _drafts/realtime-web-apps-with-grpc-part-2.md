---
layout: post
title: "Real-time web applications with gRPC - Part 2"
description: "The second part of my series on developing web applications with gRPC."
comments: true
tags:
  - grpc
  - grpc-web
  - netcore
  - protobuf
---

Setting up the back end was just half the story. Let's move onto the front end stuff.

<!--more-->

### Prerequisites

We went with a project called [`grpc-web`][4] for our front end implementation. However, there are a few things that needed to be set up in order to get the whole thing running.

Firstly, we had to install a protobuf compiler, which is used to generate the client code needed to communicate with the gRPC endpoint. Fortunately, the good devs have provided a [quick start guide][8] on how to install `protoc` and `protoc-gen-grpc-web` on your development machine.

### Generating the gRPC client lib

Once we have `protoc` installed, we can now generate our gRPC client library.

```
protoc --js_out=import_style=commonjs,binary:./src/proto --grpc-web_out=import_style=typescript,mode=grpcwebtext:./src/proto --proto_path=./Protos ./Protos/rtmap.proto
```

The command is quite lengthy. So, let's break it down.

We're basically telling `protoc` to generate the gRPC client library as a CommonJS module, and output the files to `/src/proto` (`--js_out=import_style=commonjs,binary:./src/proto`). Additionally, we want it to generate the service stub in TypeScript, use the `grpcwebtext` wire format mode, and output the files to `./src/proto` (`--grpc-web_out=import_style=typescript,mode=grpcwebtext:./src/proto`). 

We're also telling `protoc` where it can find any other `.proto` files we have referenced (`--proto_path=./Protos`) in the input `.proto` file, which is the final argument in the command (`./Protos/rtmap.proto`).

But why use `grpcwebtext`, which is simply base64-encoded string, instead of `grpcweb` which is binary? At the time of writing, `grpcweb` does not yet support binary server streaming. We will update to `grpcweb` once binary server streaming is supported.

We added the command to our `package.json` as an NPM script, because we knew we'd be running it several times during our development cycle.

### Hooking things up

Our front-end app runs on Vue.js, with Vuex at the core.


### NPM packages

* [google-protobuf][3]
* [grpc-web][4]

[1]: https://www.nuget.org/packages/Grpc.AspNetCore
[2]: https://www.nuget.org/packages/Grpc.AspNetCore.Web
[3]: https://www.npmjs.com/package/google-protobuf
[4]: https://www.npmjs.com/package/grpc-web
[5]: https://developers.google.com/protocol-buffers/
[6]: https://www.yonego.com/nl/why-milliseconds-matter/
[7]: https://blog.tech-fellow.net/2019/12/08/building-real-time-public-transport-tracking-system-on-azure-part1/
[8]: https://github.com/grpc/grpc-web/tree/master/packages/grpc-web#quick-start
[9]: https://docs.microsoft.com/en-us/aspnet/core/tutorials/grpc/grpc-start?view=aspnetcore-3.1&tabs=visual-studio
[10]: https://www.npmjs.com/package/protoc-gen-grpc
[11]: https://github.com/grpc/grpc-web#wire-format-mode
[12]: https://devblogs.microsoft.com/dotnet/an-introduction-to-system-threading-channels/
[13]: https://www.nuget.org/packages/Easy.MessageHub