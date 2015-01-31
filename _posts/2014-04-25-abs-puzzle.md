---
layout: post
status: publish
published: true
title: Abs Puzzle
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 2842
wordpress_url: http://luajalla.azurewebsites.net/?p=2842
date: '2014-04-25 21:17:49 +0200'
date_gmt: '2014-04-25 21:17:49 +0200'
image:
  feature: header5.jpg
  credit: Basel, Rhein
tags:
- fstar
- discoveries
- pex
- .net
- jvm
comments: []
---
If someone asked you to implement abs function (say, for ints), how would you do that? A simple experiment shows that almost everyone comes up with something like that, whether it's F#, C#, Scala or anything else:  

<pre class="fssnip">
<span class="l">1: </span><span class="k">let</span> <span class="i">abs x </span><span class="o">=</span> <span class="k">if</span> <span class="i">x</span> <span class="o">&lt;</span> <span class="n">0</span> <span class="k">then</span> <span class="o">-</span><span class="i">x</span> <span class="k">else</span> <span class="i">x</span>
</pre>  

The obvious question - do the answers match your expectations? That depends...  

<img src="{{ site.url }}/images/absx.jpg" alt="absx" style="text-align:center;margin-left:auto;margin-right:auto;display:block"/>

*[Originally posted [here](http://luajalla.azurewebsites.net/abs-puzzle/).]*   

It's quite obvious when you think about the corner cases - here it's the minimal integer value:  

<pre class="fssnip">
<span class="o">-</span><span class="i">(</span><span class="o">-</span><span class="n">2147483648</span><span class="i">)</span> <span class="o">= -</span><span class="n">2147483648</span>
</pre>  

So, in Java/Scala the absolute value can be <a href="http://docs.oracle.com/javase/8/docs/api/java/lang/Math.html" title="Java Math" target="_blank">negative</a>:  

> Note that if the argument is equal to the value of Integer.MIN_VALUE, the most negative representable int value, the result is that same value, which is negative.  

In .NET you'll get an <a href="http://msdn.microsoft.com/en-us/library/dk4666yx%28v=vs.110%29.aspx" title=".NET Math.Abs" target="_blank">exception</a> - personally, I think that's a better behaviour for this function:  
````OverflowException - value equals Int32.MinValue````  

When I started to think about different examples for our future meetup (collecting all kinds of crashes on the way - from 'CLR detected an invalid program' to 'unexpected exception'), I couldn't resist but look at what happens when you move some checks to the type level.  

Let's define a refinement type for the natural numbers in F* and make sure it works (you can try the examples <a href="http://rise4fun.com/FStar" title="Try FStar" target="_blank">in your browser</a>):  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">module</span> <span class="i">Nums</span>

<span class="k">type</span> <span class="i">nat</span> <span class="o">=</span> <span class="i">x</span><span class="o">:</span><span class="i">int</span><span class="o">{</span><span class="i">x</span> <span class="o">&gt;=</span> <span class="n">0</span><span class="o">}</span>

<span class="k">let</span> <span class="i">good_x</span><span class="o">:</span> <span class="i">nat</span> <span class="o">=</span> <span class="n">42</span>
<span class="k">let</span> <span class="i">bad_x</span><span class="o">:</span> <span class="i">nat</span> <span class="o">=</span> <span class="o">-</span><span class="n">42</span>
<span class="fsi">input(6,17-6,20) : Error : Expected an expression of type: x_3:int{GTE x_3 0} but got</span>
<span class="fsi">(op_Minus(42)): x_357_1:int{Eq2 int int x_357_1 (Minus (42))} Type checking failed: Nums</span>
</pre></td></tr></table>  

Now it's turn for our ````abs```` function:  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">val</span> <span class="i">abs</span><span class="o">:</span> <span class="i">int</span> <span class="o">-&gt;</span> <span class="i">nat</span>
<span class="k">let</span> <span class="i">abs x </span><span class="o">=</span> <span class="k">if</span> <span class="i">x</span> <span class="o">&lt;</span> <span class="n">0</span> <span class="k">then</span> <span class="o">-</span><span class="i">x</span>

<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs</span> <span class="n">2147483647</span>  
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs (</span><span class="o">-</span><span class="n">2147483647</span><span class="i">)</span> 

<span class="c">// both following statements give Syntax error:</span>                                
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs</span> <span class="n">2147483648</span>
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs (</span><span class="o">-</span><span class="n">2147483648</span><span class="i">)</span>
</pre></td></tr></table>  

The first error is kind of expected, because the literal ````2147483648```` is too large for ````int````. However, I'd think the second one should be ok, and after a quick look at the F* lexer not sure why they both fail (````"Allow &lt;max_int+1&gt; to parse as min_int"````). Anyway, there's another interesting case:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>  
<span class="l">    </span>  
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs (</span><span class="n">2147483647</span> <span class="o">+</span> <span class="n">1</span><span class="i">)</span>
<span class="c">// And the result is...</span>                                                         
<span class="fsi">Verified module: Nums</span>
</pre></td></tr></table>

A bit strange, isn't it? If you ask Scala or F# what ````2147483647 + 1```` is, you get ````-2147483648````, that means ````abs (2147483647 + 1)```` can't be of ````nat```` type! Here's a bunch of checks for '+1' with different refinements:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">val</span> <span class="i">check</span><span class="o">:</span> <span class="i">x</span><span class="o">:</span><span class="i">nat</span> <span class="o">-&gt;</span> <span class="i">y</span><span class="o">:</span><span class="i">int</span><span class="o">{</span><span class="i">y</span> <span class="o">&gt;</span> <span class="i">x</span><span class="o">}</span>
<span class="k">let</span> <span class="i">check x</span> <span class="o">=</span> <span class="k">x</span> <span class="o">+</span> <span class="n">1</span> 

<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">check</span> <span class="i">(</span><span class="n">2147483647</span> <span class="o">+</span> <span class="n">1</span><span class="i">)</span>
<span class="fsi">Error : Let-bound variable op_Addition(2147483647, 1) escapes its scope in type</span>
<span class="fsi">x_8_1:int{GT x_8_1 x_11_3}; insert an explicit type annotation. (expected type none)</span>
<span class="fsi">Type checking failed: Nums</span>    
</pre></td></tr></table>  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets">
<pre class="fssnip">
<span class="k">val</span> <span class="i">check</span><span class="o">:</span> <span class="i">x</span><span class="o">:</span><span class="i">nat</span> <span class="o">-&gt;</span> <span class="i">y</span><span class="o">:</span><span class="i">int</span><span class="o">{</span><span class="i">y</span> <span class="o">=</span> <span class="i">(x</span><span class="o">+</span><span class="n">1</span><span class="i">)</span><span class="o">}</span> 
<span class="fsi">Error : Let-bound variable op_Addition(2147483647, 1) escapes its scope in type</span>
<span class="fsi">x_6_1:int{Eq2 int int x_6_1 (Add (x_9_5) (1))}; insert an explicit type annotation.</span>
<span class="fsi">(expected type none) Type checking failed: Nums</span>
</pre></td></tr></table>  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">val</span> <span class="i">check</span><span class="o">:</span> <span class="i">x</span><span class="o">:</span><span class="i">nat</span> <span class="o">-&gt;</span> <span class="i">y</span><span class="o">:</span><span class="i">int</span><span class="o">{</span><span class="i">y</span> <span class="o">&gt;</span> <span class="n">0</span><span class="o">}</span> 
<span class="fsi">Verified module: Nums</span>                                                            
</pre></td></tr></table>  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">val</span> <span class="i">check</span><span class="o">:</span> <span class="i">x</span><span class="o">:</span><span class="i">nat</span> <span class="o">-&gt;</span> <span class="i">y</span><span class="o">:</span><span class="i">int</span><span class="o">{</span><span class="i">y</span> <span class="o">&lt;</span> <span class="n">0</span> <span class="o">\/</span> <span class="i">y</span> <span class="o">&gt;</span> <span class="i">x</span><span class="o">}</span>
<span class="fsi">Error : Let-bound variable op_Addition(2147483647, 1) escapes its scope in type</span>
<span class="fsi">x_8_1:int{((LT x_8_1 0) || (GT x_8_1 x_11_3))}; insert an explicit type annotation.</span>
<span class="fsi">(expected type none) Type checking failed: Nums</span>
</pre></td></tr></table>   

All the cases above succeeded for the max value:  

<table class="presmall"><tr><td class="snippets">
<pre class="fssnip">
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">check</span> <span class="n">2147483647</span>
</pre></td></tr></table>  

I also tried these examples on my old-slightly-working F* 0.7 alpha Mono build, where the results seem to be more reasonable:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs</span> <span class="n">2147483648</span> 
<span class="fsi">Error : Expected an expression of type: int but got (2147483648.000000): float</span>
<span class="fsi">Type checking failed: Nums</span>
</pre></td></tr></table>  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">let</span> <span class="i">_</span> <span class="o">=</span> <span class="i">abs</span> <span class="i">(</span><span class="o">-</span><span class="n">2147483648</span><span class="i">)</span> 
<span class="fsi"> starting backend</span>
<span class="fsi"> starting derefinement</span>
<span class="fsi"> starting expanding types</span>
<span class="fsi"> finished with expanding types</span>
<span class="fsi">Verified module: Nums</span>                                                    
</pre></td></tr></table>  

Maybe one day I'll finally setup a VM with Windows to take a look at IL, but for now the conclusion is to always be on the lookout.  

P.S. <a href="http://pexforfun.com/" title="Pex" target="_blank">Pex</a> can find the failing case (for some reason, at least in online version this example works only with C# and VB):  

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
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
<span class="l">    </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">using</span> <span class="i">System;</span>
<span class="k">using</span> <span class="i">System.Diagnostics.Contracts;</span>

<span class="k">public class</span> <span class="i">Program</span> <span class="o">{</span>
  <span class="k">public static void</span> <span class="i">Puzzle(int x) </span><span class="o">{</span>
    <span class="k">var</span> <span class="i">y</span> <span class="o">=</span> <span class="i">x</span> <span class="o">&lt;</span> <span class="n">0</span> <span class="o">?</span> <span class="o">-</span><span class="i">x</span> <span class="o">:</span> <span class="i">x;</span>
    <span class="i">Contract.Assert(y</span> <span class="o">&gt;=</span> <span class="n">0</span><span class="i">);</span>
  <span class="o">}</span>
<span class="o">}</span>
<span class="fsi">x            | Output/Exception  | Error Message</span>
<span class="fsi">-----------------------------------------------------------</span>
<span class="fsi">0            |                   |</span>
<span class="fsi">int.MinValue | ContractException | Assertion failed: y >= 0</span>    
</pre></td></tr></table>  

<p>&nbsp;<br />
&nbsp;</p>
