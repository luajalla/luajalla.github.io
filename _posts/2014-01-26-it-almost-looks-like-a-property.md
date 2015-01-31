---
layout: post
title: It almost looks like a property
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 2622
wordpress_url: http://luajalla.azurewebsites.net/?p=2622
date: '2014-01-26 22:31:30 +0100'
date_gmt: '2014-01-26 22:31:30 +0100'
image:
  feature: header5.jpg
  credit: Basel, Rhein
tags:
- csharp
comments: []
---
*Based on a true story. Only the names, places, and events have been changed.* 

![Character]({{ site.url }}/images/character.jpg)
{: .image-right}

Many people today forget that the tools are just tools. Doesn't matter if it's OO or functional programming, take any language: there're plenty of ways to write awesome and even more - to write terrifying code. 
````lets . compose . these . functions . until . your . type-checker````  
````  . explodes . btw . did . you . save . everything?````  

*[Originally posted [here](http://luajalla.azurewebsites.net/it-almost-looks-like-a-property/).]*  


Today I'll share a simple code-reducing trick, which I came up with a couple of years ago, but still like it for some reason (if you don't - just deal with it). So what the problem is?  

*Now answer the question - Do you believe there might be anything except the classes?*  
*(No)* No problem. At all. There's a class, with a bunch of fields. <a href="#theend">The end.</a>  
*(Yes)* Ok, let's say the objects of this class suppose to describe the weather conditions. Moving on,  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">class</span><span class="i"> Weather</span>
<span class="o">{</span>
    <span class="k">public double</span><span class="i"> Temp <span class="o">{</span> get; set; <span class="o">}</span></span>
    <span class="k">public double</span><span class="i"> Humidity <span class="o">{</span> get; set; <span class="o">}</span></span>
    <span class="k">public double</span><span class="i"> Pressure <span class="o">{</span> get; set; <span class="o">}</span></span> 
    <span class="k">public double</span><span class="i"> Rainmm <span class="o">{</span> get; set; <span class="o">}</span></span>
<span class="o">}</span></pre></td></tr></table>   

You may say, the temperature is usually reported as a range. Here we go:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">class</span><span class="i"> Weather</span>
<span class="o">{</span>
    <span class="k">public double</span><span class="i"> MinTemp <span class="o">{</span> get; set; <span class="o">}</span></span>
    <span class="k">public double</span><span class="i"> MaxTemp <span class="o">{</span> get; set; <span class="o">}</span></span>
    <span class="k">public double</span><span class="i"> WindChill <span class="o">{</span> get; set; <span class="o">}</span></span> 
    <span class="i">... </span><span class="c">// other properties</span>
<span class="o">}</span></pre></td></tr></table>  

Nice, but there're also different observations for the night/morning/afternoon/evening forecasts, which one may want to include into the same forecast:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">class</span><span class="i"> Weather</span>
<span class="o">{</span>
    <span class="k">public double</span><span class="i"> MinNightTemp <span class="o">{</span> get; set; <span class="o">}</span></span>
    <span class="k">public double</span><span class="i"> MinDayTemp <span class="o">{</span> get; set; <span class="o">}</span></span>
    <span class="i">... </span><span class="c">// other properties</span>
<span class="o">}</span></pre></td></tr></table>   

Now add several predictions for future dates, whatever. On a rainy day you may find out the number of fields somehow approached fifty, \*sigh\*, add another one and forget about them.  

And then the Universe decides that it was not fun enough and you should make the predictions more consistent with reality (or at least pretend to do so), for example, by including the information from other sources like <*insert your favorite weather forecast website here, I'd better take an umbrella anyway*>. You quickly run an experiment with the min/max temperatures and expect to get a 10% improvement in accuracy by weighting the observations from different sources... But there're more than 50 properties already, remember?  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
</pre></td><td class="snippets">
<pre class="fssnip"><span class="k">var</span><span class="i"> w1 </span><span class="o">=</span><span class="n"> 0.8</span><span class="i">;</span>
<span class="k">var</span><span class="i"> w2 </span><span class="o">=</span><span class="n"> 1 </span><span class="o">-</span><span class="i"> w1;</span>
<span class="k">var</span><span class="i"> result </span><span class="o">=</span><span class="k"> new </span><span class="i">Weather </span>
<span class="o">{</span> 
    <span class="i">MinNightTemp </span><span class="o">=</span><span class="i"> w1 </span><span class="o">*</span><span class="i"> orig.MinNightTemp </span><span class="o">+</span><span class="i"> w2 </span><span class="o">*</span><span class="i"> ext.MinNightTemp,</span> 
    <span class="i">MaxNightTemp </span><span class="o">=</span><span class="i"> w1 </span><span class="o">*</span><span class="i"> orig.MaxNightTemp </span><span class="o">+</span><span class="i"> w2 </span><span class="o">*</span><span class="i"> ext.MaxNightTemp,</span>  
    <span class="i">... </span><span class="c">// oh, why is that intern on vacation now? who will fill in all this stuff?</span>
<span class="o">}</span></pre></td></tr></table>  

It could be anything else, the key is that there's a set of operations, similar for all the fields, and the fields are somewhat similar too - in our case, they're all of type ````double````.  

Let's create an array instead, where the fields are the elements of this array:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
</pre></td><td class="snippets">
<pre class="fssnip"><span class="l">1: </span><span class="k">for</span><span class="i">(</span><span class="k">int</span><span class="i"> i </span><span class="o">=</span><span class="n"> 0</span><span class="i">; i <span class="o">&lt;</span><span class="i"> n; </span><span class="o">++</span><span class="i">i)</span>
<span class="l">2: </span>    <span class="i">x[i] </span><span class="o">=</span><span class="i"> w1 </span><span class="o">*</span><span class="i"> y[i] </span><span class="o">+</span><span class="i"> w2 </span><span class="o">*</span><span class="i"> z[i];</span>
</span></pre></td></tr></table>  

But... but now we don't know what is what. Ok, here's the answer:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
</pre></td><td class="snippets">
<pre class="fssnip">
<span class="k">enum</span><span class="i"> P </span>
<span class="o">{</span>
    <span class="i">MinNightTemp,</span>
    <span class="i">MaxNightTemp,</span>
    <span class="i">...</span>
<span class="o">}</span>
<span class="i">...</span>   
<span class="k">var</span><span class="i"> res </span><span class="o">=</span><span class="i"> x[(</span><span class="k">int</span><span class="i">)P.MinNightTemp];</span></pre></td></tr></table>  

What about a kind of vector? It looks even better:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
</pre></td><td class="snippets">
<span class="k">var</span><span class="i"> x </span><span class="o">=</span><span class="i"> w1 </span><span class="o">*</span><span class="i"> y </span><span class="o">+</span><span class="i"> w2 </span><span class="o">*</span><span class="i"> z;</span>   
<span class="k">var</span><span class="i"> res </span><span class="o">=</span><span class="i"> x[P.MinNightTemp]; </span><span class="c">// compare to x.MinNightTemp</span></pre></td></tr></table>  

- Saves a lot of typing, decreses the probability of potential mistakes;  
- You don't need even a dictionary - an array is already enough;  
- The enum part can help to preserve the order (in original case that was mandatory requirement), nothing breaks if you add a new feature somewhere in the middle;  
- Every field automatically has its own underlying value, you avoid the mistake of assigning the same value to the different constants (e.g. ````MinTempInd = 42```` and ````MaxTempInd = 42````);  
- There're good chances no one will ever notice, that ````Weather```` is not a separate class any more. *It looks almost like a property*.  
<p>&nbsp;</p>
<h4 id="theend">Instead of conclusion</h4>  

There're simple yet annoying problems, and to solve them you don't need a dataframe or any complicated data structure or even a separate class. C# is not that bad in the end, no one forces you to write only overly-OOP code.  

That doesn't mean you can't come up with more concise solutions using a different language or solving a different problem. Only that there're ways - in everything - to do the familiar things differently, the ways not everyone notices.  

<p>&nbsp;</p>
