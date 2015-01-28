---
layout: post
title: Z3 Mono Starter
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 272
wordpress_url: http://luajalla.azurewebsites.net/?p=272
date: '2013-05-24 00:20:00 +0200'
date_gmt: '2013-05-24 00:20:00 +0200'
tags:
- z3
- mono
- fsharp
- quotations
image:
  feature: header3.jpg
comments:
- id: 12
  author: dungpa
  author_email: phananhdung309@yahoo.com
  author_url: ''
  date: '2013-05-24 05:40:02 +0200'
  date_gmt: '2013-05-24 05:40:02 +0200'
  content: "I have a Github repo to play around with Z3 and F#,. There is a lot of
    examples, you might want to take a look https://github.com/dungpa/Z3Fs\r\n\r\nI
    once asked a question on how to use Z3 on Mono in SO, which is easier to use with
    binary distribution http://stackoverflow.com/questions/14307192/use-z3-managed-api-on-mono"
- id: 22
  author: luajalla
  author_email: lu-a-jalla@ya.ru
  author_url: ''
  date: '2013-05-24 14:07:03 +0200'
  date_gmt: '2013-05-24 14:07:03 +0200'
  content: "Thank you, looks interesting - I like this custom operators approach!
    \r\nDid you try OCaml bindings? For me it wasn't really a choice as the main goal
    was to get F* working on mono, but how is it comparing to dotnet ones?"
- id: 32
  author: dungpa
  author_email: phananhdung309@yahoo.com
  author_url: ''
  date: '2013-05-24 14:56:27 +0200'
  date_gmt: '2013-05-24 14:56:27 +0200'
  content: "I tried the old version of OCaml binding. IMO, it's more verbose and less
    intuitive than the F# version. Z3 team is working on a new OCaml API at http://z3.codeplex.com/SourceControl/list/changesets?branch=ml-ng
    so hopefully it will get better. \r\n\r\nIn the Z3's Windows installer, there
    is utils/Quotations.fs module which compiles a big subset of F# to SMT and verify
    or prove theorems. It's very similar to your approach here. Unfortunately, it
    currently uses the deprecated API (v3.x). I would like to update it to the latest
    API but never get round to do so :-).\r\n\r\nI'm looking forward to your post
    regarding F* and Mono."
- id: 42
  author: luajalla
  author_email: lu-a-jalla@ya.ru
  author_url: ''
  date: '2013-05-24 22:51:46 +0200'
  date_gmt: '2013-05-24 22:51:46 +0200'
  content: |-
    Yeah, it is. And the most annoying is the context (in all versions) - still not sure what is the best way to hide it.
    The point about Windows explains why I didn't find it on Mac ) Wish there were more hours in a day...
- id: 52
  author: 'F# Weekly #21 2013 | Sergey Tihon&#039;s Blog'
  author_email: ''
  author_url: http://sergeytihon.wordpress.com/2013/05/27/f-weekly-21-2013/
  date: '2013-05-26 21:02:10 +0200'
  date_gmt: '2013-05-26 21:02:10 +0200'
  content: '[...] Natallie Baikevich posted &#8220;Z3 Mono Starter&#8220;. [...]'
- id: 552
  author: Yu
  author_email: baiyu0584@gmail.com
  author_url: ''
  date: '2014-07-19 12:23:17 +0200'
  date_gmt: '2014-07-19 12:23:17 +0200'
  content: "Thanks! This really hepls!\r\n\r\nBy the way, I'm using OS X Mavericks
    10.9.4, and in your third step I need to replace the option -fopenmp by -D_NO_OMP_
    to forbid OpenMP, otherwise it won't compile."
- id: 562
  author: luajalla
  author_email: lu-a-jalla@ya.ru
  author_url: ''
  date: '2014-07-22 17:52:39 +0200'
  date_gmt: '2014-07-22 17:52:39 +0200'
  content: Thank you for letting me know, will update the post! (hasn't tried it on
    Mavericks)
---
Have you ever thought how exciting verification tools are? How do you *prove* the *correctness* of a program? Of course, the green tests bring some confidence... but we all know it's not a proof.  

