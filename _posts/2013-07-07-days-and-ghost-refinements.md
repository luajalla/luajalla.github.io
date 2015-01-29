---
layout: post
status: publish
published: true
title: Days and Ghost Refinements
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 1512
wordpress_url: http://luajalla.azurewebsites.net/?p=1512
date: '2013-07-07 16:26:50 +0200'
date_gmt: '2013-07-07 16:26:50 +0200'
image:
  feature: header3.jpg
  credit: Geneva Lake
tags:
- fstar
- refinements
- Excel
comments:
- id: 152
  author: 'F# Weekly #27 2013 | Sergey Tihon&#039;s Blog'
  author_email: ''
  author_url: http://sergeytihon.wordpress.com/2013/07/08/f-weekly-27-2013/
  date: '2013-07-07 21:05:06 +0200'
  date_gmt: '2013-07-07 21:05:06 +0200'
  content: '[...] Natallie Baikevich blogged &#8220;Days and Ghost Refinements&#8220;.
    [...]'
---
Let's look at the simple function, which calculates the number of days between dates, when there're 30 days in a month (and 360 in a year).  
  
*[Originally posted [here](http://luajalla.azurewebsites.net/days-and-ghost-refinements).]*

Something like this F# code:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
</pre></td>
<td class="snippets"><pre class="fssnip">
<span class="k">let</span> <span class="i">days360</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="o">=</span>
    (<span class="i">ey</span> <span class="o">-</span> <span class="i">sy</span>)<span class="o">*</span><span class="n">360</span> <span class="o">+</span> (<span class="i">em</span> <span class="o">-</span> <span class="i">sm</span>)<span class="o">*</span><span class="n">30</span> <span class="o">+</span> (<span class="i">ed</span> <span class="o">-</span> <span class="i">sd</span>)
</pre></td></tr></table>  

We can even write a bunch of tests to be sure the function works:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
<span class="l"> 9: </span>
<span class="l">10: </span>
<span class="l">11: </span>
<span class="l">12: </span>
</pre></td>
<td class="snippets"><pre class="fssnip">
<span class="k">let</span> <span class="i">tests</span> <span class="o">=</span> [
    <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">2</span>,<span class="n">29</span>), <span class="i">DateTime</span>(<span class="n">2013</span>,<span class="n">2</span>,<span class="n">28</span>), <span class="n">359</span>
    <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">2</span>,<span class="n">29</span>), <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">3</span>,<span class="n">01</span>), <span class="n">2</span>
    <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">2</span>,<span class="n">29</span>), <span class="i">DateTime</span>(<span class="n">2013</span>,<span class="n">3</span>,<span class="n">01</span>), <span class="n">362</span>
    <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">3</span>,<span class="n">01</span>), <span class="i">DateTime</span>(<span class="n">2013</span>,<span class="n">3</span>,<span class="n">01</span>), <span class="n">360</span>
    <span class="i">DateTime</span>(<span class="n">2011</span>,<span class="n">2</span>,<span class="n">28</span>), <span class="i">DateTime</span>(<span class="n">2013</span>,<span class="n">2</span>,<span class="n">28</span>), <span class="n">720</span>
    <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">5</span>,<span class="n">31</span>), <span class="i">DateTime</span>(<span class="n">2012</span>,<span class="n">7</span>,<span class="n">31</span>), <span class="n">60</span>
]

<span class="c">//</span><span class="c">true</span>
<span class="i">tests</span> <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">forall</span>(<span class="k">fun</span> (<span class="i">s</span>,<span class="i">e</span>,<span class="i">res</span>) <span class="k">-&gt;</span>
    <span class="i">days360</span> <span class="i">s</span><span class="o">.</span><span class="i">Year</span> <span class="i">s</span><span class="o">.</span><span class="i">Month</span> <span class="i">s</span><span class="o">.</span><span class="i">Day</span> <span class="i">e</span><span class="o">.</span><span class="i">Year</span> <span class="i">e</span><span class="o">.</span><span class="i">Month</span> <span class="i">e</span><span class="o">.</span><span class="i">Day</span> <span class="o">=</span> <span class="i">res</span>)</pre></td></tr></table>  
  

