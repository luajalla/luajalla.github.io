---
layout: post
title: "Reshaping Arrays in .NET"
modified:
categories: 
description:
tags: .net matlab fsharp
image:
  feature: header8.jpg
  credit: Alpine Crocus
  creditlink:
comments:
share:
date: 2015-02-21T17:07:56+01:00
---
Reshape is one of these little functions, which look so simple and straightforward that you don't think much about them. It's quite useful and, I'm pretty sure, familiar to everyone who's written something in a language like Matlab. However, in other languages it might become a bit tricky.

#### Question
I asked a bunch of people how they would write [reshape](http://ch.mathworks.com/help/matlab/ref/reshape.html) in a .NET language. That's not really a problem when you know the exact type and dimensionality, e.g. when you *always* convert 2D-array to 3D, but what if you don't? Given the limitations of generic constraints, the only requirement to the output (in addition to correctness, of course) was an ability to get the full information about the underlying types. And that's where the challenge starts...    

#### Requirements

Let's take a look at a somewhat simplified problem - converting a single-dimensional array into a multidimensional one. My usecase was reading the files in .mat binary format as a part of the [type provider](https://github.com/luajalla/matprovider): read the bytes, figure out the type and if it's supposed to be an array - make it an array. Type conversions are not relevant for reshaping itself, so we can skip that for now.  

- The input is a single-dimensional array of any type and another array with dimension sizes;
- The output is another array, properly reshaped, where the type information is preserved (not necessary explicitly);
- Use standard types if possible.

#### Meet System.Array
Unfortunately, that's not the case for fancy type signatures, so we'll go with a plain old ````System.Array````.

The simplest example is 1D -> 1D "conversion", it's possible to get the type information or cast the output to a real array type, whether it's ````int[]````, ````string[]````, ````float[][]```` or anything else:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
<span class="l">4: </span>
<span class="l">5: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">System</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">Array</span>) <span class="o">=</span> <span onmouseout="hideTip(event, 'fst2', 5)" onmouseover="showTip(event, 'fst2', 5)" class="i">xs</span>

<span class="k">let</span> <span onmouseout="hideTip(event, 'fst5', 6)" onmouseover="showTip(event, 'fst5', 6)" class="i">xs</span> <span class="o">=</span> [| <span class="n">1</span>;<span class="n">2</span>;<span class="n">3</span>;<span class="n">4</span>;<span class="n">5</span> |]
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 7)" onmouseover="showTip(event, 'fst6', 7)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 8)" onmouseover="showTip(event, 'fst1', 8)" class="i">f</span> <span onmouseout="hideTip(event, 'fst5', 9)" onmouseover="showTip(event, 'fst5', 9)" class="i">xs</span> <span class="c">// val ys : System.Array = [|1; 2; 3; 4; 5|]</span>
<span onmouseout="hideTip(event, 'fst6', 10)" onmouseover="showTip(event, 'fst6', 10)" class="i">ys</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst7', 11)" onmouseover="showTip(event, 'fst7', 11)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// &quot;Int32[]&quot;</span></pre>
</td>
</tr>
</table>

Great! The next step is to add a dimension:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f2</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">System</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">Array</span>) <span class="o">=</span> [| <span onmouseout="hideTip(event, 'fst2', 5)" onmouseover="showTip(event, 'fst2', 5)" class="i">xs</span>; <span onmouseout="hideTip(event, 'fst2', 6)" onmouseover="showTip(event, 'fst2', 6)" class="i">xs</span> |]
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst5', 7)" onmouseover="showTip(event, 'fst5', 7)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 8)" onmouseover="showTip(event, 'fst1', 8)" class="i">f2</span> <span class="i">xs</span> <span class="c">// val ys : System.Array [] = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span></pre>
</td>
</tr>
</table>  

