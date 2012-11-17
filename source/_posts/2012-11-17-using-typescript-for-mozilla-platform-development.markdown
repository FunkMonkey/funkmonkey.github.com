---
layout: post
title: "Using TypeScript for Mozilla platform development"
date: 2012-11-17 18:35
comments: true
categories: [Mozilla, JavaScript, TypeScript, CommonJS, ES6, Harmony]
---

Recently I started converting [Loomo](https://github.com/FunkMonkey/Loomo) to [TypeScript](http://www.typescriptlang.org/). Results are currently in the [typescript](https://github.com/FunkMonkey/Loomo/tree/typescript-support) branch. 

## JS code modules

The most challenging part was the support for Mozilla's [JS code modules](https://developer.mozilla.org/en-US/docs/JavaScript_code_modules), as they don't fit well with the two module formats TypeScript can generate - CommonJS and AMD.

I created a module (in plain JS) that provides functionality for writing a JS code module using CommonJS syntax (the format TS compiles to).

It does two important things:

* it gives the module a `require` function that allows loading of relatively located modules
* it turns exported objects (`exports.foo`) into elements of `EXPORTED_SYMBOLS`

{% codeblock CommonJS.jsm lang:js %}
var EXPORTED_SYMBOLS = ["initCommonJSModule"];

/**
 * Initializes the global object of a Mozilla JS Module to be a CommonJS module
 *    - adds require and exports
 *
 * @param  {Object}   global   Global object of Mozilla JS module
 */
function initCommonJSModule(global){

	// adding CommonJS require
	global.require = function require(path) {
			if (!("__URI__" in global))
			  throw Error("require may only be used from a JSM, and its first argument "+
						  "must be that JSM's global object (hint: use this)");
			let uri = global.__URI__;
			let i = uri.lastIndexOf("/");
			var res = {};
			Components.utils.import(uri.substring(0, i+1) + path + ".js", res);
			return res;
		};
	
	// adding the symbols needes for Mozilla JS modules
	global.EXPORTED_SYMBOLS = [];
	
	// Proxy handler used for CommonJS exports that will automatically adjust EXPORTED_SYMBOLS
	var handler = {
		set: function(target, name, value){
			if(global.EXPORTED_SYMBOLS.indexOf(name) === -1)
				global.EXPORTED_SYMBOLS.push(name);
			target[name] = value;
			return true;
		}
	};
	
	// adding CommonJS exports
	global.exports = new Proxy(global, handler);
	global.module = {};
	global.module.exports = global.exports;
}
{% endcodeblock %}

All you need to do, is to include the JS module in your TypeScript file.

{% codeblock SomeFile.ts lang:js %}
// Boilerplate for hacking support JS modules in TypeScript
///<reference path='../../Moz.d.ts' />
Components.utils.import("chrome://path/to/CommonJS.jsm");
initCommonJSModule(eval('this'));

import Bar = module("../Bar");

export class Foo {
	
}
{% endcodeblock %}

Relative file loading in `require` uses the `__URI__` property every JS code module provides and which is also used in the implementation of [`XPCOMUtils.importRelative`](https://developer.mozilla.org/en-US/docs/JavaScript_code_modules/XPCOMUtils.jsm#importRelative).

There are some things to consider.

You need to provide declarations for Mozilla specific APIs like `Components.xxx`, which I do provide in [`MozGlobals.d.ts`](https://github.com/FunkMonkey/Loomo/blob/typescript-support/Loomo/chrome/content/MozGlobals.d.ts) (which is referenced by `Moz.d.ts`). You will also have to provide the declaration for `initCommonJSModule`, otherwise TypeScript won't compile. Doh!

Also TypeScript does not support using `this` in global scope, but it is the only way to access the global object of the module and thus needed. I had to hack it using `eval(this)`. I considered using a function which simply returns `this`, but I think this will be problematic in [strict mode](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Functions_and_function_scope/Strict_mode).

One big caveat is still there: importing modules from other contexts (like "chrome://browser" or extensions) can't be done, as `require` can only import relative to the source file. You will have to use `Components.utils.import` and provide TypeScript declarations.

## Some remarks: Mozilla specifics, ES6/Harmony features and host objects

As I already said, you will have to provide declarations for all Mozilla specific APIs. This includes APIs and functions that will appear with ES6/Harmony, which shouldn't be too complicated.

Really problematic are ES6/Harmony's syntactic changes. TypeScript currently does not support `let` and `const`. I opened a bug [report for it](https://typescript.codeplex.com/workitem/368), but I don't see it happening in the near future. I'm not sure about generators and `yield` either.

Also, [for now](http://typescript.codeplex.com/workitem/13), host objects like `Array` can not be extended.

## Conclusion

Apart from some problems here and there and missing features of ES6/Harmony, TypeScript conversion went pretty smoothly.






