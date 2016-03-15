---
layout: post
title: "Keywords Mix"
modified:
categories: 
description:
tags: [csharp keywords]
image:
  feature: header5.jpg
  credit: Basel, Rhein
comments:
share:
date: 2016-03-15T00:45:43+01:00
---
<style type="text/css">
	pre {
		width: 100%;
		border: none;
	}
	figure {
		padding: 0px;
	}
</style>

I got recently caught up with the paper work and came accross some fun notes from years ago! Couldn't wait to share this contextual keywords mix, the courtesy of [@v2_matveev](https://twitter.com/v2_matveev):

{% highlight csharp linenos %}
partial class where<partial> where partial: where<partial>
{
	dynamic dynamic<yield>(dynamic dynamic)
	{
		return dynamic<yield>(dynamic);
	}
}
{% endhighlight %}

Yes, it compiles :) 