This one doesn't work for us because of its return type, so:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f3</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Array</span>) <span class="o">=</span> [| <span onmouseout="hideTip(event, 'fst2', 4)" onmouseover="showTip(event, 'fst2', 4)" class="i">xs</span>; <span onmouseout="hideTip(event, 'fst2', 5)" onmouseover="showTip(event, 'fst2', 5)" class="i">xs</span>|] <span class="o">:&gt;</span> <span onmouseout="hideTip(event, 'fst3', 6)" onmouseover="showTip(event, 'fst3', 6)" class="i">Array</span>;;
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst4', 7)" onmouseover="showTip(event, 'fst4', 7)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 8)" onmouseover="showTip(event, 'fst1', 8)" class="i">f3</span> <span class="i">xs</span> <span class="c">// val ys : Array = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span>
<span onmouseout="hideTip(event, 'fst4', 9)" onmouseover="showTip(event, 'fst4', 9)" class="i">ys</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst5', 10)" onmouseover="showTip(event, 'fst5', 10)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// &quot;Array[]&quot;</span></pre>
</td>
</tr>
</table>  

And the same output with ````Array.init```` methods:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f4</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Array</span>) <span class="o">=</span> <span onmouseout="hideTip(event, 'fst3', 4)" onmouseover="showTip(event, 'fst3', 4)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst4', 5)" onmouseover="showTip(event, 'fst4', 5)" class="i">init</span> <span class="n">2</span> (<span class="k">fun</span> _ <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst2', 6)" onmouseover="showTip(event, 'fst2', 6)" class="i">xs</span>) <span class="o">:&gt;</span> <span onmouseout="hideTip(event, 'fst3', 7)" onmouseover="showTip(event, 'fst3', 7)" class="i">Array</span>;;
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst5', 8)" onmouseover="showTip(event, 'fst5', 8)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 9)" onmouseover="showTip(event, 'fst1', 9)" class="i">f4</span> <span class="i">xs</span> <span class="c">// val ys : System.Array = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span>
<span onmouseout="hideTip(event, 'fst5', 10)" onmouseover="showTip(event, 'fst5', 10)" class="i">ys</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst6', 11)" onmouseover="showTip(event, 'fst6', 11)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// &quot;Array[]&quot;</span></pre>
</td>
</tr>
</table>


You also can't cast the result any more - ````ys :?> int[][]```` throws an exception:  

<table class="presmall"><tr><td>
	System.InvalidCastException: Cannot cast from source type to destination type.
      at &lt;StartupCode$FSI_0017&gt;.$FSI_0017.main@ () [0x00000] in &lt;filename unknown&gt;:0 
      at (wrapper managed-to-native) System.Reflection.MonoMethod:InternalInvoke (System.Reflection.MonoMethod,object,object[],System.Exception&)
      at System.Reflection.MonoMethod.Invoke (System.Object obj, BindingFlags invokeAttr, System.Reflection.Binder binder, System.Object[] parameters, System.Globalization.CultureInfo culture) [0x00000] in &lt;filename unknown&gt;:0 
    Stopped due to error
</td></tr></table>
	
And yes, of course, you *can* get the ints out of it:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">arr</span> <span class="o">=</span> <span class="i">ys</span><span class="o">.</span><span class="i">GetValue</span> <span class="n">0</span> <span class="c">// val arr : obj = [|1; 2; 3; 4; 5|]</span>
<span onmouseout="hideTip(event, 'fst1', 2)" onmouseover="showTip(event, 'fst1', 2)" class="i">arr</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst2', 3)" onmouseover="showTip(event, 'fst2', 3)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// val it : string = &quot;Int32[]&quot;</span>
<span onmouseout="hideTip(event, 'fst1', 4)" onmouseover="showTip(event, 'fst1', 4)" class="i">arr</span> <span class="o">:?&gt;</span> <span onmouseout="hideTip(event, 'fst3', 5)" onmouseover="showTip(event, 'fst3', 5)" class="i">int</span>[] <span class="c">// val it : int [] = [|1; 2; 3; 4; 5|]</span></pre>
</td>
</tr>
</table>

That's somewhat disappointing, because we do need something castable, with more or less concrete type. But it's quite intuitive: the argument is ````Array```` - the output is ````Array[]````, in the typed version ````'T[]```` - ````'T[][]````:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
<span class="l">4: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f5</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">int</span>[]) <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst5', 5)" onmouseover="showTip(event, 'fst5', 5)" class="i">init</span> <span class="n">2</span> (<span class="k">fun</span> _ <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst2', 6)" onmouseover="showTip(event, 'fst2', 6)" class="i">xs</span>) <span class="o">:&gt;</span> <span onmouseout="hideTip(event, 'fst4', 7)" onmouseover="showTip(event, 'fst4', 7)" class="i">Array</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 8)" onmouseover="showTip(event, 'fst6', 8)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 9)" onmouseover="showTip(event, 'fst1', 9)" class="i">f5</span> <span class="i">xs</span> <span class="c">// val ys : System.Array = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span>
<span onmouseout="hideTip(event, 'fst6', 10)" onmouseover="showTip(event, 'fst6', 10)" class="i">ys</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst7', 11)" onmouseover="showTip(event, 'fst7', 11)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// val it : string = &quot;Int32[][]&quot;</span>
<span onmouseout="hideTip(event, 'fst6', 12)" onmouseover="showTip(event, 'fst6', 12)" class="i">ys</span> <span class="o">:?&gt;</span> <span onmouseout="hideTip(event, 'fst3', 13)" onmouseover="showTip(event, 'fst3', 13)" class="i">int</span>[][] <span class="c">// val it : int [] [] = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span></pre>
</td>
</tr>
</table>  

or something like that:  
 
<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f6</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Array</span>) <span class="o">=</span> <span onmouseout="hideTip(event, 'fst3', 4)" onmouseover="showTip(event, 'fst3', 4)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst4', 5)" onmouseover="showTip(event, 'fst4', 5)" class="i">init</span> <span class="n">2</span> (<span class="k">fun</span> _ <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst2', 6)" onmouseover="showTip(event, 'fst2', 6)" class="i">xs</span> <span class="o">:?&gt;</span> <span onmouseout="hideTip(event, 'fst5', 7)" onmouseover="showTip(event, 'fst5', 7)" class="i">int</span>[]) <span class="o">:&gt;</span> <span onmouseout="hideTip(event, 'fst3', 8)" onmouseover="showTip(event, 'fst3', 8)" class="i">Array</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 9)" onmouseover="showTip(event, 'fst6', 9)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 10)" onmouseover="showTip(event, 'fst1', 10)" class="i">f6</span> <span class="i">xs</span> <span class="c">// val ys : Array = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span>
<span onmouseout="hideTip(event, 'fst6', 11)" onmouseover="showTip(event, 'fst6', 11)" class="i">ys</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst7', 12)" onmouseover="showTip(event, 'fst7', 12)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// val it : string = &quot;Int32[][]&quot;  </span></pre>
</td>
</tr>
</table>

At this point  we're back to ````System.Array```` and its method ````CreateInstance````:  

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
<span class="l">4: </span>
<span class="l">5: </span>
<span class="l">6: </span>
<span class="l">7: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">f7</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">xs</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Array</span>) <span class="o">=</span>                   
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">t</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst2', 5)" onmouseover="showTip(event, 'fst2', 5)" class="i">xs</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst5', 6)" onmouseover="showTip(event, 'fst5', 6)" class="i">GetType</span>()                                                
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 7)" onmouseover="showTip(event, 'fst6', 7)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst3', 8)" onmouseover="showTip(event, 'fst3', 8)" class="i">Array</span><span class="o">.</span><span class="i">CreateInstance</span>(<span onmouseout="hideTip(event, 'fst4', 9)" onmouseover="showTip(event, 'fst4', 9)" class="i">t</span>, <span class="n">2</span>)
    <span class="k">for</span> <span onmouseout="hideTip(event, 'fst7', 10)" onmouseover="showTip(event, 'fst7', 10)" class="i">i</span> <span class="k">in</span> <span class="n">0..</span><span class="n">1</span> <span class="k">do</span> <span onmouseout="hideTip(event, 'fst6', 11)" onmouseover="showTip(event, 'fst6', 11)" class="i">ys</span><span class="o">.</span><span class="i">SetValue</span>(<span onmouseout="hideTip(event, 'fst2', 12)" onmouseover="showTip(event, 'fst2', 12)" class="i">xs</span>, <span onmouseout="hideTip(event, 'fst7', 13)" onmouseover="showTip(event, 'fst7', 13)" class="i">i</span>)
    <span onmouseout="hideTip(event, 'fst6', 14)" onmouseover="showTip(event, 'fst6', 14)" class="i">ys</span> 
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst8', 15)" onmouseover="showTip(event, 'fst8', 15)" class="i">ys</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst1', 16)" onmouseover="showTip(event, 'fst1', 16)" class="i">f7</span> <span class="i">xs</span> <span class="c">// val ys : Array = [|[|1; 2; 3; 4; 5|]; [|1; 2; 3; 4; 5|]|]</span>
<span onmouseout="hideTip(event, 'fst8', 17)" onmouseover="showTip(event, 'fst8', 17)" class="i">ys</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst5', 18)" onmouseover="showTip(event, 'fst5', 18)" class="i">GetType</span>()<span class="o">.</span><span class="i">Name</span> <span class="c">// val it : string = &quot;Int32[][]&quot;  </span></pre>
</td>
</tr>
</table>


