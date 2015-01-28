---
layout: post
title: F* Challenge or The Tale of Tails
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 1292
wordpress_url: http://luajalla.azurewebsites.net/?p=1292
date: '2013-06-07 15:58:57 +0200'
date_gmt: '2013-06-07 15:58:57 +0200'
tags:
- mono
- fsharp
- fstar
- tail calls
image:
  feature: header3.jpg
  credit: Geneva Lake
comments:
- id: 102
  author: leppie
  author_email: xacc.ide@gmail.com
  author_url: http://ironscheme.codeplex.com
  date: '2013-06-07 18:59:44 +0200'
  date_gmt: '2013-06-07 18:59:44 +0200'
  content: Mono is still horribly broken in terms in tail call support.
- id: 112
  author: luajalla
  author_email: lu-a-jalla@ya.ru
  author_url: ''
  date: '2013-06-07 19:30:27 +0200'
  date_gmt: '2013-06-07 19:30:27 +0200'
  content: |-
    Well, to be fair it works for the most-used straightforward cases I needed before. But suddenly trolling scala devs with tco is not that fun anymore )
    And it's frustrating that I don't really understand the <em>why</em> part here as it goes somewhere to implementation details. Looking for a workaround seems to be the most realistic solution.
---
F* is a verification-oriented programming language developed at Microsoft Research. If you already know F#, or OCaml, or Haskell, or another language from ML family, you’ll find it familiar.  