As you see ````days360```` doesn't take .NET ```DateTime```` as parameters, but ints. Would be nice to check them. Say, a month can take value from 1 to 12 - so what we need is a refinement type. F* supports two types of refinements: concrete and ghost. Here is how they are defined in “Secure Distributed Programming with Value-Dependent Types” <a title="Secure Distributed Programming with Value-Dependent Types" href="http://research.microsoft.com/apps/pubs/default.aspx?id=141708" target="_blank">paper</a>:

> **Concrete refinements** are pairs representing a value and a proof term serving as a logical evidence of the refinement property, similar to those in Coq and Fine.  

> **Ghost refinements** are used to state specifications for which proof terms are not maintained at run time. Ghost refinements have the form $$x:t\{\phi\}$$ where $$x$$ is a value variable, $$t$$ is a type, and $$\phi$$ is a logical formula, itself represented as a type that must have kind $$E$$ and may depend on $$x$$ and other in-scope variables. Ghost refinements provide the following benefits:  
- they enable precise symbolic models for many cryptographic patterns and primitives, and evidence for ghost refinement properties can be constructed and communicated using cryptographic constructions, such as digital signatures;  
- they benefit from a powerful subtyping relation: $$x:t\{\phi\}$$ is a subtype of $$t$$; this structural subtyping is convenient to write and verify higher-order programs;  
- they provide precise specification to legacy code without requiring any modifications;
- when used in conjunction with concrete refinements, they support selective erasure and dynamic reconstruction of evidence, enabling a variety of new applications and greatly reducing the performance penalty for runtime proofs.


Here we use the numbers as an example, because they are simple and intersections/unions of types are obvious. Note that we don't verify that the dates are entirely valid. This is an artificial example - small enough to try it online =).

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
<span class="l"> 9: </span>
<span class="l">10: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">type</span> <span class="i">year</span> <span class="o">=</span> <span class="i">x</span><span class="o">:</span><span class="i">int</span>{ <span class="n">1900</span> <span class="o">&lt;=</span> <span class="i">x</span> <span class="o">/</span><span class="o">\</span> <span class="i">x</span> <span class="o">&lt;=</span> <span class="n">9999</span> }
<span class="k">type</span> <span class="i">month</span> <span class="o">=</span> <span class="i">x</span><span class="o">:</span><span class="i">int</span>{ <span class="n">1</span> <span class="o">&lt;=</span> <span class="i">x</span> <span class="o">/</span><span class="o">\</span> <span class="i">x</span> <span class="o">&lt;=</span> <span class="n">12</span> }
<span class="k">type</span> <span class="i">day</span> <span class="o">=</span> <span class="i">x</span><span class="o">:</span><span class="i">int</span>{ <span class="n">1</span> <span class="o">&lt;=</span> <span class="i">x</span> <span class="o">/</span><span class="o">\</span> <span class="i">x</span> <span class="o">&lt;=</span> <span class="n">31</span> }  

<span class="k">val</span> <span class="i">days360</span><span class="o">:</span> <span class="i">year</span> <span class="k">-&gt;</span> <span class="i">month</span> <span class="k">-&gt;</span> <span class="i">day</span> <span class="k">-&gt;</span> <span class="i">int</span>

<span class="k">let</span> <span class="i">days360</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="o">=</span>
    (<span class="i">ey</span> <span class="o">-</span> <span class="i">sy</span>)<span class="o">*</span><span class="n">360</span> <span class="o">+</span> (<span class="i">em</span> <span class="o">-</span> <span class="i">sm</span>)<span class="o">*</span><span class="n">30</span> <span class="o">+</span> (<span class="i">ed</span> <span class="o">-</span> <span class="i">sd</span>)

<span class="k">let</span> _ <span class="o">=</span> <span class="i">days360</span> <span class="n">2013</span> <span class="n">6</span> <span class="n">1</span> <span class="n">2013</span> <span class="n">6</span> <span class="n">42</span></pre></td>
</tr></table>  

We run F* - and get a type check time failure!  