#### Reshape  

So this is the way to go. For an arbitrary number of dimensions we can use the same set of functions, the only thing left is filling the array with actual values. 

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
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="c">// init array of specific type</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">fill</span> <span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">t</span> (<span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">len</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">int</span>) <span onmouseout="hideTip(event, 'fst5', 5)" onmouseover="showTip(event, 'fst5', 5)" class="i">f</span> <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 6)" onmouseover="showTip(event, 'fst6', 6)" class="i">xs</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst7', 7)" onmouseover="showTip(event, 'fst7', 7)" class="i">Array</span><span class="o">.</span><span class="i">CreateInstance</span>(<span onmouseout="hideTip(event, 'fst2', 8)" onmouseover="showTip(event, 'fst2', 8)" class="i">t</span>, <span onmouseout="hideTip(event, 'fst3', 9)" onmouseover="showTip(event, 'fst3', 9)" class="i">len</span>)
    <span class="k">for</span> <span onmouseout="hideTip(event, 'fst8', 10)" onmouseover="showTip(event, 'fst8', 10)" class="i">i</span> <span class="k">in</span> <span class="n">0..</span><span onmouseout="hideTip(event, 'fst3', 11)" onmouseover="showTip(event, 'fst3', 11)" class="i">len</span> <span class="o">-</span> <span class="n">1</span> <span class="k">do</span> <span onmouseout="hideTip(event, 'fst6', 12)" onmouseover="showTip(event, 'fst6', 12)" class="i">xs</span><span class="o">.</span><span class="i">SetValue</span>(<span onmouseout="hideTip(event, 'fst5', 13)" onmouseover="showTip(event, 'fst5', 13)" class="i">f</span> <span onmouseout="hideTip(event, 'fst8', 14)" onmouseover="showTip(event, 'fst8', 14)" class="i">i</span>, <span onmouseout="hideTip(event, 'fst8', 15)" onmouseover="showTip(event, 'fst8', 15)" class="i">i</span>)
    <span onmouseout="hideTip(event, 'fst6', 16)" onmouseover="showTip(event, 'fst6', 16)" class="i">xs</span>