> This blog was originally posted [here](http://luajalla.azurewebsites.net/z3-mono-starter). 

![Pex Logo]({{ site.url }}/images/pex.png)
{: .image-right}
Just look at <a title="Pex" href="http://www.pexforfun.com" target="_blank">Pex</a>, it definitely can't leave anybody cold (recommendation: skip this step for now, it was a trap).  

But this story is not about Pex, but what powers it - Z3, an efficient SMT solver from Microsoft Research. You may ask why you should care (at least I always  do when looking at something new - a tool/language/app/whatever), the answer is simple - because we want the proof, not only a bunch of valid inputs. The work made me a bit paranoid to this point - it takes more time, but the shining correctness allows you not to worry about how much customers can lose just because someone forgot about a not-likely-to-happen workflow, like an unexpected negative number from nowhere -> power -> NaN (surprise, surprise!).  

  
###Resources:    

![Z3 Logo]({{ site.url }}/images/z3.png)
{: .image-right }    
* Z3 on <a title="Z3 Home" href="http://z3.codeplex.com/" target="_blank">codeplex</a>, with documentation, papers and slides.  
* Try it online with <a title="Rise4Fun" href="http://rise4fun.com/Z3/tutorial/guide" target="_blank">Rise4Fun</a>.  
* Z3 Constraint Solver on <a title="Nikolaj Bjørner and Leonardo de Moura: The Z3 Constraint Solver" href="http://channel9.msdn.com/blogs/peli/the-z3-constraint-solver" target="_blank">Ch9 </a>.  
* Original home of <a title="Z3: Theorem Prover" href="http://research.microsoft.com/en-us/um/redmond/projects/z3/old/index.html" target="_blank">Z3 project</a>.  
  
Z3 is supported on Windows, OSX, Linux and FreeBSD and has an extensive API:  C, C++, OCaml, .NET languages, Python, Java.  
  
###Z3 + Mono
The standard Z3 distrubution for OSX comes with x64 binaries, but to get it working with Mono we need to compile it ourselves with x86 architecture.  

<strong>1.</strong> Download the sources <a title="Z3 Home" href="http://z3.codeplex.com/" target="_blank">here</a>.  
<strong>2.</strong> Open the folder and generate the makefiles.  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
</pre></td>
<td class="snippet"><pre class="fssnip">
python scripts/mk_make.py
<span class="k">cd</span> build</pre></td>
</tr></table>
<strong>3.</strong> We're interested in `config.mk` file. By default the result architecture is x86_x64, that's where the compiler flags help:

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>  

<span class="l"> 2: </span>
<span class="l"> 3: </span>
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="k">CXXFLAGS</span>= -D_MP_INTERNAL -m32  -c -fopenmp -mfpmath=sse -O3 -D _EXTERNAL_RELEASE
-fomit-frame-pointer -fPIC -msse -msse2
<span class="k">LINK_FLAGS</span>=-m32
<span class="k">SLINK_FLAGS</span>=-dynamiclib -m32</pre>
</td></tr></table>  

*Update:* according to the Yu's comment you might need to replace the option `-fopenmp` with `-D_NO_OMP_`.  

<strong>4.</strong> Just make it [^1]. The recent Z3 versions require a little change as compiler gives `typename outside of template` error, I just removed `typename` from `fdd.h`.  
  
<strong>5.</strong> Check the architecture

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
</pre></td>
<td class="snippet"><pre class="fssnip">
$ <span class="k">lipo</span> -info libz3.dylib
 Non-fat file: libz3.dylib is architecture: i386</pre></td>
</tr></table>
<strong>6.</strong>  cd to `z3/src/api/dotnet` folder and compile Microsoft.Z3.csproj with Mono/.NET 4.0 as a target framework [^2]. Also you'd need to add a config file to map `libz3.dll` to our fresh native `libz3.dylib`.

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
</pre></td>
<td class="snippet"><pre class="fssnip">
&lt;<span class="k">?xml</span> version=<span class="s">"1.0"</span> encoding=<span class="s">"utf-8"</span>&gt;
&lt;<span class="k">configuration</span>&gt;
    &lt;<span class="k">dllmap</span> dll=<span class="s">"libz3.dll"</span> target=<span class="s">"libz3.dylib"</span> os=<span class="s">"osx"</span> cpu=<span class="s">"x86"</span>/&gt;
&lt;/<span class="k">configuration</span>&gt;</pre></td>
</tr></table>

Now we are ready to try it!

###Examples time  
Let's start with a simple script using official Z3 API, the C# version of examples below can be found in Z3 <a title="Z3 example (C#)" href="http://z3.codeplex.com/SourceControl/latest#examples/dotnet/Program.cs" target="_blank">repository</a>. There's also a nice set of F# examples - I couldn't leave it just in comments - <a title="Z3Fs" href="https://github.com/dungpa/Z3Fs/tree/master/Z3Fs" target="_blank">Z3Fs</a>, a DSL to solve SMT problems by  <a href="https://twitter.com/dungpa">@dungpa</a>.  

For simplicity we assume that Microsoft.Z3.dll, config and libz3.dylib are in the script's folder.  

*Prove that x = y implies g(x) = g(y).*  

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
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="prep">#r</span> <span class="s">"</span><span class="s">Microsoft</span><span class="s">.</span><span class="s">Z3</span><span class="s">.</span><span class="s">dll</span><span class="s">"</span>  
  
<span class="omitted" onmouseover="showTip(event, 'fstips1', 1)" onmouseout="hideTip(event, 'fstips1', 1)">usings</span>  
  
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips2', 2)" onmouseout="hideTip(event, 'fstips2', 2)">prove</span> (<span class="i" onmouseover="showTip(event, 'fstips3', 3)" onmouseout="hideTip(event, 'fstips3', 3)">ctx</span><span class="o">:</span> <span class="i" onmouseover="showTip(event, 'fstips4', 4)" onmouseout="hideTip(event, 'fstips4', 4)">Context</span>) <span class="i" onmouseover="showTip(event, 'fstips5', 5)" onmouseout="hideTip(event, 'fstips5', 5)">f</span> <span class="o">=</span>  
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips6', 6)" onmouseout="hideTip(event, 'fstips6', 6)">s</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 7)" onmouseout="hideTip(event, 'fstips3', 7)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips7', 8)" onmouseout="hideTip(event, 'fstips7', 8)">MkSolver</span>()  
    <span class="i" onmouseover="showTip(event, 'fstips6', 9)" onmouseout="hideTip(event, 'fstips6', 9)">s</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips8', 10)" onmouseout="hideTip(event, 'fstips8', 10)">Assert</span> (<span class="i" onmouseover="showTip(event, 'fstips3', 11)" onmouseout="hideTip(event, 'fstips3', 11)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips9', 12)" onmouseout="hideTip(event, 'fstips9', 12)">MkNot</span> <span class="i" onmouseover="showTip(event, 'fstips5', 13)" onmouseout="hideTip(event, 'fstips5', 13)">f</span>)  
    <span class="k">match</span> <span class="i" onmouseover="showTip(event, 'fstips6', 14)" onmouseout="hideTip(event, 'fstips6', 14)">s</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips10', 15)" onmouseout="hideTip(event, 'fstips10', 15)">Check</span>() <span class="k">with</span>  
    | <span class="i" onmouseover="showTip(event, 'fstips11', 16)" onmouseout="hideTip(event, 'fstips11', 16)">Status</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips12', 17)" onmouseout="hideTip(event, 'fstips12', 17)">UNKNOWN</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips13', 18)" onmouseout="hideTip(event, 'fstips13', 18)">printfn</span> <span class="s">"</span><span class="s">unknown </span><span class="s">because </span><span class="s">%</span><span class="s">A</span><span class="s">"</span> <span class="i" onmouseover="showTip(event, 'fstips6', 19)" onmouseout="hideTip(event, 'fstips6', 19)">s</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips14', 20)" onmouseout="hideTip(event, 'fstips14', 20)">ReasonUnknown</span>  
    | <span class="i" onmouseover="showTip(event, 'fstips11', 21)" onmouseout="hideTip(event, 'fstips11', 21)">Status</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips15', 22)" onmouseout="hideTip(event, 'fstips15', 22)">SATISFIABLE</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips13', 23)" onmouseout="hideTip(event, 'fstips13', 23)">printfn</span> <span class="s">"</span><span class="s">error</span><span class="s">"</span>  
    | _ <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips13', 24)" onmouseout="hideTip(event, 'fstips13', 24)">printfn</span> <span class="s">"</span><span class="s">ok</span><span class="s">, </span><span class="s">proof</span><span class="s">: </span><span class="s">%</span><span class="s">A</span><span class="s">"</span> <span class="i" onmouseover="showTip(event, 'fstips6', 25)" onmouseout="hideTip(event, 'fstips6', 25)">s</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips16', 26)" onmouseout="hideTip(event, 'fstips16', 26)">Proof</span>  
    
