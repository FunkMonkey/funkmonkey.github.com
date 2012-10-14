---
layout: post
title: "Added basic TypeScript support to SublimeLinter"
date: 2012-10-14 14:30
comments: true
categories: [programming, plugin, Sublime]
---
I took a look at [SublimeLinter](https://github.com/SublimeLinter/SublimeLinter) and added basic TypeScript support, see [this pull request](https://github.com/SublimeLinter/SublimeLinter/pull/259).

Sublime can now show errors from TypeScript files, but it is still quite problematic when working with bigger TypeScript projects, as every lint operation will reparse everything (including referenced files) - thus there is still a lot to be done, but it is a start.