<span class="c">// prod of prev dimenstions: [|2; 3; 4|] -&gt; [|1; 2; 6; 24|]</span>
<span class="k">let</span> <span class="k">inline</span> <span onmouseout="hideTip(event, 'fst9', 17)" onmouseover="showTip(event, 'fst9', 17)" class="i">prods</span> <span onmouseout="hideTip(event, 'fst10', 18)" onmouseover="showTip(event, 'fst10', 18)" class="i">dims</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst7', 19)" onmouseover="showTip(event, 'fst7', 19)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst11', 20)" onmouseover="showTip(event, 'fst11', 20)" class="i">scan</span> (<span class="o">(*)</span>) <span class="n">1</span> <span onmouseout="hideTip(event, 'fst10', 21)" onmouseover="showTip(event, 'fst10', 21)" class="i">dims</span> 

<span class="c">// all array types from T[][]..[] to T[]</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst12', 22)" onmouseover="showTip(event, 'fst12', 22)" class="i">types</span> (<span onmouseout="hideTip(event, 'fst2', 23)" onmouseover="showTip(event, 'fst2', 23)" class="i">t</span><span class="o">:</span> <span class="i">Type</span>) <span onmouseout="hideTip(event, 'fst13', 24)" onmouseover="showTip(event, 'fst13', 24)" class="i">n</span> <span class="o">=</span>
    (<span onmouseout="hideTip(event, 'fst2', 25)" onmouseover="showTip(event, 'fst2', 25)" class="i">t</span>, <span class="n">0</span>)
    <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst14', 26)" onmouseover="showTip(event, 'fst14', 26)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst15', 27)" onmouseover="showTip(event, 'fst15', 27)" class="i">unfold</span> (<span class="k">fun</span> (<span onmouseout="hideTip(event, 'fst2', 28)" onmouseover="showTip(event, 'fst2', 28)" class="i">t</span>, <span onmouseout="hideTip(event, 'fst16', 29)" onmouseover="showTip(event, 'fst16', 29)" class="i">i</span>) <span class="k">-&gt;</span> <span class="k">if</span> <span onmouseout="hideTip(event, 'fst16', 30)" onmouseover="showTip(event, 'fst16', 30)" class="i">i</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst13', 31)" onmouseover="showTip(event, 'fst13', 31)" class="i">n</span> <span class="k">then</span> <span onmouseout="hideTip(event, 'fst17', 32)" onmouseover="showTip(event, 'fst17', 32)" class="i">None</span> <span class="k">else</span> <span onmouseout="hideTip(event, 'fst18', 33)" onmouseover="showTip(event, 'fst18', 33)" class="i">Some</span> (<span onmouseout="hideTip(event, 'fst2', 34)" onmouseover="showTip(event, 'fst2', 34)" class="i">t</span>, (<span onmouseout="hideTip(event, 'fst2', 35)" onmouseover="showTip(event, 'fst2', 35)" class="i">t</span><span class="o">.</span><span class="i">MakeArrayType</span>(),<span onmouseout="hideTip(event, 'fst16', 36)" onmouseover="showTip(event, 'fst16', 36)" class="i">i</span><span class="o">+</span><span class="n">1</span>)))
    <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst14', 37)" onmouseover="showTip(event, 'fst14', 37)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst19', 38)" onmouseover="showTip(event, 'fst19', 38)" class="i">toArray</span>
    <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst7', 39)" onmouseover="showTip(event, 'fst7', 39)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst20', 40)" onmouseover="showTip(event, 'fst20', 40)" class="i">rev</span></pre>