<table class="pre"><tr><td class="snippet">
<pre class="fssnip"><span class="i">input</span>(<span class="n">12</span>,<span class="n">8</span><span class="o">-</span><span class="n">12</span>,<span class="n">34</span>) <span class="o">:</span> <span class="i">Error</span> <span class="o">:</span> <span class="i">Expected</span> <span class="i">an</span> <span class="i">expression</span> <span class="i">of</span> <span class="i">type</span><span class="o">:</span>
<span class="i">x_5_1</span><span class="o">:</span><span class="i">int</span>{((<span class="i">LTE</span> <span class="n">1</span> <span class="i">x_5_1</span>) <span class="o">&amp;&amp;</span> (<span class="i">LTE</span> <span class="i">x_5_1</span> <span class="n">31</span>))}
<span class="i">but</span> <span class="i">got</span> (<span class="n">42</span>)<span class="o">:</span> <span class="i">int</span>
<span class="i">Type</span> <span class="i">checking</span> <span class="i">failed</span></pre></td></tr></table>  

Well, 12 was supposed to be here:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">let</span> _ <span class="o">=</span> <span class="i">days360</span> <span class="n">2013</span> <span class="n">6</span> <span class="n">1</span> <span class="n">2013</span> <span class="n">6</span> <span class="n">12</span> <span class="c">//</span><span class="c">ok</span></pre></td></tr></table>  

But wait, there're still 31-day months... and February. With EU 30/360 convention 31 is simply replaced with 30, so we modify the function:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
<span class="l"> 9: </span>
<span class="l">10: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span class="i">adjDay</span> <span class="i">d</span> <span class="o">=</span> <span class="k">if</span> <span class="i">d</span> <span class="o">=</span> <span class="n">31</span> <span class="k">then</span> <span class="n">30</span> <span class="k">else</span> <span class="i">d</span>

<span class="k">let</span> <span class="i">days360</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="o">=</span>
    <span class="k">let</span> <span class="i">sd</span>, <span class="i">ed</span> <span class="o">=</span> <span class="i">adjDay</span> <span class="i">sd</span>, <span class="i">adjDay</span> <span class="i">ed</span>
    (<span class="i">ey</span> <span class="o">-</span> <span class="i">sy</span>)<span class="o">*</span><span class="n">360</span> <span class="o">+</span> (<span class="i">em</span> <span class="o">-</span> <span class="i">sm</span>)<span class="o">*</span><span class="n">30</span> <span class="o">+</span> (<span class="i">ed</span> <span class="o">-</span> <span class="i">sd</span>)

<span class="c">//</span><span class="c">DateTime(2012,4,25),</span> <span class="c">DateTime(2012,7,31),</span> <span class="c">95</span>
<span class="c">//</span><span class="c">DateTime(2012,6,30),</span> <span class="c">DateTime(2012,7,31),</span> <span class="c">30</span>
<span class="c">//</span><span class="c">DateTime(2012,2,28),</span> <span class="c">DateTime(2012,3,31),</span> <span class="c">32</span>
<span class="c">//</span><span class="c">DateTime(2012,2,29),</span> <span class="c">DateTime(2012,3,31),</span> <span class="c">31</span></pre></td></tr></table>  

In this case start and end dates can't be greater than 30. Can we define that with types? Sure!  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">type</span> <span class="i">day30</span> <span class="o">=</span> <span class="i">x</span><span class="o">:</span><span class="i">day</span>{ <span class="i">x</span> <span class="o">&lt;&gt;</span> <span class="n">31</span> }

<span class="k">val</span> <span class="i">adjDay</span><span class="o">:</span> <span class="i">day</span> <span class="k">-&gt;</span> <span class="i">day30</span>
<span class="k">let</span> <span class="i">adjDay</span> <span class="i">d</span> <span class="o">=</span> <span class="k">if</span> <span class="i">d</span> <span class="o">=</span> <span class="n">31</span> <span class="k">then</span> <span class="n">30</span> <span class="k">else</span> <span class="i">d</span> <span class="c">//</span><span class="c">try</span> <span class="c">to</span> <span class="c">leave</span> <span class="c">only</span> <span class="c">d</span> <span class="c">here</span> <span class="c">-</span> <span class="c">it</span> <span class="c">fails</span> <span class="c">to</span> <span class="c">typecheck</span></pre></td></tr></table>  

