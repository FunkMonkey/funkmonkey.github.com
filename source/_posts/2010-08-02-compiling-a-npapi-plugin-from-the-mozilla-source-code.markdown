---
layout: post
title: "Compiling a NPAPI-Plugin from the Mozilla source-code"
date: 2010-08-02 16:50
comments: true
categories: [Mozilla, programming, NPAPI, plugin, C++]
---

Some days ago I tried to compile the basic NPAPI-Plugin from the Mozilla codebase and of course ran into some problems that could easily be solved, though finding the answers wasn't that easy.
 
Here's the first one, when compiling:

{% codeblock lang:text %}
c:\program files\microsoft sdks\windows\v7.0\include\winnt.h(6361): error C2146: syntax error : missing ';' before identifier 'ContextRecord'
{% endcodeblock %}

Huh? `winnt.h`. Shouldn't change something in there. I googled a little and [someone](http://www.apijunkie.com/APIJunkie/blog/post/2008/09/22/Building-Firefox-Plugins-using-Visual-Studio.aspx) knew the problem. He suggested to alter `npplat.h` and move some includes after the include of `windows.h`. Though this will work, it doesn't actually sound like something you're supposed to do. So I digged further and checked out the [Gecko-Win32-Plugin-Samples-Package](https://developer.mozilla.org/@api/deki/files/3844/=GeckoPluginSDK-samplesWin32.zip) that you can get at [MDC](https://developer.mozilla.org/en-US/docs/Plugins/Samples_and_Test_Cases). The basic plugin in there compiled fine, which confused me a little, so I diffed the project-files and there I found the solution: The project-file from comm-central missed the preprocessor-tag `_X86_`.
 
I used some XPCOM-Components in my plugin, which lead to another compile-error:

{% codeblock lang:text %}
nsidomnode.h(94) : error C2059: syntax error : 'constant'
{% endcodeblock %}
 
I found [someone](http://osdir.com/ml/mozilla.devel.dom/2003-10/msg00059.html) in the net, having the same problem. Seems there is a `#define GetNextSibling GetWindow` somewhere in `windows.h`, which collides with the method-declaration of `GetNextSibling` in `nsidomnode.h`. So I undef'ed it after including `windows.h` and got the problem solved.