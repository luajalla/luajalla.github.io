---
layout: post
title: 'Excel-DNA: Three Stories'
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 1702
wordpress_url: http://luajalla.azurewebsites.net/?p=1702
date: '2013-08-05 01:00:07 +0200'
date_gmt: '2013-08-05 01:00:07 +0200'
image:
  feature: header3.jpg
  credit: Geneva Lake
tags:
- fsharp
- Excel
- R
comments:
- id: 202
  author: 'F# Weekly #31 2013 | Sergey Tihon&#039;s Blog'
  author_email: ''
  author_url: http://sergeytihon.wordpress.com/2013/08/05/f-weekly-31-2013/
  date: '2013-08-05 05:18:28 +0200'
  date_gmt: '2013-08-05 05:18:28 +0200'
  content: '[...] Natallie Baikevich ‏posted &#8220;Excel-DNA: Three Stories&#8220;.
    [...]'
- id: 212
  author: Lyndsy Simon
  author_email: lyndsy@lyndsysimon.com
  author_url: http://lyndsysimon.com
  date: '2013-08-05 15:01:23 +0200'
  date_gmt: '2013-08-05 15:01:23 +0200'
  content: "<blockquote>The code is of unknown origin and protected, so no way to
    check it and I just gave up.</blockquote>\r\n\r\nYikes!\r\n\r\nPlease don't run
    code from unknown sources. That code could have busily been attempting to gain
    access to every system on your network for 24 hours, happily installing rootkits
    along the way."
- id: 222
  author: luajalla
  author_email: lu-a-jalla@ya.ru
  author_url: ''
  date: '2013-08-05 19:40:27 +0200'
  date_gmt: '2013-08-05 19:40:27 +0200'
  content: Unknown here doesn't mean randomly downloaded/untrusted, just that I don't
    know who wrote it ) Most probably there's a bug inside.
- id: 232
  author: F# and R in Excel | Excel-DNA
  author_email: ''
  author_url: http://excel-dna.net/2013/08/05/f-and-r-in-excel/
  date: '2013-08-05 21:39:19 +0200'
  date_gmt: '2013-08-05 21:39:19 +0200'
  content: '[...] F# and R in Excel [...]'
- id: 242
  author: niggler
  author_email: nirk.niggler@gmail.com
  author_url: ''
  date: '2013-10-10 03:42:16 +0200'
  date_gmt:   '2013-10-10 03:42:16 +0200'
  content: 'You can break VBA protection fairly easily with a hex editor: http://blog.nig.gl/post/63428658404/excel-vba-password-protection-is-useless'
- id: 252
  author: luajalla
  author_email: lu-a-jalla@ya.ru
  author_url: ''
  date: '2013-10-10 15:28:28 +0200'
  date_gmt: '2013-10-10 15:28:28 +0200'
  content: Thanks - was too lazy to look for that :) anyway, fixing VBA is the last
    thing you want to do when there's a simpler solution.