What if we want to add another convention? US 30/360 handles EOM dates differently, so if a start date is greater than end date the function results can become inconsistent. So we expect start date to be less than (or equal to) end date. A type checker can verify this requirement too:  
	
<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
<span class="l"> 9: </span>
<span class="l">10: </span>
<span class="l">11: </span>
<span class="l">12: </span>
<span class="l">13: </span>
<span class="l">14: </span>
<span class="l">15: </span>
<span class="l">16: </span>
<span class="l">17: </span>
<span class="l">18: </span>
<span class="l">19: </span>
<span class="l">20: </span>
<span class="l">21: </span>
<span class="l">22: </span>
<span class="l">23: </span>
<span class="l">24: </span>
<span class="l">25: </span>
<span class="l">26: </span>
<span class="l">27: </span>
<span class="l">28: </span>
<span class="l">29: </span>
<span class="l">30: </span>
<span class="l">31: </span>
<span class="l">32: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">val</span> <span class="i">daysInMonth</span><span class="o">:</span> <span class="i">year</span> <span class="k">-&gt;</span> <span class="i">month</span> <span class="k">-&gt;</span> <span class="i">day</span>

<span class="k">val</span> <span class="i">lastDay</span><span class="o">:</span> <span class="i">year</span> <span class="k">-&gt;</span> <span class="i">month</span> <span class="k">-&gt;</span> <span class="i">day</span> <span class="k">-&gt;</span> <span class="i">bool</span> <span class="k">-&gt;</span> <span class="i">bool</span>
<span class="c">//</span><span class="c">EU</span> <span class="c">30/360:</span> <span class="c">the</span> <span class="c">last</span> <span class="c">day</span> <span class="c">of</span> <span class="c">Feb</span> <span class="c">is</span> <span class="c">not</span> <span class="c">changed</span> <span class="c">to</span> <span class="c">30</span>
<span class="k">let</span> <span class="i">lastDay</span> <span class="i">y</span> <span class="i">m</span> <span class="i">d</span> <span class="i">eu</span> <span class="o">=</span>
    <span class="k">if</span> <span class="i">eu</span> <span class="o">&amp;&amp;</span> (<span class="i">m</span> <span class="o">=</span> <span class="n">2</span>) <span class="k">then</span> <span class="k">false</span>
    <span class="k">else</span> <span class="i">d</span> <span class="o">=</span> (<span class="i">daysInMonth</span> <span class="i">y</span> <span class="i">m</span>)

<span class="k">let</span> <span class="i">adjDay</span> <span class="i">d</span> <span class="i">last</span> <span class="o">=</span> <span class="k">if</span> <span class="i">last</span> <span class="k">then</span> <span class="n">30</span> <span class="k">else</span> <span class="i">d</span>

<span class="k">val</span> <span class="i">days360</span><span class="o">:</span> <span class="i">sy</span><span class="o">:</span><span class="i">year</span> <span class="k">-&gt;</span> <span class="i">sm</span><span class="o">:</span><span class="i">month</span> <span class="k">-&gt;</span> <span class="i">sd</span><span class="o">:</span> <span class="i">day</span>
    <span class="k">-&gt;</span> <span class="i">ey</span><span class="o">:</span><span class="i">year</span>{<span class="i">ey</span> <span class="o">&gt;</span><span class="o">=</span> <span class="i">sy</span>}
    <span class="k">-&gt;</span> <span class="i">em</span><span class="o">:</span><span class="i">month</span>{<span class="i">ey</span> <span class="o">&gt;</span> <span class="i">sy</span> <span class="o">\/</span> <span class="i">(ey <span class="o">=</span> <span class="i">sy</span> <span class="o">/\</span> <span class="i">em</span> <span class="o">&gt;=</span> <span class="i">sm)}</span>
    <span class="k">-&gt;</span> <span class="i">ed</span><span class="o">:</span><span class="i">day</span>{<span class="i">ey</span> <span class="o">&gt;</span> <span class="i">sy</span> <span class="o">\/</span> <span class="i">em</span> <span class="o">&gt;</span> <span class="i">sm</span> <span class="o">\/</span> <span class="i">ed</span> <span class="o">&gt;=</span> <span class="i">sd}</span>
    <span class="k">-&gt;</span> <span class="i">bool</span>
    <span class="k">-&gt;</span> <span class="i">int</span>