*[Originally posted [here](http://luajalla.azurewebsites.net/fstar-challenge-or-the-tale-of-tails)].* 

###Resources:  

* F* <a title="F* - Security Distributed Programming with Value-Dependent Types" href="http://research.microsoft.com/en-us/projects/fstar/">project home  
* Rise4Fun – <a title="Rise4Fun - F*" href="http://rise4fun.com/FStar" target="_blank">try it online</a>/go through the <a title="Rise4Fun - F* tutorial" href="http://rise4fun.com/FStar/tutorial/guide" target="_blank">guide</a>  
* F* <a title="F* Download" href="http://research.microsoft.com/en-us/downloads/e9089b8e-8871-46a8-987b-75effdcf70e6/default.aspx" target="_blank">download</a>  

To start experimenting with the language on your machine you need .NET 4.0. With Windows that’s it – just start enjoying verifications. But things are getting more interesting if you’re a mono user, when you want to compile the “right” compiler with blackjack and everything.  

F* Download includes the sources and some binaries to bootstrap the compiler.  
There is a dependency on Z3, we need F# PowerPack libs, fslex, fsyacc and i386 version of dylib for Mac and mono version of `Microsoft.Z3.dll` library (either compile a new one or download <a title="Z3Fs" href="https://github.com/dungpa/Z3Fs/" target="_blank">here</a> or <a title="Z3 Starter" href="https://github.com/luajalla/everything-fun/tree/master/z3" target="_blank">here</a>).  

But first of all, need to admit it works only for the small programs now. That’s why I ain’t gonna list the steps now. They are actually quite straightforward – update the paths [^1] and fix the errors if any (e.g. I replaced the reserved word `const` with `cnst`). You can also define `MONO` symbol and add references to `z3mono.dll`.  

The first wall is `Certify.dll` rule – got StackOverflow here. Given the .dll which is in bin folder by default, everything else compiles, including `fstar.exe`.  

At this point only the simplest programs can be successfully verified. Why only them?  

F* relies on tail call optimizations – lots and lots of recursive functions here. Consider the following simple function, it does nothing usefull but illustrates the problem:  
  
<table class="presmall"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="k">let</span> <span class="k">rec</span> <span class="i" onmouseover="showTip(event, 'fstips1', 1)" onmouseout="hideTip(event, 'fstips1', 1)">f</span> <span class="i" onmouseover="showTip(event, 'fstips2', 2)" onmouseout="hideTip(event, 'fstips2', 2)">x</span> <span class="i" onmouseover="showTip(event, 'fstips3', 3)" onmouseout="hideTip(event, 'fstips3', 3)">cont</span> <span class="o">=</span>                                                       
    <span class="k">if</span> <span class="i" onmouseover="showTip(event, 'fstips2', 4)" onmouseout="hideTip(event, 'fstips2', 4)">x</span> <span class="o">&lt;</span> <span class="n">0</span> <span class="k">then</span> <span class="n">0</span>
    <span class="k">elif</span> <span class="i" onmouseover="showTip(event, 'fstips2', 5)" onmouseout="hideTip(event, 'fstips2', 5)">x</span> <span class="o">=</span> <span class="n">0</span> <span class="k">then</span> <span class="i" onmouseover="showTip(event, 'fstips3', 6)" onmouseout="hideTip(event, 'fstips3', 6)">cont</span> <span class="i" onmouseover="showTip(event, 'fstips2', 7)" onmouseout="hideTip(event, 'fstips2', 7)">x</span>
    <span class="k">else</span> <span class="i" onmouseover="showTip(event, 'fstips1', 8)" onmouseout="hideTip(event, 'fstips1', 8)">f</span> (<span class="i" onmouseover="showTip(event, 'fstips2', 9)" onmouseout="hideTip(event, 'fstips2', 9)">x</span><span class="o">-</span><span class="n">1</span>) (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips2', 10)" onmouseout="hideTip(event, 'fstips2', 10)">x</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips1', 11)" onmouseout="hideTip(event, 'fstips1', 11)">f</span> <span class="i" onmouseover="showTip(event, 'fstips2', 12)" onmouseout="hideTip(event, 'fstips2', 12)">x</span> <span class="i" onmouseover="showTip(event, 'fstips3', 13)" onmouseout="hideTip(event, 'fstips3', 13)">cont</span>)

<span class="i" onmouseover="showTip(event, 'fstips1', 14)" onmouseout="hideTip(event, 'fstips1', 14)">f</span> <span class="n">250000</span> <span class="i" onmouseover="showTip(event, 'fstips4', 15)" onmouseout="hideTip(event, 'fstips4', 15)">id</span> <span class="c">//</span><span class="c">SO</span></pre></td>
</tr></table>

We can verify that the `.tail` instructions are where expected:  

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
<span class="l">33: </span>
<span class="l">34: </span>  

<span class="l">35: </span>
<span class="l">36: </span>
<span class="l">37: </span>
<span class="l">38: </span>
<span class="l">39: </span>
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="k">.method public static</span> 
  <span class="k">int32</span> f (<span class="k">int32</span> x,
	 <span class="k">class</span> [FSharp.Core]Microsoft.FSharp.Core.FSharpFunc`2&lt;<span class="k">int32, int32</span>&gt; cont
	 ) <span class="k">cil managed</span>	
{	
 <span class="k">.custom instance void</span> [FSharp.Core]Microsoft.FSharp.Core
	.CompilationArgumentCountsAttribute::.ctor(<span class="k">int32</span>[]) = (
		01 00 02 00 00 00 01 00 00 00 01 00 00 00 00 00
	)
	<span class="c">// Method begins at RVA 0x2050</span>
	<span class="c">// Code size 35 (0x23)</span>
	.maxstack 8
	<span class="c">// loop start</span>
	 IL_0000: nop
	 IL_0001: ldarg.0
	 IL_0002: ldc.i4.0
	 IL_0003: bge.s IL_0007
  
	 IL_0005: ldc.i4.0
	 IL_0006: ret
  
	 IL_0007: ldarg.0
	 IL_0008: brtrue.s IL_0014
  
	 IL_000a: ldarg.1
	 IL_000b: ldarg.0
	 IL_000c: <span class="k">tail.</span>  <span class="c">// cont x</span>
	 IL_000e: <span class="k">callvirt instance</span> !1 <span class="k">class</span> 
           [FSharp.Core]Microsoft.FSharp.Core.FSharpFunc`2&lt;<span class="k">int32, int32</span>&gt;::Invoke(!0)
	 IL_0013: <span class="k">ret</span>
  
	 IL_0014: ldarg.0
	 IL_0015: ldc.i4.1
	 IL_0016: sub
	 IL_0017: ldarg.1
	 IL_0018: newobj instance void Tailcalls/f@4::.ctor(
           class [FSharp.Core]Microsoft.FSharp.Core.FSharpFunc`2&lt;int32, int32&gt;) 
	 IL_001d: starg.s cont
	 IL_001f: starg.s x
	 IL_0021: br.s IL_0000
	 <span class="c">// end loop</span>
}  <span class="c">// end of method Tailcalls::f</span></pre></td>
</tr></table>

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
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="k">.method public strict virtual</span> 
<span class="k">instance int32</span> Invoke (<span class="k">int32</span> x) cil managed 
{
	<span class="c">// Method begins at RVA 0x2084</span>
	<span class="c">// Code size 16 (0x10)</span>
	.maxstack 8

	IL_0000: nop
	IL_0001: ldarg.1
	IL_0002: ldarg.0
	IL_0003: ldfld class 
          [FSharp.Core]Microsoft.FSharp.Core.FSharpFunc`2&lt;int32, int32&gt; Tailcalls/f@4::cont
	IL_0008: <span class="k">tail.</span>  <span class="c">// f x cont</span>
	IL_000a: <span class="k">call int32</span> Tailcalls::f(<span class="k">int32</span>, 
          <span class="k">class</span> [FSharp.Core]Microsoft.FSharp.Core.FSharpFunc`2&lt;<span class="k">int32, int32</span>&gt;)
	IL_000f: <span class="k">ret</span>
}  <span class="c">// end of method f@4::Invoke</span></pre></td>
</tr></table>  
  
Does it work? Well, for .NET the answer is yes. But on mono the continuation part makes the stack grow. Manipulations with stack size are not the solution, so the sources modification seems to be a way to go.  

There’s an old <a title="Bug 476785 - Tail call support in F#" href="https://bugzilla.novell.com/show_bug.cgi?id=476785" target="_blank">issue</a> with tail calls when called and caller functions have different number of parameters. I thought that can be the case, because of `FSharpFunc` `Invoke`/`InvokeFast` methods, and created a special type for arguments, so all functions had a single parameter. It isn't.  

I still believe it is possible to get F* working on mono [^2]. Hope to find some spare time to dig into the logic and check how it can be rewritten with a simpler recursion, just extremely busy right now ). I also shared some ideas with Nikhil Swamy from F* team – he experimented with mono builds too and definitely knows much better what can be done. That’s an interesting challenge and, of course, the fresh ideas are very welcome!

<p>&nbsp;</p>

[^1]: it’s better to include the full paths – for example I usually happily forget that <i>fsc</i> on my machine means <i>Fast Scala Compiler</i> and waste the time trying to get why it rejects .fs files.
[^2]: everything comes to electricity at the end, right? ;) 

<p><!-- HTML code for ToolTips --></p>
<div class="tip" id="fstips1">
<p>val f : int -&gt; (int -&gt; int) -&gt; int</p>
<p>Full name: Test.f</p>
</div>
<div class="tip" id="fstips2">
<p>val x : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips3">val cont : (int -&gt; int)</div>
<div class="tip" id="fstips4">
<p>val id : 'T -&gt; 'T</p>
<p>Full name: Microsoft.FSharp.Core.Operators.id</p>
</div>