</td>
</tr>
</table>
For simplicity let's assume that all inputs are already nice and valid (no nulls, the product of dimensions is equal to the length of array and so on), then ````reshape```` might look like:

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
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">reshape</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">arr</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Array</span>) (<span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">dims</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst5', 5)" onmouseover="showTip(event, 'fst5', 5)" class="i">int</span>[]) <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 6)" onmouseover="showTip(event, 'fst6', 6)" class="i">t</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst2', 7)" onmouseover="showTip(event, 'fst2', 7)" class="i">arr</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst7', 8)" onmouseover="showTip(event, 'fst7', 8)" class="i">GetType</span>()<span class="o">.</span><span class="i">GetElementType</span>()
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst8', 9)" onmouseover="showTip(event, 'fst8', 9)" class="i">ts</span> <span class="o">=</span> <span class="i">types</span> <span onmouseout="hideTip(event, 'fst6', 10)" onmouseover="showTip(event, 'fst6', 10)" class="i">t</span> <span onmouseout="hideTip(event, 'fst4', 11)" onmouseover="showTip(event, 'fst4', 11)" class="i">dims</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst9', 12)" onmouseover="showTip(event, 'fst9', 12)" class="i">Length</span>

    <span class="k">let</span> <span class="k">rec</span> <span class="i">init</span> <span class="i">dim</span> <span class="i">k</span> <span class="o">=</span>
        <span class="k">if</span> <span class="i">dim</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 13)" onmouseover="showTip(event, 'fst4', 13)" class="i">dims</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst9', 14)" onmouseover="showTip(event, 'fst9', 14)" class="i">Length</span> <span class="o">-</span> <span class="n">1</span> <span class="k">then</span> 
            <span class="i">fill</span> <span class="i">ts</span><span class="o">.</span>[<span class="i">dim</span>] <span onmouseout="hideTip(event, 'fst4', 15)" onmouseover="showTip(event, 'fst4', 15)" class="i">dims</span><span class="o">.</span>[<span class="i">dim</span>] (<span class="k">fun</span> <span class="i">i</span> <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst2', 16)" onmouseover="showTip(event, 'fst2', 16)" class="i">arr</span><span class="o">.</span><span class="i">GetValue</span> (<span class="i">k</span> <span class="o">*</span> <span onmouseout="hideTip(event, 'fst4', 17)" onmouseover="showTip(event, 'fst4', 17)" class="i">dims</span><span class="o">.</span>[<span class="i">dim</span>] <span class="o">+</span> <span class="i">i</span>))
        <span class="k">else</span>			
            <span class="i">fill</span> <span class="i">ts</span><span class="o">.</span>[<span class="i">dim</span>] <span onmouseout="hideTip(event, 'fst4', 18)" onmouseover="showTip(event, 'fst4', 18)" class="i">dims</span><span class="o">.</span>[<span class="i">dim</span>] (<span class="k">fun</span> <span class="i">i</span> <span class="k">-&gt;</span> <span class="i">init</span> (<span class="i">dim</span><span class="o">+</span><span class="n">1</span>) (<span class="i">k</span> <span class="o">*</span> <span onmouseout="hideTip(event, 'fst4', 19)" onmouseover="showTip(event, 'fst4', 19)" class="i">dims</span><span class="o">.</span>[<span class="i">dim</span>] <span class="o">+</span> <span class="i">i</span>))
    <span class="i">init</span> <span class="n">0</span> <span class="n">0</span></pre>
</td>
</tr>
</table>


This function initializes the elements sequentially:

<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
<span class="l">4: </span>
<span class="l">5: </span>
<span class="l">6: </span>
<span class="l">7: </span>
<span class="l">8: </span>
<span class="l">9: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="i">reshape</span> [|<span class="n">1..</span><span class="n">12</span>|] [|<span class="n">2</span>;<span class="n">3</span>;<span class="n">2</span>|]                                                   
<span class="c">// val it : Array =</span>
<span class="c">//   [|[|[|1; 2|]; [|3; 4|]; [|5; 6|]|]; [|[|7; 8|]; [|9; 10|]; [|11; 12|]|]|]</span>
<span class="i">reshape</span> [|<span class="n">1..</span><span class="n">12</span>|] [|<span class="n">2</span>;<span class="n">6</span>|];;  
<span class="c">// val it : Array = [|[|1; 2; 3; 4; 5; 6|]; [|7; 8; 9; 10; 11; 12|]|]</span>
<span class="i">reshape</span> [|<span class="n">1..</span><span class="n">12</span>|] [|<span class="n">1</span>;<span class="n">1</span>;<span class="n">12</span>|];;                                                   
<span class="c">// val it : Array = [|[|[|1; 2; 3; 4; 5; 6; 7; 8; 9; 10; 11; 12|]|]|]</span>
(<span class="i">reshape</span> [|<span class="n">1..</span><span class="n">12</span>|] [|<span class="n">2</span>;<span class="n">3</span>;<span class="n">2</span>|])<span class="o">.</span><span class="i">GetValue</span> <span class="n">1</span> <span class="o">:?&gt;</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">int</span>[][]
<span class="c">// val it : int [] [] = [|[|7; 8|]; [|9; 10|]; [|11; 12|]|]</span></pre>
</td>
</tr>
</table>  

However, that's not how the original function works. For example (you can try it out in [octave-online](http://octave-online.net/)),

    squeeze(reshape((1:12),[2 3 2])(2,:,:))
     ans =
     
        2    8
        4   10
        6   12
	 

It might take a while to get a feeling how the arrays are reshaped, just run some examples and check where the numbers go. Meanwhile here's a function which does what's required:  

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
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">reshape</span> (<span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">arr</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Array</span>) (<span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">dims</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst5', 5)" onmouseover="showTip(event, 'fst5', 5)" class="i">int</span>[]) <span class="o">=</span> 
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 6)" onmouseover="showTip(event, 'fst6', 6)" class="i">t</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst2', 7)" onmouseover="showTip(event, 'fst2', 7)" class="i">arr</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst7', 8)" onmouseover="showTip(event, 'fst7', 8)" class="i">GetType</span>()<span class="o">.</span><span class="i">GetElementType</span>()

    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst8', 9)" onmouseover="showTip(event, 'fst8', 9)" class="i">ps</span> <span class="o">=</span> <span class="i">prods</span> <span onmouseout="hideTip(event, 'fst4', 10)" onmouseover="showTip(event, 'fst4', 10)" class="i">dims</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, 'fst9', 11)" onmouseover="showTip(event, 'fst9', 11)" class="i">ts</span> <span class="o">=</span> <span class="i">types</span> <span onmouseout="hideTip(event, 'fst6', 12)" onmouseover="showTip(event, 'fst6', 12)" class="i">t</span> <span onmouseout="hideTip(event, 'fst4', 13)" onmouseover="showTip(event, 'fst4', 13)" class="i">dims</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst10', 14)" onmouseover="showTip(event, 'fst10', 14)" class="i">Length</span>

    <span class="k">let</span> <span class="k">rec</span> <span onmouseout="hideTip(event, 'fst11', 15)" onmouseover="showTip(event, 'fst11', 15)" class="i">init</span> <span onmouseout="hideTip(event, 'fst12', 16)" onmouseover="showTip(event, 'fst12', 16)" class="i">dim</span> <span onmouseout="hideTip(event, 'fst13', 17)" onmouseover="showTip(event, 'fst13', 17)" class="i">k</span> <span class="o">=</span>
        <span class="k">if</span> <span onmouseout="hideTip(event, 'fst12', 18)" onmouseover="showTip(event, 'fst12', 18)" class="i">dim</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 19)" onmouseover="showTip(event, 'fst4', 19)" class="i">dims</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst10', 20)" onmouseover="showTip(event, 'fst10', 20)" class="i">Length</span> <span class="o">-</span> <span class="n">1</span> <span class="k">then</span>
            <span class="i">fill</span> <span onmouseout="hideTip(event, 'fst9', 21)" onmouseover="showTip(event, 'fst9', 21)" class="i">ts</span><span class="o">.</span>[<span onmouseout="hideTip(event, 'fst12', 22)" onmouseover="showTip(event, 'fst12', 22)" class="i">dim</span>] <span onmouseout="hideTip(event, 'fst4', 23)" onmouseover="showTip(event, 'fst4', 23)" class="i">dims</span><span class="o">.</span>[<span onmouseout="hideTip(event, 'fst12', 24)" onmouseover="showTip(event, 'fst12', 24)" class="i">dim</span>] (<span class="k">fun</span> <span class="i">i</span> <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst2', 25)" onmouseover="showTip(event, 'fst2', 25)" class="i">arr</span><span class="o">.</span><span class="i">GetValue</span> (<span onmouseout="hideTip(event, 'fst8', 26)" onmouseover="showTip(event, 'fst8', 26)" class="i">ps</span><span class="o">.</span>[<span onmouseout="hideTip(event, 'fst12', 27)" onmouseover="showTip(event, 'fst12', 27)" class="i">dim</span>] <span class="o">*</span> <span class="i">i</span> <span class="o">+</span> <span onmouseout="hideTip(event, 'fst13', 28)" onmouseover="showTip(event, 'fst13', 28)" class="i">k</span>))
        <span class="k">else</span> 
            <span class="i">fill</span> <span onmouseout="hideTip(event, 'fst9', 29)" onmouseover="showTip(event, 'fst9', 29)" class="i">ts</span><span class="o">.</span>[<span onmouseout="hideTip(event, 'fst12', 30)" onmouseover="showTip(event, 'fst12', 30)" class="i">dim</span>] <span onmouseout="hideTip(event, 'fst4', 31)" onmouseover="showTip(event, 'fst4', 31)" class="i">dims</span><span class="o">.</span>[<span onmouseout="hideTip(event, 'fst12', 32)" onmouseover="showTip(event, 'fst12', 32)" class="i">dim</span>] (<span class="k">fun</span> <span class="i">i</span> <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst11', 33)" onmouseover="showTip(event, 'fst11', 33)" class="i">init</span> (<span onmouseout="hideTip(event, 'fst12', 34)" onmouseover="showTip(event, 'fst12', 34)" class="i">dim</span><span class="o">+</span><span class="n">1</span>) (<span onmouseout="hideTip(event, 'fst8', 35)" onmouseover="showTip(event, 'fst8', 35)" class="i">ps</span><span class="o">.</span>[<span onmouseout="hideTip(event, 'fst12', 36)" onmouseover="showTip(event, 'fst12', 36)" class="i">dim</span>] <span class="o">*</span> <span class="i">i</span> <span class="o">+</span> <span onmouseout="hideTip(event, 'fst13', 37)" onmouseover="showTip(event, 'fst13', 37)" class="i">k</span>))
    <span onmouseout="hideTip(event, 'fst11', 38)" onmouseover="showTip(event, 'fst11', 38)" class="i">init</span> <span class="n">0</span> <span class="n">0</span>  
	
(<span onmouseout="hideTip(event, 'fst1', 39)" onmouseover="showTip(event, 'fst1', 39)" class="i">reshape</span> [|<span class="n">1..</span><span class="n">12</span>|] [|<span class="n">2</span>;<span class="n">3</span>;<span class="n">2</span>|])<span class="o">.</span><span class="i">GetValue</span> <span class="n">1</span> <span class="o">:?&gt;</span> <span onmouseout="hideTip(event, 'fst5', 40)" onmouseover="showTip(event, 'fst5', 40)" class="i">int</span>[][];;                             
<span class="c">// val it : int [] [] = [|[|2; 8|]; [|4; 10|]; [|6; 12|]|]</span></pre>
</td>
</tr>
</table>

In the end we do get a new array together with its type information, so the type provider can help you to avoid the casts from ````System.Array````.  