<span class="k">let</span> <span class="i">days360</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="i">convEU</span> <span class="o">=</span>
    <span class="k">let</span> <span class="i">slast</span>, <span class="i">elast</span> <span class="o">=</span> <span class="i">lastDay</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">convEU</span>, <span class="i">lastDay</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="i">convEU</span> <span class="k">in</span>

    <span class="c">//</span><span class="c">EU:</span> <span class="c">31</span> <span class="c">-&gt;</span> <span class="c">30</span>
    <span class="c">//</span><span class="c">US:</span> <span class="c">31</span> <span class="c">-&gt;</span> <span class="c">30;</span> <span class="c">end</span> <span class="c">of</span> <span class="c">Feb</span> <span class="c">-&gt;</span> <span class="c">30</span>
    <span class="k">let</span> <span class="i">sd</span> <span class="o">=</span> <span class="i">adjDay</span> <span class="i">sd</span> <span class="i">slast</span> <span class="k">in</span>
    <span class="c">//</span><span class="c">EU:</span> <span class="c">31</span> <span class="c">-&gt;</span> <span class="c">30</span>
    <span class="c">//</span><span class="c">US:</span> <span class="c">31</span> <span class="c">-&gt;</span> <span class="c">30</span> <span class="c">if</span> <span class="c">sd</span> <span class="c">is</span> <span class="c">EOM;</span> <span class="c">end</span> <span class="c">of</span> <span class="c">Feb</span> <span class="c">-&gt;</span> <span class="c">30</span> <span class="c">if</span> <span class="c">sd</span> <span class="c">was</span> <span class="c">end</span> <span class="c">of</span> <span class="c">Feb</span> <span class="c">too</span>
    <span class="k">let</span> <span class="i">checkEndDate</span> <span class="o">=</span> <span class="i">convEU</span> <span class="o">||</span> (<span class="i">slast</span> <span class="o">&amp;&amp;</span> ((<span class="i">sm</span> <span class="o">=</span> <span class="n">2</span>) <span class="o">||</span> <span class="i">not</span> (<span class="i">em</span> <span class="o">=</span> <span class="n">2</span>))) <span class="k">in</span>
    <span class="k">let</span> <span class="i">ed</span> <span class="o">=</span> <span class="i">adjDay</span> <span class="i">ed</span> (<span class="i">elast</span> <span class="o">&amp;&amp;</span> (<span class="i">convEU</span> <span class="o">||</span> <span class="i">checkEndDate</span>)) <span class="k">in</span>

    (<span class="i">ey</span> <span class="o">-</span> <span class="i">sy</span>)<span class="o">*</span><span class="n">360</span> <span class="o">+</span> (<span class="i">em</span> <span class="o">-</span> <span class="i">sm</span>)<span class="o">*</span><span class="n">30</span> <span class="o">+</span> (<span class="i">ed</span> <span class="o">-</span> <span class="i">sd</span>)

<span class="k">let</span> _ <span class="o">=</span> <span class="i">days360</span> <span class="n">2013</span> <span class="n">7</span> <span class="n">6</span> <span class="n">2015</span> <span class="n">7</span> <span class="n">6</span> <span class="k">true</span>
<span class="k">let</span> _ <span class="o">=</span> <span class="i">days360</span> <span class="n">2015</span> <span class="n">7</span> <span class="n">6</span> <span class="n">2013</span> <span class="n">7</span> <span class="n">6</span> <span class="k">true</span> <span class="c">//</span><span class="c">error</span>
</span></pre></td></tr></table>  

