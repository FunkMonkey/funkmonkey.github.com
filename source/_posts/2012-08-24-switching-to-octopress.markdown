---
layout: post
title: "Switching to Octopress"
date: 2012-08-24 22:49
comments: true
categories: [octopress, website]
---
This is more a test post than anything else.

Switched from Joomla to [Octopress](http://octopress.org/). I will transfer the old pages one by one.

Here are some things that I have learned.

#### Use 32-bit Python

Rubypython seems to depend on 32-bit Python. I had 64-bit Python installed, which gave me weird errors like

{% codeblock lang:text %}
'block in ffi_lib': Could not open library (LoadError)
{% endcodeblock %}

followed by "library not found" or some gibberish unicode characters.