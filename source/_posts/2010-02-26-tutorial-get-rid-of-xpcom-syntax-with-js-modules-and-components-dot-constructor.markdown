---
layout: post
title: "[Tutorial] Get rid of XPCOM syntax with JS Modules and Components.Constructor"
date: 2010-02-26 14:19
comments: true
categories: [Mozilla, JavaScript, XPCOM, tutorial]
---
In case you dislike the syntax of creating XPCOM components, you can use [Components.Constructor](https://developer.mozilla.org/en-US/docs/Components.Constructor) to achieve a more Javascript like syntax for object-creation. Combine this with [JS modules](https://developer.mozilla.org/en-US/docs/JavaScript_code_modules) and your constructors can be accessed from everywhere.
 
### MozXPCOM.jsm

{% codeblock lang:js %}
var EXPORTED_SYMBOLS = ["MOZ"];
 
const Cc = Components.classes;
const Ci = Components.interfaces;
const CCtor = Components.Constructor;
 
var MOZ =
{
	CON: {}
};
 
MOZ.CON.nsLocalFile     = "@mozilla.org/file/local;1";
MOZ.LocalFile           = CCtor(MOZ.CON.nsLocalFile, Ci.nsILocalFile);
 
MOZ.CON.nsMutableArray  = "@mozilla.org/array;1";
MOZ.MutableArray        = CCtor(MOZ.CON.nsMutableArray, Ci.nsIMutableArray);
{% endcodeblock %}

### Usage

{% codeblock lang:js %}
Components.utils.import("resource://myext/MozXPCOM.jsm");
 
// ...
 
var anArray = new MOZ.MutableArray();
{% endcodeblock %}

I prefer to put all constructors in a MOZ-object, so I don't have to export them all.