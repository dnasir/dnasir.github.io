---
layout: post
title: "Real-time data streaming with gRPC - Part 1"
description: "The first part of my series on developing a gRPC endpoint for web applications."
comments: true
tags:
  - grpc
  - grpc-web
  - netcore
  - protobuf
---

According to Google's developer page, [Protocol Buffers][5] is defined as "Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data".

Protocol Buffers, or protobuf, allows data to be transferred between systems that are running on different frameworks and languages. For example, a .NET system can communicate with a system running Java using a common data format.

Sound familiar? That's because we've been doing this with REST using XML and JSON. So why gRPC? This is because when dealing with real-time data streaming of large data sets, gRPC has a good advantage over the traditional REST approach employed by most API services that use the XML and JSON data formats, as described in this [post][6].

In this series, I'll go through my process of setting up a simple real-time data streaming application that displays a list of the latest unconfirmed Bitcoin transactions with data retrieved from blockchain.com.

## The idea

* When a user subscribes, the server starts fetching data from blockchain.com at 1s intervals.
* Data is then streamed to the connected client for as long as the stream is not cancelled.
* The Vue app will update the list with the latest unconfirmed Bitcoin transactions.

## The setup




What do you need?

### NuGet packages

* [Grpc.AspNetCore][1]
* [Grpc.AspNetCore.Web][2]

### NPM packages

* [google-protobuf][3]
* [grpc-web][4]





### The proto file

```proto
syntax = "proto3";

option csharp_namespace = "LoremService";

package lorem;

service Lorem {
  rpc StartStream (LoremRequest) returns (stream LoremReply);
}

message LoremRequest {
}

message LoremReply {
  string message = 1;
}
```

### The server-side code

```csharp
namespace LoremService
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
            app.UseGrpcWeb();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapGrpcService<LoremService>().EnableGrpcWeb();

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

## References

* https://nilsmagnus.github.io/post/proto-json-sizes/


[1]: https://www.nuget.org/packages/Grpc.AspNetCore
[2]: https://www.nuget.org/packages/Grpc.AspNetCore.Web
[3]: https://www.npmjs.com/package/google-protobuf
[4]: https://www.npmjs.com/package/grpc-web
[5]: https://developers.google.com/protocol-buffers/
[6]: https://www.yonego.com/nl/why-milliseconds-matter/