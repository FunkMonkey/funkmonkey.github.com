---
layout: post
title: "C++11 auto and references"
date: 2012-01-01 19:08
comments: true
categories: [C++, programming, C++11]
---
I just started playing around with the `auto` keyword of the new C++ standard. I assumed a variable declared with `auto` would have the type of the according expression.

Though consider the following example:


{% codeblock Example of using auto lang:cpp %}
#include <vector>
 
using namespace std;
 
vector<int> data;
 
vector<int>& getData()
{
	return data;
}
 
int main( int argc, const char* argv[] )
{
	data.push_back(1);
 
	auto data2 = getData();
	data.push_back(2);
} 
{% endcodeblock %}

Looking at the variables in the debugger, you see that `data` contains the values `1` and `2`, whereas `data2` only contains `1`, thus `data2` is not the same object as `data`. I assumed `data2` was of type `vector<int>&` (the return type of `getData`), though it obviously just is `vector<int>`.

Declaring it as

{% codeblock lang:cpp %}
auto& data2 = getData();
{% endcodeblock %}

works though. `data2` is now a reference to `data`.

It makes sense, the standard defines it that way. Otherwise, how could you create a copy of the return value? Still it makes using `auto` a little more confusing ...