<span class="c">///</span><span class="c">Prove </span><span class="c">that </span><span class="c">x </span><span class="c">= </span><span class="c">y </span><span class="c">implies </span><span class="c">g(x) </span><span class="c">= </span><span class="c">g(y)</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips17', 27)" onmouseout="hideTip(event, 'fstips17', 27)">proveSample</span>() <span class="o">=</span>  
    <span class="omitted" onmouseover="showTip(event, 'fstips18', 28)" onmouseout="hideTip(event, 'fstips18', 28)">create context</span>

    <span class="c">//</span><span class="c">create </span><span class="c">uninterpreted </span><span class="c">type</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips19', 29)" onmouseout="hideTip(event, 'fstips19', 29)">U</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 30)" onmouseout="hideTip(event, 'fstips3', 30)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips20', 31)" onmouseout="hideTip(event, 'fstips20', 31)">MkUninterpretedSort</span> (<span class="i" onmouseover="showTip(event, 'fstips3', 32)" onmouseout="hideTip(event, 'fstips3', 32)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips21', 33)" onmouseout="hideTip(event, 'fstips21', 33)">MkSymbol</span> <span class="s">"</span><span class="s">U</span><span class="s">"</span>)
    <span class="c">//</span><span class="c">declare </span><span class="c">function </span><span class="c">g</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips22', 34)" onmouseout="hideTip(event, 'fstips22', 34)">g</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 35)" onmouseout="hideTip(event, 'fstips3', 35)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips23', 36)" onmouseout="hideTip(event, 'fstips23', 36)">MkFuncDecl</span>(<span class="s">"</span><span class="s">g</span><span class="s">"</span>, <span class="i" onmouseover="showTip(event, 'fstips19', 37)" onmouseout="hideTip(event, 'fstips19', 37)">U</span>, <span class="i" onmouseover="showTip(event, 'fstips19', 38)" onmouseout="hideTip(event, 'fstips19', 38)">U</span>)

    <span class="c">//</span><span class="c">create </span><span class="c">x </span><span class="c">and </span><span class="c">y</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips24', 39)" onmouseout="hideTip(event, 'fstips24', 39)">x</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 40)" onmouseout="hideTip(event, 'fstips3', 40)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips25', 41)" onmouseout="hideTip(event, 'fstips25', 41)">MkConst</span>(<span class="s">"</span><span class="s">x</span><span class="s">"</span>, <span class="i" onmouseover="showTip(event, 'fstips19', 42)" onmouseout="hideTip(event, 'fstips19', 42)">U</span>)
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips26', 43)" onmouseout="hideTip(event, 'fstips26', 43)">y</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 44)" onmouseout="hideTip(event, 'fstips3', 44)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips25', 45)" onmouseout="hideTip(event, 'fstips25', 45)">MkConst</span>(<span class="s">"</span><span class="s">y</span><span class="s">"</span>, <span class="i" onmouseover="showTip(event, 'fstips19', 46)" onmouseout="hideTip(event, 'fstips19', 46)">U</span>)
   <span class="c">//</span><span class="c">create </span><span class="c">g(x), </span><span class="c">g(y)</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips27', 47)" onmouseout="hideTip(event, 'fstips27', 47)">gx</span>, <span class="i" onmouseover="showTip(event, 'fstips28', 48)" onmouseout="hideTip(event, 'fstips28', 48)">gy</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips22', 49)" onmouseout="hideTip(event, 'fstips22', 49)">g</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips24', 50)" onmouseout="hideTip(event, 'fstips24', 50)">x</span>], <span class="i" onmouseover="showTip(event, 'fstips22', 51)" onmouseout="hideTip(event, 'fstips22', 51)">g</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips26', 52)" onmouseout="hideTip(event, 'fstips26', 52)">y</span>]

    <span class="c">//</span><span class="c">assert </span><span class="c">x </span><span class="c">= </span><span class="c">y</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips29', 53)" onmouseout="hideTip(event, 'fstips29', 53)">eq</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 54)" onmouseout="hideTip(event, 'fstips3', 54)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips30', 55)" onmouseout="hideTip(event, 'fstips30', 55)">MkEq</span>(<span class="i" onmouseover="showTip(event, 'fstips24', 56)" onmouseout="hideTip(event, 'fstips24', 56)">x</span>, <span class="i" onmouseover="showTip(event, 'fstips26', 57)" onmouseout="hideTip(event, 'fstips26', 57)">y</span>)

    <span class="c">//</span><span class="c">prove </span><span class="c">g(x) </span><span class="c">= </span><span class="c">g(y)</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips5', 58)" onmouseout="hideTip(event, 'fstips5', 58)">f</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips3', 59)" onmouseout="hideTip(event, 'fstips3', 59)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips30', 60)" onmouseout="hideTip(event, 'fstips30', 60)">MkEq</span>(<span class="i" onmouseover="showTip(event, 'fstips27', 61)" onmouseout="hideTip(event, 'fstips27', 61)">gx</span>, <span class="i" onmouseover="showTip(event, 'fstips28', 62)" onmouseout="hideTip(event, 'fstips28', 62)">gy</span>)
    <span class="i" onmouseover="showTip(event, 'fstips13', 63)" onmouseout="hideTip(event, 'fstips13', 63)">printfn</span> <span class="s">"</span><span class="s">prove</span><span class="s">: </span><span class="s">x </span><span class="s">= </span><span class="s">y </span><span class="s">implies </span><span class="s">g</span><span class="s">(</span><span class="s">x</span><span class="s">) </span><span class="s">= </span><span class="s">g</span><span class="s">(</span><span class="s">y</span><span class="s">)</span><span class="s">"</span>
    <span class="i" onmouseover="showTip(event, 'fstips2', 64)" onmouseout="hideTip(event, 'fstips2', 64)">prove</span> <span class="i" onmouseover="showTip(event, 'fstips3', 65)" onmouseout="hideTip(event, 'fstips3', 65)">ctx</span> (<span class="i" onmouseover="showTip(event, 'fstips3', 66)" onmouseout="hideTip(event, 'fstips3', 66)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips31', 67)" onmouseout="hideTip(event, 'fstips31', 67)">MkImplies</span>(<span class="i" onmouseover="showTip(event, 'fstips29', 68)" onmouseout="hideTip(event, 'fstips29', 68)">eq</span>, <span class="i" onmouseover="showTip(event, 'fstips5', 69)" onmouseout="hideTip(event, 'fstips5', 69)">f</span>))

<span class="i" onmouseover="showTip(event, 'fstips17', 70)" onmouseout="hideTip(event, 'fstips17', 70)">proveSample</span>()</pre></td>
</tr>
</table>  

Well, it's a pain to write all those `ctx.Mk*`. That's where F# quotations come to hand. Some papers and slides mention Z3 support for quotations, though they seem to be a bit outdated - haven't seen anything similar in the sources. Here is an example of what it can look like.  

*Simplify expression "x + (y - (x + z))"*  

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
</pre></td>
<td class="snippet"><pre class="fssnip"><span class="c">///</span><span class="c">Simplified</span><span class="c">SpecificCall</span>
<span class="k">let</span> <span class="k">inline</span> (|<span class="i" onmouseover="showTip(event, 'fstips32', 71)" onmouseout="hideTip(event, 'fstips32', 71)">Func</span>|_|) <span class="i" onmouseover="showTip(event, 'fstips33', 72)" onmouseout="hideTip(event, 'fstips33', 72)">expr</span> <span class="o">=</span> <span class="omitted" onmouseover="showTip(event, 'fstips34', 73)" onmouseout="hideTip(event, 'fstips34', 73)">(...)</span>

