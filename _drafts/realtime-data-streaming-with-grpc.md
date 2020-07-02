---
layout: post
title: "Real-time web applications with gRPC"
description: "The first part of my series on developing a gRPC endpoint for web applications."
comments: true
tags:
  - grpc
  - grpc-web
  - netcore
  - protobuf
---

In this post, I'll be collaborating with my colleague, Valdis Iljuconoks, on the topic of real-time data distribution using gRPC. If you haven't done so already, head over to Valdis' blog for a multi-part series on ["Building Real-time Public Transport Tracking System on Azure"][7] for details on setting up the Azure infrastructure for this project. This post focuses on the client distribution system.

Let's get started.

## Background

Our team has been involved in developing real-time tracking and monitoring tools for a public transportation company for the past couple of years. During that time, we've experimented with many different technologies and concepts in our quest to find the best solution for this project.

However, as a developer, I don't believe there is such a thing as the perfect solution. As technology continues to improve, new concepts and services render existing solutions obsolete.

One of the many technologies we have experimented with is gRPC. You can read about it [here](https://grpc.io/about/).

In short, gRPC allows data to be transferred between systems that are running on completely different frameworks, languages, and even environments. For example, a .NET system can communicate with a system running Java via a common data format.

Sound familiar? That's because we've been doing this with REST using XML and JSON. So why gRPC? This is because when dealing with real-time data streaming of large data sets, gRPC has a good advantage over the traditional REST approach employed by most API services that use the XML and JSON data formats, as described in this [post][6].

## Architecture

The idea is pretty simple - push the data we receive from Azure down to all connected clients.

// TODO: insert architectural diagram here

Azure -> API Controller -> SnapshotReceiverBuffer <- onNewItem.Invoke -> 

Since we already have the data service set up, all that's left to do is to set up the mechanism to broadcast to all connected clients.

## The server

Since this is a .Net Core gRPC project, we needed to set up a gRPC service project. Thankfully, Microsoft has posted a [tutorial][9] on this. It covers setting up both server and client, but we were only interested in the server part, since we weren't planning on setting up a .Net gRPC client.

### Updating the `.proto` file

The `.proto` file that came with the standard gRPC template is not really useful for anything more than a simple demo. So we updated it.

```
syntax = "proto3";

option csharp_namespace = "GrpcService";

package rtmap;

service RTMap {
  rpc StartStream (StartStreamRequest) returns (stream StartStreamReponse);
}

message StartStreamRequest {
}

message StartStreamReponse {
  repeated Marker data = 1;
}

message Marker {
  string id = 1;
  LatLng position = 2;
}

message LatLng {
    double lat = 1;
    double lng = 2;
}
```

While the generator should have set everything up correctly, it's worth making sure that the project is set to build the `.proto` file in `Server` mode.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  ... project stuff

  <ItemGroup>
    <Protobuf Include="Protos\rtmap.proto" GrpcServices="Server" />
  </ItemGroup>

  ... more project stuff

</Project>
```

### Configuring the server

Next, we configured the server to accept web clients. This was done by first installing the [Grpc.AspNetCore.Web][2] package from NuGet, and updating the `Startup.cs` file so it activates the middleware.

```csharp
namespace GrpcServiceApp
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddGrpc();
        }

        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseRouting();
            // This activates the gRPC middleware
            // This must be after routing and before endpoints
            app.UseGrpcWeb();

            app.UseEndpoints(endpoints =>
            {
                // This tells it which service to allow web clients for
                endpoints.MapGrpcService<GrpcService>().EnableGrpcWeb();

                endpoints.MapGet("/",
                    async context =>
                    {
                        await context.Response.WriteAsync(
                            "Communication with gRPC endpoints must be made through a gRPC client. To learn how to create a client, visit: https://go.microsoft.com/fwlink/?linkid=2086909");
                    });
            });
        }
    }
}
```

That's it for the configuration code.

### Implementing the gRPC service methods

After compiling the project, we now have access to a generated class that our GrpcService should inherit from called `RTMap`. This class is regenerated every time you rebuild the project after making changes to the `.proto` file.

The next step was to implement our gRPC methods, and it's as simple as overriding the methods that were automatically generated from when we built the project.

```csharp
public class GrpcService : RTMap.RTMapBase
{
    public override async Task StartStream(
        StartStreamRequest request,
        IServerStreamWriter<StartStreamResponse> responseStream,
        ServerCallContext context)
    {
        // do stuff here
    }
}
```

All the gRPC streaming demos I've seen demonstrates how I should go about streaming data to the client, given that there is an end to the data, by asynchronously writing to the `responseStream` parameter.

For example;

```csharp
public override async Task StartStream(
    StartStreamRequest request,
    IServerStreamWriter<StartStreamResponse> responseStream,
    ServerCallContext context)
{
    var i = 0;
    while (!context.CancellationToken.IsCancellationRequested && i++ < 10)
    {
        await responseStream.WriteAsync($"Dataset {i}");
    }
}
```

But we needed the connection the remain open indefinitely, since we there isn't an end to the data we wanted to stream.

The fastest (read: naive) way to this way to do this is by simply setting up a while loop that would only exit when the `IsCancellationRequested` flag is true. Like so;

```csharp
public override async Task StartStream(
    StartStreamRequest request,
    IServerStreamWriter<StartStreamResponse> responseStream,
    ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        var data = await _dataService.getData();
        var response = new StartStreamReponse() {
          Data = data
        };
        await responseStream.WriteAsync(response);

        // wait 1s before sending next dataset
        await Task.Delay(1000);
    }
}
```

What this is supposed to do is keep the connection open and stream data once every second until the connection is closed.
 
The `Task.Delay()` is there to prevent the service from spamming the data service and the client with data. This approach worked, but it would mean the data is not really "real-time" in the practical sense, since there's a chance new data could have arrived in between the intervals and the client would have to wait for the next interval to receive the data.

Ideally, the data service should determine when new data is broadcasted.

The next problem with this approach is much more severe, and it's one that would bring the entire system down to its knees and beg for mercy.

We later discovered that for every client connecting to the gRPC endpoint, a new instance of the gRPC service was created, and by extension, a new `while` loop. Task Manager showed a bump in CPU usage by as much as 20% for every new client.

Needless to say, this will inevitably kill the hamsters powering our servers.

We wanted to make the endpoint available to hundreds, if not thousands, of clients. Not five.

// TODO: add some screenshots of the task manager

// TODO: stop here for this part?

The solution? Async/await for the cancellation token instead.

Valdis came up with a pretty simple extension method that allowed us to wait for the cancellation token, rendering the while loop redundant.

```csharp
public static class CancellationTokenExtensions
{
    public static async Task WaitUntilCancelled(this CancellationToken cancellationToken)
    {
        var taskCompletionSource = 
          new TaskCompletionSource<object>(TaskCreationOptions.RunContinuationsAsynchronously);

        using (
            cancellationToken.Register(
                state => { ((TaskCompletionSource<object>) state).TrySetResult(null); }, 
                taskCompletionSource,
                false
            )
        )
        {
            await taskCompletionSource.Task.ConfigureAwait(false);
        }
    }
}
```

Next, we updated our gRPC service code to utilise the new extension method.

```csharp
public override async Task StartStream(
    StartStreamRequest request,
    IServerStreamWriter<StartStreamResponse> responseStream,
    ServerCallContext context)
{
    try
    {
        await context.CancellationToken.WaitUntilCancelled();
    }
    catch (InvalidOperationException)
    {
        // client disconnected
    }
}
```

What this allows us to do now is asynchronously wait for the cancellation token to be called, and in the meantime not take up any CPU cycles like `while` loops do.

But now we have to handle the data streaming a bit differently to how we did in the previous example with the `while` loop.

## Part 2 - Using Channel





## The client

Setting up the back end was just half the story. Let's move onto the front end stuff.

### Prerequisites

We went with a project called [`grpc-web`][4] for our front end implementation. However, there are a few things that needed to be set up in order to get the whole thing running.

Firstly, we had to install a protobuf compiler, which is used to generate the client code needed to communicate with the gRPC endpoint. Fortunately, the good devs have provided a [quick start guide][8] on how to install `protoc` and `protoc-gen-grpc-web` on your development machine.

### Generating the gRPC client lib

Once we have `protoc` installed, we can now generate our gRPC client library.

```
protoc --js_out=import_style=commonjs,binary:./src/proto --grpc-web_out=import_style=typescript,mode=grpcwebtext:./src/proto --proto_path=./Protos ./Protos/rtmap.proto
```

The command is quite lengthy. So, let's break it down.

We're basically telling `protoc` to generate the gRPC client library as a CommonJS module, and output the files to `/src/proto` (`--js_out=import_style=commonjs,binary:./src/proto`). Additionally, we want it to generate the service stub in TypeScript, use the `grpcwebtext` wire format mode, and output the files to `./src/proto` (`--grpc-web_out=import_style=typescript,mode=grpcwebtext:./src/proto`). We're also telling `protoc` where it can find any other `.proto` files we have referenced (`--proto_path=./Protos`) in the input `.proto` file, which is the final argument in the command (`./Protos/rtmap.proto`).

But why use `grpcwebtext`, which is simply base64-encoded string, instead of `grpcweb` which is binary? At the time of writing, `grpcweb` does not yet support server streaming calls. We will update to `grpcweb` once server streaming calls become supported.

We added the command to our `package.json` as an NPM script, because we knew we'd be running it several times during our development.


### NPM packages

* [google-protobuf][3]
* [grpc-web][4]


## References

* https://nilsmagnus.github.io/post/proto-json-sizes/


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