The last line gives the following error:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="i">input</span>(<span class="n">44</span>,<span class="n">8</span><span class="o">-</span><span class="n">44</span>,<span class="n">29</span>) <span class="o">:</span> <span class="i">Error</span> <span class="o">:</span>
<span class="i">Expected</span> <span class="i">an</span> <span class="i">expression</span> <span class="i">of</span> <span class="i">type</span><span class="o">:</span> <span class="i">x_23_6</span><span class="o">:</span> <span class="i">Dates</span><span class="o">.</span><span class="i">year</span>{<span class="i">GTE</span> <span class="i">x_23_6</span> <span class="n">2013</span>}
<span class="i">but</span> <span class="i">got</span> (<span class="n">2013</span>)<span class="o">:</span> <span class="i">int</span>
<span class="i">Type</span> <span class="i">checking</span> <span class="i">failed</span></pre></td></tr></table>  

All these >, \/, /\, < look funny, if it's not enough – just imagine the effect of adding time components. Fortunately, the relation between the dates can be defined as a separate type:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="c">//</span><span class="c">start</span> <span class="c">date</span> <span class="c">&lt;=</span> <span class="c">end</span> <span class="c">date</span>
<span class="k">type</span> <span class="i">LTE</span> <span class="o">=</span> <span class="k">fun</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="o">=&gt;</span> 
    <span class="i">ey</span> <span class="o">&gt;</span> <span class="i">sy</span> <span class="o">\/</span> <span class="i">(ey <span class="o">=</span> <span class="i">sy</span> <span class="o">/\</span> <span class="i">(em</span> <span class="o">&gt;</span> <span class="i">sm</span> <span class="o">\/</span> <span class="i">(em</span> <span class="o">=</span> <span class="i">sm</span> <span class="o">/\</span> <span class="i">ed</span> <span class="o">&gt;=</span> <span class="i">sd)))</span>

<span class="k">val</span> <span class="i">days360</span><span class="o">:</span> <span class="i">sy</span><span class="o">:</span><span class="i">year</span> <span class="k">-&gt;</span> <span class="i">sm</span><span class="o">:</span><span class="i">month</span> <span class="k">-&gt;</span> <span class="i">sd</span><span class="o">:</span><span class="i">day</span>
    <span class="k">-&gt;</span> <span class="i">ey</span><span class="o">:</span><span class="i">year</span> <span class="k">-&gt;</span> <span class="i">em</span><span class="o">:</span><span class="i">month</span> <span class="k">-&gt;</span> <span class="i">ed</span><span class="o">:</span><span class="i">day</span>{<span class="i">LTE</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span>}
    <span class="k">-&gt;</span> <span class="i">bool</span>
    <span class="k">-&gt;</span> <span class="i">int</span>
</span></pre></td></tr></table>  

Now let's assume that a date is valid when it's not 2/30 or 2/31. We can define a logical function using ````logic val```` construct. Such functions can be used in refinements but not in code itself. The axioms are defined using the ````assume```` construct:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">logic</span> <span class="k">val</span> <span class="i">isValidDate</span><span class="o">:</span> <span class="i">year</span> <span class="k">-&gt;</span> <span class="i">month</span> <span class="k">-&gt;</span> <span class="i">day</span> <span class="k">-&gt;</span> <span class="i">bool</span>
<span class="k">assume</span> <span class="i">Valid</span><span class="o">:</span> <span class="i">forall</span> <span class="i">y</span> <span class="i">m</span> (<span class="i">d</span><span class="o">:</span><span class="i">day</span>{<span class="i">m</span> <span class="o">&lt;&gt;</span> <span class="n">2</span> <span class="o">\/</span> <span class="i">d</span> <span class="o">&lt;</span> <span class="n">30</span>}<span class="i">). isValidDate y m d</span> <span class="o">=</span> <span class="k">true</span> 