<span class="c">///</span><span class="c">From </span><span class="c">quotations </span><span class="c">to </span><span class="c">Z3 </span><span class="c">objects</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips35', 74)" onmouseout="hideTip(event, 'fstips35', 74)">z3</span> <span class="i" onmouseover="showTip(event, 'fstips33', 75)" onmouseout="hideTip(event, 'fstips33', 75)">expr</span> <span class="o">=</span>
    <span class="omitted" onmouseover="showTip(event, 'fstips36', 76)" onmouseout="hideTip(event, 'fstips36', 76)">create context</span>
    <span class="k">let</span> <span class="k">rec</span> <span class="i" onmouseover="showTip(event, 'fstips37', 77)" onmouseout="hideTip(event, 'fstips37', 77)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips33', 78)" onmouseout="hideTip(event, 'fstips33', 78)">expr</span> <span class="o">=</span>
        <span class="k">match</span> <span class="i" onmouseover="showTip(event, 'fstips33', 79)" onmouseout="hideTip(event, 'fstips33', 79)">expr</span> <span class="k">with</span>
        | <span class="i" onmouseover="showTip(event, 'fstips38', 80)" onmouseout="hideTip(event, 'fstips38', 80)">Func</span> &lt;@@ (<span class="o">+</span>) @@&gt; [<span class="i" onmouseover="showTip(event, 'fstips39', 81)" onmouseout="hideTip(event, 'fstips39', 81)">x</span>; <span class="i" onmouseover="showTip(event, 'fstips40', 82)" onmouseout="hideTip(event, 'fstips40', 82)">y</span>] <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips3', 83)" onmouseout="hideTip(event, 'fstips3', 83)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips41', 84)" onmouseout="hideTip(event, 'fstips41', 84)">MkAdd</span> (<span class="i" onmouseover="showTip(event, 'fstips37', 85)" onmouseout="hideTip(event, 'fstips37', 85)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips39', 86)" onmouseout="hideTip(event, 'fstips39', 86)">x</span>, <span class="i" onmouseover="showTip(event, 'fstips37', 87)" onmouseout="hideTip(event, 'fstips37', 87)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips40', 88)" onmouseout="hideTip(event, 'fstips40', 88)">y</span>)
        | <span class="i" onmouseover="showTip(event, 'fstips38', 89)" onmouseout="hideTip(event, 'fstips38', 89)">Func</span> &lt;@@ (<span class="o">-</span>) @@&gt; [<span class="i" onmouseover="showTip(event, 'fstips39', 90)" onmouseout="hideTip(event, 'fstips39', 90)">x</span>; <span class="i" onmouseover="showTip(event, 'fstips40', 91)" onmouseout="hideTip(event, 'fstips40', 91)">y</span>] <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips3', 92)" onmouseout="hideTip(event, 'fstips3', 92)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips42', 93)" onmouseout="hideTip(event, 'fstips42', 93)">MkSub</span> (<span class="i" onmouseover="showTip(event, 'fstips37', 94)" onmouseout="hideTip(event, 'fstips37', 94)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips39', 95)" onmouseout="hideTip(event, 'fstips39', 95)">x</span>, <span class="i" onmouseover="showTip(event, 'fstips37', 96)" onmouseout="hideTip(event, 'fstips37', 96)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips40', 97)" onmouseout="hideTip(event, 'fstips40', 97)">y</span>)
        | <span class="i" onmouseover="showTip(event, 'fstips43', 98)" onmouseout="hideTip(event, 'fstips43', 98)">Lambdas</span> (_, <span class="i" onmouseover="showTip(event, 'fstips44', 99)" onmouseout="hideTip(event, 'fstips44', 99)">e</span>) <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips37', 100)" onmouseout="hideTip(event, 'fstips37', 100)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips44', 101)" onmouseout="hideTip(event, 'fstips44', 101)">e</span>
        | <span class="i" onmouseover="showTip(event, 'fstips45', 102)" onmouseout="hideTip(event, 'fstips45', 102)">Var</span> <span class="i" onmouseover="showTip(event, 'fstips46', 103)" onmouseout="hideTip(event, 'fstips46', 103)">var</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips3', 104)" onmouseout="hideTip(event, 'fstips3', 104)">ctx</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips47', 105)" onmouseout="hideTip(event, 'fstips47', 105)">MkIntConst</span> <span class="i" onmouseover="showTip(event, 'fstips46', 106)" onmouseout="hideTip(event, 'fstips46', 106)">var</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips48', 107)" onmouseout="hideTip(event, 'fstips48', 107)">Name</span> <span class="o">:&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips49', 108)" onmouseout="hideTip(event, 'fstips49', 108)">ArithExpr</span>
        | <span class="i" onmouseover="showTip(event, 'fstips39', 109)" onmouseout="hideTip(event, 'fstips39', 109)">x</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips50', 110)" onmouseout="hideTip(event, 'fstips50', 110)">failwith</span> (<span class="i" onmouseover="showTip(event, 'fstips51', 111)" onmouseout="hideTip(event, 'fstips51', 111)">sprintf</span> <span class="s">"</span><span class="s">unknown </span><span class="s">expression </span><span class="s">%</span><span class="s">A</span><span class="s">"</span> <span class="i" onmouseover="showTip(event, 'fstips39', 112)" onmouseout="hideTip(event, 'fstips39', 112)">x</span>)
    <span class="i" onmouseover="showTip(event, 'fstips37', 113)" onmouseout="hideTip(event, 'fstips37', 113)">unquote</span> <span class="i" onmouseover="showTip(event, 'fstips33', 114)" onmouseout="hideTip(event, 'fstips33', 114)">expr</span>

<span class="c">///</span><span class="c">Simplify </span><span class="c">expression </span><span class="c">"x </span><span class="c">+ </span><span class="c">(y </span><span class="c">- </span><span class="c">(x </span><span class="c">+ </span><span class="c">z))"</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips52', 115)" onmouseout="hideTip(event, 'fstips52', 115)">simplifier</span>() <span class="o">=</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips53', 116)" onmouseout="hideTip(event, 'fstips53', 116)">t1</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips35', 117)" onmouseout="hideTip(event, 'fstips35', 117)">z3</span> &lt;@@ <span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips54', 118)" onmouseout="hideTip(event, 'fstips54', 118)">x</span> <span class="i" onmouseover="showTip(event, 'fstips55', 119)" onmouseout="hideTip(event, 'fstips55', 119)">y</span> <span class="i" onmouseover="showTip(event, 'fstips56', 120)" onmouseout="hideTip(event, 'fstips56', 120)">z</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips54', 121)" onmouseout="hideTip(event, 'fstips54', 121)">x</span> <span class="o">+</span> (<span class="i" onmouseover="showTip(event, 'fstips55', 122)" onmouseout="hideTip(event, 'fstips55', 122)">y</span> <span class="o">-</span> (<span class="i" onmouseover="showTip(event, 'fstips54', 123)" onmouseout="hideTip(event, 'fstips54', 123)">x</span> <span class="o">+</span> <span class="i" onmouseover="showTip(event, 'fstips56', 124)" onmouseout="hideTip(event, 'fstips56', 124)">z</span>)) @@&gt;  
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips57', 125)" onmouseout="hideTip(event, 'fstips57', 125)">t2</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips53', 126)" onmouseout="hideTip(event, 'fstips53', 126)">t1</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips58', 127)" onmouseout="hideTip(event, 'fstips58', 127)">Simplify</span>()
    <span class="i" onmouseover="showTip(event, 'fstips13', 128)" onmouseout="hideTip(event, 'fstips13', 128)">printfn</span> <span class="s">"</span><span class="s">%</span><span class="s">A </span><span class="s">-</span><span class="s">&gt; </span><span class="s">%</span><span class="s">A</span><span class="s">"</span> <span class="i" onmouseover="showTip(event, 'fstips53', 129)" onmouseout="hideTip(event, 'fstips53', 129)">t1</span> <span class="i" onmouseover="showTip(event, 'fstips57', 130)" onmouseout="hideTip(event, 'fstips57', 130)">t2</span>

<span class="i" onmouseover="showTip(event, 'fstips52', 131)" onmouseout="hideTip(event, 'fstips52', 131)">simplifier</span>()</pre></td></tr></table>

Looks much better, doesn't it?  

*Next time: bringing another f-language to mono...*  
<br/>

[^1]: don't forget to clean up (*.a* and *.obj* files) the folder if you have built x64 lib before.
[^2]: MD-generated makefile & co are available on <a title="Sample on GitHub" href="https://github.com/luajalla/everything-fun/tree/master/z3" target="_blank">github</a>

