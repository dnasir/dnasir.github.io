---
layout: post
title: CacheManager for EPiServer CMS
date: 2012-08-07 12:53
comments: true
categories: [EPiServer, episerver, nuget module, Web Development]
---
Geta has released yet another open source EPiServer CMS module. This latest module allows EPiServer administrators, as well as anyone with access rights, to manage the cache for EPiServer sites.
<h3>Features</h3>
<ul>
	<li><strong>One-click cache clear </strong>- Clear EPiServer site cache with just one click.</li>
	<li><strong>Selective cache entry deletion</strong> - Select and delete individual cache entries.</li>
	<li><strong>Cache entry value display</strong> - View the contents of cache entries.</li>
	<li><strong>Multiple server support</strong> - Manage cache for multiple servers from a single node.</li>
</ul>

<!--more-->

<h3>Screenshots</h3>
[gallery link="file" columns="4"]
<h3>Installation</h3>
<ol>
	<li>Add http://nuget.episerver.com/feed/packages.svc as a NuGet package source.</li>
	<li>Install CacheManager for EPiServer via NuGet.</li>
	<li>Rebuild your project.</li>
	<li>Start managing your EPiServer site cache.</li>
</ol>
<h3>Limitations</h3>
<ul>
	<li>Cache entry value display currently only displays a string representation of the cache entry.</li>
	<li>No paging is implemented yet, so cache entry list loading may be a bit slow when retrieving large cache entry lists.</li>
</ul>
<div></div>
<a href="http://nuget.episerver.com/en/OtherPages/Package/?packageId=Geta.CacheManager" target="_blank">NuGet Package Link</a><a href="https://github.com/Geta/opensource/wiki/CacheManager-for-EPiServer-CMS" target="_blank">
Documentation
</a><a href="https://github.com/Geta/opensource/tree/master/Geta.CacheManager" target="_blank">Source</a>

Thanks to <a href="http://world.episerver.com/System/Users-and-profiles/Community-Profile-Card/Valdis%20Iljuconoks/" target="_blank">Valdis Iljuconoks</a> for helping out with this release.