<span class="k">val</span> <span class="i">days360</span><span class="o">:</span> <span class="i">sy</span><span class="o">:</span><span class="i">year</span> <span class="k">-&gt;</span> <span class="i">sm</span><span class="o">:</span><span class="i">month</span> <span class="k">-&gt;</span> <span class="i">sd</span><span class="o">:</span><span class="i">day</span>{<span class="i">isValidDate</span> <span class="i">sy</span> <span class="i">sm</span> <span class="i">sd</span> <span class="o">=</span> <span class="k">true</span>}
    <span class="k">-&gt;</span> <span class="i">ey</span><span class="o">:</span><span class="i">year</span> <span class="k">-&gt;</span> <span class="i">em</span><span class="o">:</span><span class="i">month</span> <span class="k">-&gt;</span> <span class="i">ed</span><span class="o">:</span><span class="i">day</span>{<span class="i">isValidDate</span> <span class="i">ey</span> <span class="i">em</span> <span class="i">ed</span> <span class="o">=</span> <span class="k">true</span> <span class="o">/\</span> <span class="i">LTE sy sm sd ey em ed</span>}
    <span class="k">-&gt;</span> <span class="i">bool</span>
    <span class="k">-&gt;</span> <span class="i">int</span></pre></td></tr></table>  
	

Well, quite enough of refinements for a start. There’s much more ways to apply them: for example, refinements with affine values are great for verification of stateful programs (see the “Secure multi-party sessions in F*” section in the paper). With rise4fun you may get a request timeout in more complex cases, so I’d recommend to download the F* package for your experiments.  

### References  

<strong>1.</strong> "Secure Distributed Programming with Value-Dependent Types" <a title="Secure Distributed Programming with Value-Dependent Types" href="http://research.microsoft.com/apps/pubs/default.aspx?id=141708" target="_blank">paper</a>.  
<strong>2.</strong> F* <a title="F* project home" href="http://research.microsoft.com/en-us/projects/fstar/" target="_blank">home page</a>.  
<strong>3.</strong> Rise4fun <a title="Rise4Fun - F* tutorial" href="http://rise4fun.com/FStar/tutorial/guide" target="_blank">tutorial</a>.  


### Bonus  

Time to get back to the good old tests: the obvious idea is to compare the results with Excel. We are interested in two functions: DAYS360 and YEARFRAC - multiplying the result with 360. Look at the following comparison (when the <a title="Days - Comparison" href="https://docs.google.com/spreadsheet/ccc?key=0AsEtPrcHNCbXdHQyb2JJQzgzNkgyREFlWF9ENEVTeEE&amp;usp=sharing" target="_blank">spreadsheet</a> was converted to Google Docs format, the results were changed, so I added separate rows with Excel output):  

<iframe src="https://docs.google.com/spreadsheet/pub?key=0AsEtPrcHNCbXdHQyb2JJQzgzNkgyREFlWF9ENEVTeEE&amp;single=true&amp;gid=0&amp;output=html&amp;widget=true" height="360" width="600" frameborder="0"></iframe>  

There’re obvious problems with DAYS360 when the dates are swapped (start date > end date) and end-of-Feb cases. But our function is different from YEARFRAC too.  

> Date adjustments rules for 30/360US:  
1. If the investment is EOM and D1, D2 are the last day of Feb, then D2 = 30.  
2. If the investment is EOM and D1 is the last day of Feb, then D1 = 30.  
3. If D2 is 31 and D1 is 30 or 31, then D2 = 30.  
4. If D1 is 31, then D1 = 30.  

Let the start and end dates be 2/29/2012 and 3/31/2012 respectively. The rules are applied in order:  
````D2 - D1 = D2 - 30 = 30 - 30 = 0````  

But in Excel the rule 3 goes before the rule 2 (our function can be simply modified to behave the same way: ````sm = 2 || em <> 2```` should be replaced with ````(sm = 2) = (em = 2)````):  
````D2 - D1 = 31 - D1 = 31 - 30 = 1````  

So we expect to see ````(2012 – 2012)*360 + (3 – 2)*30 + 0 = 30```` and not ````31````.  
F# version is available <a title="GitHub - days360.fsx" href="https://github.com/luajalla/everything-fun/blob/master/days360.fsx" target="_blank">here</a>.  
<p>&nbsp;</p>
<p>&nbsp;</p>