- id: 292
  author: Gerald W
  author_email: gerald.wluka@statfactory.co.uk
  author_url: http://www.fcell.cio
  date: '2014-02-17 18:35:48 +0100'
  date_gmt: '2014-02-17 18:35:48 +0100'
  content: If you want a powerful, robust, commercial solution (integrate .NET into
    Excel spreadsheets, with F#) take a look at Fcell.io
---
### Intro: Simulation

A couple of days ago I found a spreadsheet, potentially quite an interesting one. In theory, it should run a simple simulation (50000 paths by default) written in VBA - I'd say it's several minutes of work in the worst case. However, it took slightly more. One working day, to be precise. I still have no idea what it tried to do, why it ate 97% of CPU and even what exactly it computed, because all that ended with a weird error and crashed Excel. The code is of unknown origin and protected, so no way to check it and I just gave up.  

*[Originally posted [here](http://luajalla.azurewebsites.net/excel-dna-three-stories/).]*  

Now think about an average Excel user. He/she doesn't care about how exactly it works, in which language written or how difficult it was for you to implement. What is important then?  

- *functionality*: everything what comes to mind can become a function;  

- *simplicity*: nobody wants to write VBA (and usually anything else too), but it's always nice to have some useful UDFs at hand;  

- *reliability*: you have a well-tested library already, so why not to call it instead of rewriting in a poor language?  

- *performance*: why wait for a day when there're highly optimized libraries and distributed computing is already invented?  

For those who hasn't tried it yet - check <a title="Excel-DNA home" href="http://exceldna.codeplex.com/" target="_blank">Excel-DNA</a>. It's an open-source project, which allows to integrate .NET into Excel. And yes, actually not only .NET ;) Documentation, samples, links to related posts can be found on the project page.  

![Spreadsheet]({{ site.url }}/images/dilbert_spreadsheet.gif)
{: .image-center}

<p>&nbsp;</p>


### Keep Simple Things Simple  

Let's take log-linear interpolation as an example. Simple? Sure. We want to keep calculations clear in case if someone'd like to modify it - took 9 rows for me: functions like ````OFFSET```` and ````MATCH```` are not very friendly. And you still need to remember about sorting, extrapolation, duplicate values. What if there're thousands of points?  

Instead we call a UDF, which does all the work, it behaves in a specified way and can be reused in the future. I won't describe how to create an addin - for more information check the project <a title="Excel-DNA home" href="http://exceldna.codeplex.com/" target="_blank">homepage</a>. Briefly, in addition to a standard F# project we need a reference to *ExcelDna.Integration.dll*, *interpolation.dna* file with a reference to our library and a copy of *ExcelDna.xll* (x64 version is also available), renamed to *interpolation.xll*.  

The future UDF has ````ExcelFunction```` attribute [^1]. For consistency with Excel function names it's called ````LOGLINEAR````. You can also add a description, category and so on. All parameters and output here are float arrays.  

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
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">open</span> <span class="i">ExcelDna</span><span class="o">.</span><span class="i">Integration</span>

[&lt;<span class="i">AutoOpen</span>&gt;]
<span class="k">module</span> <span class="i">Interpolation</span> <span class="o">=</span>

    <span class="k">let</span> <span class="k">private</span> <span class="i">interpolate</span> <span class="i">curve</span> (<span class="i">point</span><span class="o">:</span> <span class="i">float</span>) <span class="o">=</span>
        <span class="k">let</span> <span class="i">len</span> <span class="o">=</span> <span class="i">Array</span><span class="o">.</span><span class="i">length</span> <span class="i">curve</span>
        <span class="k">let</span> <span class="i">u</span> <span class="o">=</span>
            <span class="k">if</span> <span class="i">point</span> <span class="o">&lt;</span> <span class="i">fst</span> <span class="i">curve</span><span class="o">.</span>[<span class="n">0</span>] <span class="k">then</span> <span class="n">1</span>
            <span class="k">elif</span> <span class="i">point</span> <span class="o">&gt;</span> <span class="i">fst</span> <span class="i">curve</span><span class="o">.</span>[<span class="i">len</span><span class="o">-</span><span class="n">1</span>] <span class="k">then</span> <span class="i">len</span><span class="o">-</span><span class="n">1</span>
            <span class="k">else</span> <span class="i">Array</span><span class="o">.</span><span class="i">findIndex</span> (<span class="k">fun</span> (<span class="i">v</span>, _) <span class="k">-&gt;</span> <span class="i">point</span> <span class="o">&lt;</span> <span class="i">v</span>) <span class="i">curve</span>
        <span class="k">let</span> (<span class="i">xu</span>, <span class="i">yu</span>), (<span class="i">xd</span>, <span class="i">yd</span>) <span class="o">=</span> <span class="i">curve</span><span class="o">.</span>[<span class="i">u</span>], <span class="i">curve</span><span class="o">.</span>[<span class="i">u</span><span class="o">-</span><span class="n">1</span>]
        <span class="k">let</span> <span class="i">lnp</span> <span class="o">=</span> <span class="i">log</span> <span class="i">yd</span> <span class="o">+</span> (<span class="i">log</span> <span class="i">yu</span> <span class="o">-</span> <span class="i">log</span> <span class="i">yd</span>) <span class="o">/</span> (<span class="i">xu</span> <span class="o">-</span> <span class="i">xd</span>) <span class="o">*</span> (<span class="i">point</span> <span class="o">-</span> <span class="i">xd</span>)
        <span class="i">exp</span> <span class="i">lnp</span>

    [&lt;<span class="i">ExcelFunction</span>(<span class="i">Name</span><span class="o">=</span><span class="s">"</span><span class="s">LOGLINEAR</span><span class="s">"</span>, <span class="i">Description</span><span class="o">=</span><span class="s">"</span><span class="s">Log</span><span class="s">-</span><span class="s">Linear</span> <span class="s">Interpolation</span><span class="s">"</span>,
Category</span><span class="o">=</span><span class="s">"</span><span class="s">Custom</span><span class="s">"</span>, <span class="i">IsThreadSafe</span> <span class="o">=</span> <span class="k">true</span>)&gt;]
    <span class="k">let</span> <span class="i">loglinear</span> (<span class="i">xs</span><span class="o">:</span> _[]) (<span class="i">ys</span><span class="o">:</span> _[]) <span class="i">points</span> <span class="o">=</span>
        <span class="k">let</span> <span class="i">curve</span> <span class="o">=</span>
            <span class="i">Seq</span><span class="o">.</span><span class="i">zip</span> <span class="i">xs</span> <span class="i">ys</span>
            <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">distinctBy</span> <span class="i">fst</span>
            <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">sort</span>
            <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">toArray</span>

        <span class="k">if</span> <span class="i">curve</span><span class="o">.</span><span class="i">Length</span> <span class="o">&lt;</span> <span class="n">2</span> <span class="k">then</span> <span class="i">failwith</span> <span class="s">"</span><span class="s">at</span> <span class="s">least</span> <span class="s">2</span> <span class="s">points</span> <span class="s">are</span> <span class="s">required</span><span class="s">"</span>
        <span class="i">Array</span><span class="o">.</span><span class="i">map</span> (<span class="i">interpolate</span> <span class="i">curve</span>) <span class="i">points</span></pre></td></tr></table>  
		
After loading the add-in in Excel (double-click on *interpolation.xll* in output folder), you can just type ````LOGLINEAR```` and see the results![^2].  

![Excel-Interpolation]({{ site.url }}/images/interpolation.png)
{: .image-center}

<p>&nbsp;</p>  


### Make Complex Things Simple  

Well, it's cool, but I still can do this interpolation by hands - and it'll work, you don't need to be super-smart to do a couple of subtractions and multiplications without mistakes. But things are getting more interesting when a bunch of standard functions is not enough.  

How about machine learning with Excel? Of course, it can be handy only for experiments with adequately small amounts of data. But for sure, not something I'd want to write in VBA.  

A lot of my fellow traders use random forests for feature selection (it is actually what I like about RF most) before feeding the data into actual models, neural nets or anything else. So let's take a look at *rtp* addin, which can help to find important features and get rid of useless (and potentially harmful?) ones. Important point: this example is for demo purposes only! I took some Yahoo stock prices - it's not that easy to get good data for free, so can't call it realistic; the same works for features[^3]. If you want a bit more for free, there're also two old kaggle competitions: <a title="Benchmark Bond Trade Price Challenge" href="https://www.kaggle.com/c/benchmark-bond-trade-price-challenge" target="_blank">Benchmark Bond Trade Price Challenge</a>  and <a title="Algorithmic Trading Challenge" href="http://www.kaggle.com/c/AlgorithmicTradingChallenge" target="_blank">Algorithmic Trading Challenge</a>  (the winners <a title="Winning Algorithmic Trading Challenge" href="http://sugiyama-www.cs.titech.ac.jp/~sugi/2013/Kaggle.pdf" target="_blank">used RF</a>).  

Anyway, we are lucky because all data is numeric. Even more than that - we have <a title="R Type Provider" href="https://github.com/BlueMountainCapital/FSharpRProvider" target="_blank">F# R Type Provider</a> and can use R's randomForest package!  

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
</pre></td><td class="snippet"><pre class="fssnip">
<span class="k">open</span> <span class="i">RProvider</span>
<span class="k">open</span> <span class="i">RProvider</span><span class="o">.</span><span class="i">``base``</span>
<span class="k">open</span> <span class="i">RProvider</span><span class="o">.</span><span class="i">randomForest</span>

<span class="k">open</span> <span class="i">ExcelDna</span><span class="o">.</span><span class="i">Integration</span>

[&lt;<span class="i">ExcelFunction</span>(<span class="i">Name</span> <span class="o">=</span> <span class="s">"</span><span class="s">IMPORTANCE</span><span class="s">"</span>, 
    <span class="i">Description</span> <span class="o">=</span> <span class="s">"</span><span class="s">Variable</span> <span class="s">importance</span> <span class="s">measures</span> <span class="s">as</span> <span class="s">produced</span> <span class="s">by</span> <span class="s">R's</span> <span class="s">randomForest</span> <span class="s">"</span>)&gt;]
<span class="k">let</span> <span class="i">importance</span> (<span class="i">names</span><span class="o">:</span> <span class="i">obj</span>[]) (<span class="i">values</span><span class="o">:</span> <span class="i">float</span>[,]) (<span class="i">ys</span><span class="o">:</span> <span class="i">float</span>[]) <span class="o">=</span>
    <span class="k">let</span> <span class="i">cols</span> <span class="o">=</span> <span class="i">min</span> <span class="i">names</span><span class="o">.</span><span class="i">Length</span> (<span class="i">values</span><span class="o">.</span><span class="i">GetLength</span> <span class="n">1</span>)
    <span class="k">let</span> <span class="i">rows</span> <span class="o">=</span> <span class="i">min</span> <span class="i">ys</span><span class="o">.</span><span class="i">Length</span> (<span class="i">values</span><span class="o">.</span><span class="i">GetLength</span> <span class="n">0</span>)

    <span class="k">let</span> <span class="i">xs</span> <span class="o">=</span> 
        <span class="i">names</span>
        <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">take</span> <span class="i">cols</span>
        <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">mapi</span> (<span class="k">fun</span> <span class="i">j</span> <span class="i">name</span> <span class="k">-&gt;</span>
            <span class="i">string</span> <span class="i">name</span>, <span class="i">Array</span><span class="o">.</span><span class="i">init</span> <span class="i">rows</span> (<span class="k">fun</span> <span class="i">i</span> <span class="k">-&gt;</span> <span class="i">values</span><span class="o">.</span>[<span class="i">i</span>, <span class="i">j</span>]))
        <span class="o">|&gt;</span> <span class="i">namedParams</span>
        <span class="o">|&gt;</span> <span class="i">R</span><span class="o">.</span><span class="i">data_frame</span>            

    <span class="k">let</span> <span class="i">rf</span> <span class="o">=</span> <span class="i">R</span><span class="o">.</span><span class="i">randomForest</span>(<span class="i">xs</span>, <span class="i">ys</span>)
    (<span class="i">R</span><span class="o">.</span><span class="i">importance</span> <span class="i">rf</span>)<span class="o">.</span><span class="i">Value</span></pre></td></tr></table>  
	
Look at the R-Importance tab: we call the function ````{=IMPORTANCE(Names,Data,Result)}```` and get a nice set of values - the greater the value, the greener and more important it is. For example, P5 and P6 (close prices for day-5 and day-6 respectively) seem to be not very useful. 

![Spreadsheet]({{ site.url }}/images/excel-importance.png)
{: .image-center}  


Adding features is pretty straightforward too. Say, we want to calculate Simple Moving Average with different offsets:  

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
</pre></td><td class="snippet"><pre class="fssnip">
[&lt;<span class="i">ExcelFunction</span>(<span class="i">Name</span> <span class="o">=</span> <span class="s">"</span><span class="s">SMA</span><span class="s">"</span>, <span class="i">Description</span><span class="o">=</span><span class="s">"</span><span class="s">Simple</span> <span class="s">Moving</span> <span class="s">Average</span><span class="s">"</span>)&gt;]
<span class="k">let</span> <span class="i">sma</span> (<span class="i">values</span><span class="o">:</span> <span class="i">float</span>[]) (<span class="i">nobj</span><span class="o">:</span> <span class="i">float</span>) <span class="o">=</span>
    <span class="k">let</span> <span class="i">n</span> <span class="o">=</span> <span class="i">int</span> <span class="i">nobj</span>
    <span class="k">let</span> <span class="i">len</span> <span class="o">=</span> <span class="i">values</span><span class="o">.</span><span class="i">Length</span>
    <span class="k">if</span> <span class="i">len</span> <span class="o">&lt;</span> <span class="i">n</span> <span class="o">||</span> <span class="i">n</span> <span class="o">&lt;</span> <span class="n">2</span> <span class="k">then</span> <span class="i">box</span> <span class="i">ExcelError</span><span class="o">.</span><span class="i">ExcelErrorNA</span>
    <span class="k">else</span> 
        <span class="k">let</span> <span class="i">res</span> <span class="o">=</span> <span class="i">Array2D</span><span class="o">.</span><span class="i">zeroCreate</span> <span class="i">len</span> <span class="n">1</span>

        <span class="i">Seq</span><span class="o">.</span><span class="i">windowed</span> <span class="i">n</span> <span class="i">values</span>
        <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">map</span> <span class="i">Seq</span><span class="o">.</span><span class="i">average</span>
        <span class="o">|&gt;</span> <span class="i">Seq</span><span class="o">.</span><span class="i">iteri</span> (<span class="k">fun</span> <span class="i">i</span> <span class="i">v</span> <span class="k">-&gt;</span> <span class="i">res</span><span class="o">.</span>[<span class="i">i</span><span class="o">+</span><span class="i">n</span><span class="o">-</span><span class="n">1</span>, <span class="n">0</span>] <span class="o">&lt;-</span> <span class="i">box</span> <span class="i">v</span>)
        <span class="i">resize</span> <span class="i">res</span></pre></td></tr></table>  
		

The interesting thing here is ````resize```` function: it allows you not to select the whole output range when calling a function, but just type ````=SMA(Values,5)```` in the top cell and result array is automatically resized. The code of these examples is available on <a title="Excel-DNA Samples" href="https://github.com/luajalla/everything-fun/tree/master/exceldna-sample" target="_blank">github</a>.  

<iframe src="https://docs.google.com/spreadsheet/pub?key=0AsEtPrcHNCbXdGFvRjRhYkwxYkhVWmxyZWxxV3dJemc&amp;output=html&amp;widget=true" height="300" width="700" frameborder="0"></iframe>  

Excel-DNA makes it possible to bring all .NET power to the spreadsheets. Just try it ^_^  

<p>&nbsp;</p>
<p>&nbsp;</p>

[^1]: see *interpolation* project <a title="Interpolation" href="https://github.com/luajalla/everything-fun/tree/master/exceldna-sample/interpolation" target="_blank">here</a>.
[^2]: as a reminder, array formulae are entered with Ctrl+Shift+Enter.
[^3]: and also my experience has nothing to do with trading.
