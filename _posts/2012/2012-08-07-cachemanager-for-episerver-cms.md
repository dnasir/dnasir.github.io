---
layout: post
title: CacheManager for EPiServer CMS
date: 2012-08-07 12:53
comments: true
categories: [EPiServer, episerver, nuget module, Web Development]
---
Geta has released yet another open source EPiServer CMS module. This latest module allows EPiServer administrators, as well as anyone with access rights, to manage the cache for EPiServer sites.

## Features

* **One-click cache clear**- Clear EPiServer site cache with just one click.
* **Selective cache entry deletion** - Select and delete individual cache entries.
* **Cache entry value display** - View the contents of cache entries.
* **Multiple server support** - Manage cache for multiple servers from a single node.

<!--more-->

## Screenshots

![Image of the CacheManager UI]({{ "/assets/img/2012/08/01.png" | absolute_url }} "Main UI")

![Image of the CacheManager UI]({{ "/assets/img/2012/08/02.png" | absolute_url }} "System stats UI")

![Image of the CacheManager UI]({{ "/assets/img/2012/08/03.png" | absolute_url }} "Load balanced server UI")

![Image of the CacheManager UI]({{ "/assets/img/2012/08/05.png" | absolute_url }} "Managing load balanced server")

## Installation

1. Add `http://nuget.episerver.com/feed/packages.svc` as a NuGet package source.
2. Install CacheManager for EPiServer via NuGet.
3. Rebuild your project.
4. Start managing your EPiServer site cache.

## Limitations

* Cache entry value display currently only displays a string representation of the cache entry.
* No paging is implemented yet, so cache entry list loading may be a bit slow when retrieving large cache entry lists.

[NuGet Package Link](http://nuget.episerver.com/en/OtherPages/Package/?packageId=Geta.CacheManager){:target="_blank"}

[Documentation](https://github.com/Geta/opensource/wiki/CacheManager-for-EPiServer-CMS){:target="_blank"}

[Source](https://github.com/Geta/opensource/tree/master/Geta.CacheManager){:target="_blank"}

Thanks to [Valdis Iljuconoks](http://world.episerver.com/System/Users-and-profiles/Community-Profile-Card/Valdis%20Iljuconoks/){:target="_blank"} for helping out with this release.