<p><!-- HTML code for ToolTips --></p>
<div class="tip" id="fstips1">open System<br />
open Microsoft.Z3<br />
open Microsoft.FSharp.Quotations<br />
open DerivedPatterns<br />
open Patterns</div>
<div class="tip" id="fstips2">
<p>val prove : Context -&gt; BoolExpr -&gt; unit</p>
<p>Full name: GoZ3.prove</p>
</div>
<div class="tip" id="fstips3">
<p>val ctx : Context</p>
<p>type: Context<br />
implements: IDisposable</p>
</div>
<div class="tip" id="fstips4">
<p>type Context =<br />
class<br />
new : unit -&gt; Microsoft.Z3.Context<br />
new : System.Collections.Generic.Dictionary&lt;string,string&gt; -&gt; Microsoft.Z3.Context<br />
member And : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member AndThen : Microsoft.Z3.Tactic * Microsoft.Z3.Tactic * Microsoft.Z3.Tactic [] -&gt; Microsoft.Z3.Tactic<br />
member BenchmarkToSMTString : string * string * string * string * Microsoft.Z3.BoolExpr [] * Microsoft.Z3.BoolExpr -&gt; string<br />
member BoolSort : Microsoft.Z3.BoolSort<br />
member Cond : Microsoft.Z3.Probe * Microsoft.Z3.Tactic * Microsoft.Z3.Tactic -&gt; Microsoft.Z3.Tactic<br />
member ConstProbe : float -&gt; Microsoft.Z3.Probe<br />
member Dispose : unit -&gt; unit<br />
member Eq : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member Fail : unit -&gt; Microsoft.Z3.Tactic<br />
member FailIf : Microsoft.Z3.Probe -&gt; Microsoft.Z3.Tactic<br />
member FailIfNotDecided : unit -&gt; Microsoft.Z3.Tactic<br />
member Ge : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member GetParamValue : string -&gt; string<br />
member Gt : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member IntSort : Microsoft.Z3.IntSort<br />
member Interrupt : unit -&gt; unit<br />
member Le : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member Lt : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member MkAdd : Microsoft.Z3.ArithExpr [] -&gt; Microsoft.Z3.ArithExpr<br />
member MkAnd : Microsoft.Z3.BoolExpr [] -&gt; Microsoft.Z3.BoolExpr<br />
member MkApp : Microsoft.Z3.FuncDecl * Microsoft.Z3.Expr [] -&gt; Microsoft.Z3.Expr<br />
member MkArrayConst : Microsoft.Z3.Symbol * Microsoft.Z3.Sort * Microsoft.Z3.Sort -&gt; Microsoft.Z3.ArrayExpr<br />
member MkArrayConst : string * Microsoft.Z3.Sort * Microsoft.Z3.Sort -&gt; Microsoft.Z3.ArrayExpr<br />
member MkArraySort : Microsoft.Z3.Sort * Microsoft.Z3.Sort -&gt; Microsoft.Z3.ArraySort<br />
member MkBV : string * uint32 -&gt; Microsoft.Z3.BitVecNum<br />
member MkBV : int * uint32 -&gt; Microsoft.Z3.BitVecNum<br />
member MkBV : uint32 * uint32 -&gt; Microsoft.Z3.BitVecNum<br />
member MkBV : int64 * uint32 -&gt; Microsoft.Z3.BitVecNum<br />
member MkBV : uint64 * uint32 -&gt; Microsoft.Z3.BitVecNum<br />
member MkBV2Int : Microsoft.Z3.BitVecExpr * bool -&gt; Microsoft.Z3.IntExpr<br />
member MkBVAND : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVASHR : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVAdd : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVAddNoOverflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr * bool -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVAddNoUnderflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVConst : Microsoft.Z3.Symbol * uint32 -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVConst : string * uint32 -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVLSHR : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVMul : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVMulNoOverflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr * bool -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVMulNoUnderflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVNAND : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVNOR : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVNeg : Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVNegNoOverflow : Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVNot : Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVOR : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVRedAND : Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVRedOR : Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVRotateLeft : uint32 * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVRotateLeft : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVRotateRight : uint32 * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVRotateRight : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVSDiv : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVSDivNoOverflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVSGE : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVSGT : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVSHL : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVSLE : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVSLT : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVSMod : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVSRem : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVSub : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVSubNoOverflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVSubNoUnderflow : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr * bool -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVUDiv : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVUGE : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVUGT : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVULE : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVULT : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkBVURem : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVXNOR : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBVXOR : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkBitVecSort : uint32 -&gt; Microsoft.Z3.BitVecSort<br />
member MkBool : bool -&gt; Microsoft.Z3.BoolExpr<br />
member MkBoolConst : Microsoft.Z3.Symbol -&gt; Microsoft.Z3.BoolExpr<br />
member MkBoolConst : string -&gt; Microsoft.Z3.BoolExpr<br />
member MkBoolSort : unit -&gt; Microsoft.Z3.BoolSort<br />
member MkBound : uint32 * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkConcat : Microsoft.Z3.BitVecExpr * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkConst : Microsoft.Z3.FuncDecl -&gt; Microsoft.Z3.Expr<br />
member MkConst : Microsoft.Z3.Symbol * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkConst : string * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkConstArray : Microsoft.Z3.Sort * Microsoft.Z3.Expr -&gt; Microsoft.Z3.ArrayExpr<br />
member MkConstDecl : Microsoft.Z3.Symbol * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkConstDecl : string * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkConstructor : Microsoft.Z3.Symbol * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol [] * Microsoft.Z3.Sort [] * uint32 [] -&gt; Microsoft.Z3.Constructor<br />
member MkConstructor : string * string * string [] * Microsoft.Z3.Sort [] * uint32 [] -&gt; Microsoft.Z3.Constructor<br />
member MkDatatypeSort : Microsoft.Z3.Symbol * Microsoft.Z3.Constructor [] -&gt; Microsoft.Z3.DatatypeSort<br />
member MkDatatypeSort : string * Microsoft.Z3.Constructor [] -&gt; Microsoft.Z3.DatatypeSort<br />
member MkDatatypeSorts : Microsoft.Z3.Symbol [] * Microsoft.Z3.Constructor [] [] -&gt; Microsoft.Z3.DatatypeSort []<br />
member MkDatatypeSorts : string [] * Microsoft.Z3.Constructor [] [] -&gt; Microsoft.Z3.DatatypeSort []<br />
member MkDistinct : Microsoft.Z3.Expr [] -&gt; Microsoft.Z3.BoolExpr<br />
member MkDiv : Microsoft.Z3.ArithExpr * Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.ArithExpr<br />
member MkEmptySet : Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkEnumSort : Microsoft.Z3.Symbol * Microsoft.Z3.Symbol [] -&gt; Microsoft.Z3.EnumSort<br />
member MkEnumSort : string * string [] -&gt; Microsoft.Z3.EnumSort<br />
member MkEq : Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.BoolExpr<br />
member MkExists : Microsoft.Z3.Expr [] * Microsoft.Z3.Expr * uint32 * Microsoft.Z3.Pattern [] * Microsoft.Z3.Expr [] * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Quantifier<br />
member MkExists : Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.Expr * uint32 * Microsoft.Z3.Pattern [] * Microsoft.Z3.Expr [] * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Quantifier<br />
member MkExtract : uint32 * uint32 * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkFalse : unit -&gt; Microsoft.Z3.BoolExpr<br />
member MkFiniteDomainSort : Microsoft.Z3.Symbol * uint64 -&gt; Microsoft.Z3.FiniteDomainSort<br />
member MkFiniteDomainSort : string * uint64 -&gt; Microsoft.Z3.FiniteDomainSort<br />
member MkFixedpoint : unit -&gt; Microsoft.Z3.Fixedpoint<br />
member MkForall : Microsoft.Z3.Expr [] * Microsoft.Z3.Expr * uint32 * Microsoft.Z3.Pattern [] * Microsoft.Z3.Expr [] * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Quantifier<br />
member MkForall : Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.Expr * uint32 * Microsoft.Z3.Pattern [] * Microsoft.Z3.Expr [] * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Quantifier<br />
member MkFreshConst : string * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkFreshConstDecl : string * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkFreshFuncDecl : string * Microsoft.Z3.Sort [] * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkFullSet : Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkFuncDecl : Microsoft.Z3.Symbol * Microsoft.Z3.Sort [] * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkFuncDecl : Microsoft.Z3.Symbol * Microsoft.Z3.Sort * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkFuncDecl : string * Microsoft.Z3.Sort [] * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkFuncDecl : string * Microsoft.Z3.Sort * Microsoft.Z3.Sort -&gt; Microsoft.Z3.FuncDecl<br />
member MkGe : Microsoft.Z3.ArithExpr * Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkGoal : bool * bool * bool -&gt; Microsoft.Z3.Goal<br />
member MkGt : Microsoft.Z3.ArithExpr * Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkITE : Microsoft.Z3.BoolExpr * Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkIff : Microsoft.Z3.BoolExpr * Microsoft.Z3.BoolExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkImplies : Microsoft.Z3.BoolExpr * Microsoft.Z3.BoolExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkInt : string -&gt; Microsoft.Z3.IntNum<br />
member MkInt : int -&gt; Microsoft.Z3.IntNum<br />
member MkInt : uint32 -&gt; Microsoft.Z3.IntNum<br />
member MkInt : int64 -&gt; Microsoft.Z3.IntNum<br />
member MkInt : uint64 -&gt; Microsoft.Z3.IntNum<br />
member MkInt2BV : uint32 * Microsoft.Z3.IntExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkInt2Real : Microsoft.Z3.IntExpr -&gt; Microsoft.Z3.RealExpr<br />
member MkIntConst : Microsoft.Z3.Symbol -&gt; Microsoft.Z3.IntExpr<br />
member MkIntConst : string -&gt; Microsoft.Z3.IntExpr<br />
member MkIntSort : unit -&gt; Microsoft.Z3.IntSort<br />
member MkIsInteger : Microsoft.Z3.RealExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkLe : Microsoft.Z3.ArithExpr * Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkListSort : Microsoft.Z3.Symbol * Microsoft.Z3.Sort -&gt; Microsoft.Z3.ListSort<br />
member MkListSort : string * Microsoft.Z3.Sort -&gt; Microsoft.Z3.ListSort<br />
member MkLt : Microsoft.Z3.ArithExpr * Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkMap : Microsoft.Z3.FuncDecl * Microsoft.Z3.ArrayExpr [] -&gt; Microsoft.Z3.ArrayExpr<br />
member MkMod : Microsoft.Z3.IntExpr * Microsoft.Z3.IntExpr -&gt; Microsoft.Z3.IntExpr<br />
member MkMul : Microsoft.Z3.ArithExpr [] -&gt; Microsoft.Z3.ArithExpr<br />
member MkNot : Microsoft.Z3.BoolExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkNumeral : string * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkNumeral : int * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkNumeral : uint32 * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkNumeral : int64 * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkNumeral : uint64 * Microsoft.Z3.Sort -&gt; Microsoft.Z3.Expr<br />
member MkOr : Microsoft.Z3.BoolExpr [] -&gt; Microsoft.Z3.BoolExpr<br />
member MkParams : unit -&gt; Microsoft.Z3.Params<br />
member MkPattern : Microsoft.Z3.Expr [] -&gt; Microsoft.Z3.Pattern<br />
member MkPower : Microsoft.Z3.ArithExpr * Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.ArithExpr<br />
member MkProbe : string -&gt; Microsoft.Z3.Probe<br />
member MkQuantifier : bool * Microsoft.Z3.Expr [] * Microsoft.Z3.Expr * uint32 * Microsoft.Z3.Pattern [] * Microsoft.Z3.Expr [] * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Quantifier<br />
member MkQuantifier : bool * Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.Expr * uint32 * Microsoft.Z3.Pattern [] * Microsoft.Z3.Expr [] * Microsoft.Z3.Symbol * Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Quantifier<br />
member MkReal : string -&gt; Microsoft.Z3.RatNum<br />
member MkReal : int -&gt; Microsoft.Z3.RatNum<br />
member MkReal : uint32 -&gt; Microsoft.Z3.RatNum<br />
member MkReal : int64 -&gt; Microsoft.Z3.RatNum<br />
member MkReal : uint64 -&gt; Microsoft.Z3.RatNum<br />
member MkReal : int * int -&gt; Microsoft.Z3.RatNum<br />
member MkReal2Int : Microsoft.Z3.RealExpr -&gt; Microsoft.Z3.IntExpr<br />
member MkRealConst : Microsoft.Z3.Symbol -&gt; Microsoft.Z3.RealExpr<br />
member MkRealConst : string -&gt; Microsoft.Z3.RealExpr<br />
member MkRealSort : unit -&gt; Microsoft.Z3.RealSort<br />
member MkRem : Microsoft.Z3.IntExpr * Microsoft.Z3.IntExpr -&gt; Microsoft.Z3.IntExpr<br />
member MkRepeat : uint32 * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkSelect : Microsoft.Z3.ArrayExpr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetAdd : Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetComplement : Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetDel : Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetDifference : Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetIntersection : Microsoft.Z3.Expr [] -&gt; Microsoft.Z3.Expr<br />
member MkSetMembership : Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetSort : Microsoft.Z3.Sort -&gt; Microsoft.Z3.SetSort<br />
member MkSetSubset : Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.Expr<br />
member MkSetUnion : Microsoft.Z3.Expr [] -&gt; Microsoft.Z3.Expr<br />
member MkSignExt : uint32 * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member MkSimpleSolver : unit -&gt; Microsoft.Z3.Solver<br />
member MkSolver : Microsoft.Z3.Symbol -&gt; Microsoft.Z3.Solver<br />
member MkSolver : string -&gt; Microsoft.Z3.Solver<br />
member MkSolver : Microsoft.Z3.Tactic -&gt; Microsoft.Z3.Solver<br />
member MkStore : Microsoft.Z3.ArrayExpr * Microsoft.Z3.Expr * Microsoft.Z3.Expr -&gt; Microsoft.Z3.ArrayExpr<br />
member MkSub : Microsoft.Z3.ArithExpr [] -&gt; Microsoft.Z3.ArithExpr<br />
member MkSymbol : int -&gt; Microsoft.Z3.IntSymbol<br />
member MkSymbol : string -&gt; Microsoft.Z3.StringSymbol<br />
member MkTactic : string -&gt; Microsoft.Z3.Tactic<br />
member MkTermArray : Microsoft.Z3.ArrayExpr -&gt; Microsoft.Z3.Expr<br />
member MkTrue : unit -&gt; Microsoft.Z3.BoolExpr<br />
member MkTupleSort : Microsoft.Z3.Symbol * Microsoft.Z3.Symbol [] * Microsoft.Z3.Sort [] -&gt; Microsoft.Z3.TupleSort<br />
member MkUnaryMinus : Microsoft.Z3.ArithExpr -&gt; Microsoft.Z3.ArithExpr<br />
member MkUninterpretedSort : Microsoft.Z3.Symbol -&gt; Microsoft.Z3.UninterpretedSort<br />
member MkUninterpretedSort : string -&gt; Microsoft.Z3.UninterpretedSort<br />
member MkXor : Microsoft.Z3.BoolExpr * Microsoft.Z3.BoolExpr -&gt; Microsoft.Z3.BoolExpr<br />
member MkZeroExt : uint32 * Microsoft.Z3.BitVecExpr -&gt; Microsoft.Z3.BitVecExpr<br />
member Not : Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member NumProbes : uint32<br />
member NumSMTLIBAssumptions : uint32<br />
member NumSMTLIBDecls : uint32<br />
member NumSMTLIBFormulas : uint32<br />
member NumSMTLIBSorts : uint32<br />
member NumTactics : uint32<br />
member Or : Microsoft.Z3.Probe * Microsoft.Z3.Probe -&gt; Microsoft.Z3.Probe<br />
member OrElse : Microsoft.Z3.Tactic * Microsoft.Z3.Tactic -&gt; Microsoft.Z3.Tactic<br />
member ParAndThen : Microsoft.Z3.Tactic * Microsoft.Z3.Tactic -&gt; Microsoft.Z3.Tactic<br />
member ParOr : Microsoft.Z3.Tactic [] -&gt; Microsoft.Z3.Tactic<br />
member ParseSMTLIB2File : string * Microsoft.Z3.Symbol [] * Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.FuncDecl [] -&gt; Microsoft.Z3.BoolExpr<br />
member ParseSMTLIB2String : string * Microsoft.Z3.Symbol [] * Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.FuncDecl [] -&gt; Microsoft.Z3.BoolExpr<br />
member ParseSMTLIBFile : string * Microsoft.Z3.Symbol [] * Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.FuncDecl [] -&gt; unit<br />
member ParseSMTLIBString : string * Microsoft.Z3.Symbol [] * Microsoft.Z3.Sort [] * Microsoft.Z3.Symbol [] * Microsoft.Z3.FuncDecl [] -&gt; unit<br />
member PrintMode : -&gt; Microsoft.Z3.Z3_ast_print_mode with deb set<br />
member ProbeDescription : string -&gt; string<br />
member ProbeNames : string []<br />
member RealSort : Microsoft.Z3.RealSort<br />
member Repeat : Microsoft.Z3.Tactic * uint32 -&gt; Microsoft.Z3.Tactic<br />
member SMTLIBAssumptions : Microsoft.Z3.BoolExpr []<br />
member SMTLIBDecls : Microsoft.Z3.FuncDecl []<br />
member SMTLIBFormulas : Microsoft.Z3.BoolExpr []<br />
member SMTLIBSorts : Microsoft.Z3.Sort []<br />
member SimplifyHelp : unit -&gt; string<br />
member SimplifyParameterDescriptions : Microsoft.Z3.ParamDescrs<br />
member Skip : unit -&gt; Microsoft.Z3.Tactic<br />
member TacticDescription : string -&gt; string<br />
member TacticNames : string []<br />
member Then : Microsoft.Z3.Tactic * Microsoft.Z3.Tactic * Microsoft.Z3.Tactic [] -&gt; Microsoft.Z3.Tactic<br />
member TryFor : Microsoft.Z3.Tactic * uint32 -&gt; Microsoft.Z3.Tactic<br />
member UnwrapAST : Microsoft.Z3.AST -&gt; System.IntPtr<br />
member UpdateParamValue : string * string -&gt; unit<br />
member UsingParams : Microsoft.Z3.Tactic * Microsoft.Z3.Params -&gt; Microsoft.Z3.Tactic<br />
member When : Microsoft.Z3.Probe * Microsoft.Z3.Tactic -&gt; Microsoft.Z3.Tactic<br />
member With : Microsoft.Z3.Tactic * Microsoft.Z3.Params -&gt; Microsoft.Z3.Tactic<br />
member WrapAST : System.IntPtr -&gt; Microsoft.Z3.AST<br />
static member ToggleWarningMessages : bool -&gt; unit<br />
end</p>
<p>Full name: Microsoft.Z3.Context</p>
<p>type: Context<br />
implements: IDisposable</p>
</div>
<div class="tip" id="fstips5">
<p>val f : BoolExpr</p>
<p>type: BoolExpr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: Expr<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips6">
<p>val s : Solver</p>
<p>type: Solver<br />
implements: IDisposable<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips7">
<p>Multiple overloadsContext.MkSolver(t: Tactic) : Solver</p>
<p>Context.MkSolver(logic: string) : Solver</p>
<p>Context.MkSolver(logic: Symbol) : Solver</p>
</div>
<div class="tip" id="fstips8">Solver.Assert(constraints: BoolExpr []) : unit</div>
<div class="tip" id="fstips9">Context.MkNot(a: BoolExpr) : BoolExpr</div>
<div class="tip" id="fstips10">Solver.Check(assumptions: Expr []) : Status</div>
<div class="tip" id="fstips11">
<p>type Status =<br />
| UNSATISFIABLE = -1<br />
| UNKNOWN = 0<br />
| SATISFIABLE = 1</p>
<p>Full name: Microsoft.Z3.Status</p>
<p>type: Status<br />
inherits: Enum<br />
inherits: ValueType</p>
</div>
<div class="tip" id="fstips12">field Status.UNKNOWN = 0</div>
<div class="tip" id="fstips13">
<p>val printfn : Printf.TextWriterFormat&lt;'T&gt; -&gt; 'T</p>
<p>Full name: Microsoft.FSharp.Core.ExtraTopLevelOperators.printfn</p>
</div>
<div class="tip" id="fstips14">property Solver.ReasonUnknown: string</div>
<div class="tip" id="fstips15">field Status.SATISFIABLE = 1</div>
<div class="tip" id="fstips16">property Solver.Proof: Expr</div>
<div class="tip" id="fstips17">
<p>val proveSample : unit -&gt; unit</p>
<p>Full name: GoZ3.proveSample</p>
<p><em>Prove that x = y implies g(x) = g(y)</em></p>
</div>
<div class="tip" id="fstips18">let prms = System.Collections.Generic.Dictionary (dict [ "proof", "true" ])<br />
let ctx = new Context(prms)</div>
<div class="tip" id="fstips19">
<p>val U : UninterpretedSort</p>
<p>type: UninterpretedSort<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: Sort<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips20">
<p>Multiple overloadsContext.MkUninterpretedSort(str: string) : UninterpretedSort</p>
<p>Context.MkUninterpretedSort(s: Symbol) : UninterpretedSort</p>
</div>
<div class="tip" id="fstips21">
<p>Multiple overloadsContext.MkSymbol(name: string) : StringSymbol</p>
<p>Context.MkSymbol(i: int) : IntSymbol</p>
</div>
<div class="tip" id="fstips22">
<p>val g : FuncDecl</p>
<p>type: FuncDecl<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips23">
<p>Multiple overloadsContext.MkFuncDecl(name: string, domain: Sort, range: Sort) : FuncDecl</p>
<p>Context.MkFuncDecl(name: string, domain: Sort [], range: Sort) : FuncDecl</p>
<p>Context.MkFuncDecl(name: Symbol, domain: Sort, range: Sort) : FuncDecl</p>
<p>Context.MkFuncDecl(name: Symbol, domain: Sort [], range: Sort) : FuncDecl</p>
</div>
<div class="tip" id="fstips24">
<p>val x : Expr</p>
<p>type: Expr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips25">
<p>Multiple overloadsContext.MkConst(f: FuncDecl) : Expr</p>
<p>Context.MkConst(name: string, range: Sort) : Expr</p>
<p>Context.MkConst(name: Symbol, range: Sort) : Expr</p>
</div>
<div class="tip" id="fstips26">
<p>val y : Expr</p>
<p>type: Expr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips27">
<p>val gx : Expr</p>
<p>type: Expr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips28">
<p>val gy : Expr</p>
<p>type: Expr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips29">
<p>val eq : BoolExpr</p>
<p>type: BoolExpr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: Expr<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips30">Context.MkEq(x: Expr, y: Expr) : BoolExpr</div>
<div class="tip" id="fstips31">Context.MkImplies(t1: BoolExpr, t2: BoolExpr) : BoolExpr</div>
<div class="tip" id="fstips32">Multiple itemstype Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'T16,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 * 'T14 * 'T15 * 'T16 -&gt; 'TResultFull name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'T16,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate--------------------type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 * 'T14 * 'T15 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 * 'T14 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'TResult&gt; =<br />
delegate of 'T1 * 'T2 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T,'TResult&gt; =<br />
delegate of 'T -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_&gt;</p>
<p>type: Func&lt;'T,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'TResult&gt; =<br />
delegate of unit -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_&gt;</p>
<p>type: Func&lt;'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'T14 -&gt; 'T15 -&gt; 'T16 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'T14 -&gt; 'T15 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'T14 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func(unit -&gt; 'TResult)</p>
</div>
<div class="tip" id="fstips33">val expr : Expr</div>
<div class="tip" id="fstips34">match expr with<br />
| Lambdas(_,(Call(_,minfo1,_))) -&gt; function<br />
| Call(obj, minfo2, args) when minfo1.MetadataToken = minfo2.MetadataToken -&gt;<br />
Some args<br />
| _ -&gt; None<br />
| _ -&gt; failwith "invalid template parameter"</div>
<div class="tip" id="fstips35">
<p>val z3 : Expr -&gt; ArithExpr</p>
<p>Full name: GoZ3.z3</p>
<p><em>From quotations to Z3 objects</em></p>
</div>
<div class="tip" id="fstips36">let prms = System.Collections.Generic.Dictionary (dict [ "proof", "true" ])<br />
let ctx = new Context()</div>
<div class="tip" id="fstips37">val unquote : (Expr -&gt; ArithExpr)</div>
<div class="tip" id="fstips38">
<p>Multiple itemsactive recognizer Func: Expr -&gt; Expr -&gt; Expr list optionFull name: GoZ3.( |Func|_| )</p>
<p><em>Simplified SpecificCall</em></p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'T16,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 * 'T14 * 'T15 * 'T16 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'T16,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 * 'T14 * 'T15 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'T15,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 * 'T14 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'T14,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 * 'T13 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'T13,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 * 'T12 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'T12,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 * 'T11 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'T11,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 * 'T10 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'T10,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 * 'T9 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'T9,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 * 'T8 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'T8,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 * 'T7 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'T7,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 * 'T6 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'T6,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'T5,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 * 'T5 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'T5,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'T4,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 * 'T4 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'T4,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'T3,'TResult&gt; =<br />
delegate of 'T1 * 'T2 * 'T3 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'T3,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T1,'T2,'TResult&gt; =<br />
delegate of 'T1 * 'T2 -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_,_&gt;</p>
<p>type: Func&lt;'T1,'T2,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'T,'TResult&gt; =<br />
delegate of 'T -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_,_&gt;</p>
<p>type: Func&lt;'T,'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>type Func&lt;'TResult&gt; =<br />
delegate of unit -&gt; 'TResult</p>
<p>Full name: System.Func&lt;_&gt;</p>
<p>type: Func&lt;'TResult&gt;<br />
implements: ICloneable<br />
implements: Runtime.Serialization.ISerializable<br />
inherits: MulticastDelegate<br />
inherits: Delegate</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'T14 -&gt; 'T15 -&gt; 'T16 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'T14 -&gt; 'T15 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'T14 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'T13 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'T12 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'T11 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'T10 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'T9 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'T8 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'T7 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'T6 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'T5 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'T4 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'T3 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T1 -&gt; 'T2 -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func('T -&gt; 'TResult)</p>
<p>--------------------</p>
<p>Func(unit -&gt; 'TResult)</p>
</div>
<div class="tip" id="fstips39">val x : Expr</div>
<div class="tip" id="fstips40">val y : Expr</div>
<div class="tip" id="fstips41">Context.MkAdd(t: ArithExpr []) : ArithExpr</div>
<div class="tip" id="fstips42">Context.MkSub(t: ArithExpr []) : ArithExpr</div>
<div class="tip" id="fstips43">
<p>active recognizer Lambdas: Expr -&gt; (Var list list * Expr) option</p>
<p>Full name: Microsoft.FSharp.Quotations.DerivedPatterns.( |Lambdas|_| )</p>
</div>
<div class="tip" id="fstips44">val e : Expr</div>
<div class="tip" id="fstips45">
<p>Multiple itemsactive recognizer Var: Expr -&gt; Var optionFull name: Microsoft.FSharp.Quotations.Patterns.( |Var|_| )</p>
<p>--------------------</p>
<p>type Var =<br />
class<br />
interface IComparable<br />
new : name:string * typ:Type * ?isMutable:bool -&gt; Var<br />
member IsMutable : bool<br />
member Name : string<br />
member Type : Type<br />
static member Global : name:string * typ:Type -&gt; Var<br />
end</p>
<p>Full name: Microsoft.FSharp.Quotations.Var</p>
<p>type: Var<br />
implements: IComparable</p>
</div>
<div class="tip" id="fstips46">
<p>val var : Var</p>
<p>type: Var<br />
implements: IComparable</p>
</div>
<div class="tip" id="fstips47">
<p>Multiple overloadsContext.MkIntConst(name: string) : IntExpr</p>
<p>Context.MkIntConst(name: Symbol) : IntExpr</p>
</div>
<div class="tip" id="fstips48">property Var.Name: string</div>
<div class="tip" id="fstips49">
<p>type ArithExpr =<br />
class<br />
inherit Microsoft.Z3.Expr<br />
end</p>
<p>Full name: Microsoft.Z3.ArithExpr</p>
<p>type: ArithExpr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: Expr<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips50">
<p>val failwith : string -&gt; 'T</p>
<p>Full name: Microsoft.FSharp.Core.Operators.failwith</p>
</div>
<div class="tip" id="fstips51">
<p>val sprintf : Printf.StringFormat&lt;'T&gt; -&gt; 'T</p>
<p>Full name: Microsoft.FSharp.Core.ExtraTopLevelOperators.sprintf</p>
</div>
<div class="tip" id="fstips52">
<p>val simplifier : unit -&gt; unit</p>
<p>Full name: GoZ3.simplifier</p>
<p><em>Simplify expression "x + (y - (x + z))"</em></p>
</div>
<div class="tip" id="fstips53">
<p>val t1 : ArithExpr</p>
<p>type: ArithExpr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: Expr<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips54">
<p>val x : int</p>
<p>type: int<br />
implements: IComparable<br />
implements: IFormattable<br />
implements: IConvertible<br />
implements: IComparable&lt;int&gt;<br />
implements: IEquatable&lt;int&gt;<br />
inherits: ValueType</p>
</div>
<div class="tip" id="fstips55">
<p>val y : int</p>
<p>type: int<br />
implements: IComparable<br />
implements: IFormattable<br />
implements: IConvertible<br />
implements: IComparable&lt;int&gt;<br />
implements: IEquatable&lt;int&gt;<br />
inherits: ValueType</p>
</div>
<div class="tip" id="fstips56">
<p>val z : int</p>
<p>type: int<br />
implements: IComparable<br />
implements: IFormattable<br />
implements: IConvertible<br />
implements: IComparable&lt;int&gt;<br />
implements: IEquatable&lt;int&gt;<br />
inherits: ValueType</p>
</div>
<div class="tip" id="fstips57">
<p>val t2 : Expr</p>
<p>type: Expr<br />
implements: IDisposable<br />
implements: IComparable<br />
inherits: AST<br />
inherits: Z3Object</p>
</div>
<div class="tip" id="fstips58">Expr.Simplify(p: Params) : Expr</div>