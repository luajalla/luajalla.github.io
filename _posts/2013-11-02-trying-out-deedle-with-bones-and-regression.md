---
layout: post
title: Trying out Deedle with Bones and Regression
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 2202
wordpress_url: http://luajalla.azurewebsites.net/?p=2202
date: '2013-11-02 22:07:15 +0100'
date_gmt: '2013-11-02 22:07:15 +0100'
image:
  feature: header5.jpg
  credit: Basel, Rhein
tags:
- fsharp
- data
comments: []
---
I usually don't need to run a regression anywhere, but it's kind of chasing me recently, starting with the Asset Pricing class and several variations of returns regressions (signed up to look at the familiar things from a different point of view... well, I definitely succeeded: have you ever thought about drawing the returns, prices and discount factors in space, all at once? [^1]. But I 'cheated' and completed the assignments with R.  

*[Originally posted [here](http://luajalla.azurewebsites.net/trying-out-deedle-with-bones-and-regression/).]*  

Though that was only the beginning - my cousin, MD student, was measuring the deflection of bones and other samples with different loads. And this time I decided to try out <a href="http://bluemountaincapital.github.io/Deedle/" title="Deedle: Exploratory data library for .NET" target="_blank">Deedle</a> and help her to explore the data.  

*Hint for Mono users: if XS doesn't load the main Deedle project, you can manually update the fsproj file (delete the reference to FSharp.Core), reload it, add references to Math.NET and FSharp.Data libs - it'll work nicely.*  

Let's start with loading experimental data. No more string splits and manual parsing!  

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
</pre></td>
<td class="snippets"><pre class="fssnip">
<span class="prep">#r</span> <span class="s">&quot;</span><span class="s">FSharp</span><span class="s">.</span><span class="s">Data</span><span class="s">.</span><span class="s">dll</span><span class="s">&quot;</span>
<span class="prep">#load</span> <span class="s">&quot;</span><span class="s">Deedle</span><span class="s">.</span><span class="s">fsx</span><span class="s">&quot;</span>

<span class="k">open</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">System</span>
<span class="k">open</span> <span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">Deedle</span>
<span class="c">// load data from a csv file</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">ds</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst5', 5)" onmouseover="showTip(event, 'fst5', 5)" class="i">ReadCsv</span>(<span class="k">__SOURCE_DIRECTORY__</span> <span class="o">+</span> <span class="s">&quot;</span><span class="s">/</span><span class="s">deflection</span><span class="s">.</span><span class="s">csv</span><span class="s">&quot;</span>)
<span class="fsi">val ds : Frame&lt;int,string&gt; = </span>
<span class="fsi">        p         sample type length&lt;mm&gt; width&lt;mm&gt; height&lt;mm&gt; deflection&lt;mm&gt; </span>
<span class="fsi">  0  -&gt; 0.98      bone        47         7.3       4.4        0.06 </span>
<span class="fsi">  1  -&gt; 1.96      bone        47         7.3       4.4        0.12 </span>
</pre></td></tr></table>

Then we checked out some properties of different samples groups - say, the average sample deformation. For simplicity we'll use only the bones group, load ("p") and deflection columns.  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span class="c">// choose several columns and group the data by sample type</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst6', 6)" onmouseover="showTip(event, 'fst6', 6)" class="i">bySample</span> <span class="o">=</span> 
    <span onmouseout="hideTip(event, 'fst3', 7)" onmouseover="showTip(event, 'fst3', 7)" class="i">ds</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst7', 8)" onmouseover="showTip(event, 'fst7', 8)" class="i">Columns</span><span class="o">.</span>[[<span class="s">&quot;</span><span class="s">sample</span><span class="s"> </span><span class="s">type</span><span class="s">&quot;</span>; <span class="s">&quot;</span><span class="s">p</span><span class="s">&quot;</span>; <span class="s">&quot;</span><span class="s">deflection</span><span class="s">&lt;</span><span class="s">mm</span><span class="s">&gt;</span><span class="s">&quot;</span>]] 
    <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst4', 9)" onmouseover="showTip(event, 'fst4', 9)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst8', 10)" onmouseover="showTip(event, 'fst8', 10)" class="i">groupRowsByString</span> <span class="s">&quot;</span><span class="s">sample</span><span class="s"> </span><span class="s">type</span><span class="s">&quot;</span>
<span class="fsi">val bySample : Frame&lt;(string * int),string&gt; =</span>
<span class="fsi">                  sample type p         deflection&lt;mm&gt; </span>
<span class="fsi">  bone      0  -&gt; bone        0.98      0.06           </span>
<span class="fsi">            1  -&gt; bone        1.96      0.12           </span>
<span class="fsi">  ...      ...    ...         ...                      </span>
<span class="fsi">  duralumin 6  -&gt; duralumin   0.98      0.03           </span>
<span class="fsi">  ...      ...    ...         ...                      </span>

<span class="c">// average deflection by sample type</span>
<span onmouseout="hideTip(event, 'fst6', 11)" onmouseover="showTip(event, 'fst6', 11)" class="i">bySample</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst9', 12)" onmouseover="showTip(event, 'fst9', 12)" class="i">Columns</span><span class="o">.</span>[[<span class="s">&quot;</span><span class="s">sample</span><span class="s"> </span><span class="s">type</span><span class="s">&quot;</span>; <span class="s">&quot;</span><span class="s">deflection</span><span class="s">&lt;</span><span class="s">mm</span><span class="s">&gt;</span><span class="s">&quot;</span>]] <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst4', 13)" onmouseover="showTip(event, 'fst4', 13)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst10', 14)" onmouseover="showTip(event, 'fst10', 14)" class="i">meanLevel</span> <span onmouseout="hideTip(event, 'fst11', 15)" onmouseover="showTip(event, 'fst11', 15)" class="i">Pair</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst12', 16)" onmouseover="showTip(event, 'fst12', 16)" class="i">get1Of2</span>
<span class="fsi">val it : Frame&lt;string,string&gt; =</span>
<span class="fsi">               deflection&lt;mm&gt;    </span>
<span class="fsi">  bone      -&gt; 0.228333333333333 </span>
<span class="fsi">  duralumin -&gt; 0.116666666666667 </span>
<span class="fsi">  ...       -&gt; ...               </span>

<span class="c">// select the data for bones</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst13', 17)" onmouseover="showTip(event, 'fst13', 17)" class="i">bones</span> <span class="o">=</span> (<span onmouseout="hideTip(event, 'fst4', 18)" onmouseover="showTip(event, 'fst4', 18)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst14', 19)" onmouseover="showTip(event, 'fst14', 19)" class="i">nest</span> <span onmouseout="hideTip(event, 'fst6', 20)" onmouseover="showTip(event, 'fst6', 20)" class="i">bySample</span>)<span class="o">.</span>[<span class="s">&quot;</span><span class="s">bone</span><span class="s">&quot;</span>]
<span class="fsi">val bones : Frame&lt;int,string&gt; =</span>
<span class="fsi">       sample type p         deflection&lt;mm&gt; </span>
<span class="fsi"> 0  -&gt; bone        0.98      0.06           </span>
<span class="fsi"> 1  -&gt; bone        1.96      0.12           </span>
<span class="fsi"> ...-&gt; ...         ...       ...            </span>
</pre></td></tr></table>   

You may notice that some of the values in the table are missing (the handwriting can be completely unparsable!), by default they are omited, but we can always specify how we want this data to be filled using ````Direction```` or a custom function.  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span class="c">// note that there&#39;re missing values in this dataset</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst15', 21)" onmouseover="showTip(event, 'fst15', 21)" class="i">deflections</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst13', 22)" onmouseover="showTip(event, 'fst13', 22)" class="i">bones</span><span class="o">?</span><span class="i">``deflection&lt;mm&gt;``</span>
<span class="fsi">val deflections : Series&lt;int,float&gt; =</span>
<span class="fsi"> 0  -&gt; 0.06      </span>
<span class="fsi"> 1  -&gt; 0.12      </span>
<span class="fsi"> ...-&gt; ...       </span>
<span class="fsi"> 12 -&gt; &lt;missing&gt; </span>
<span onmouseout="hideTip(event, 'fst16', 23)" onmouseover="showTip(event, 'fst16', 23)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst17', 24)" onmouseover="showTip(event, 'fst17', 24)" class="i">mean</span> <span onmouseout="hideTip(event, 'fst15', 25)" onmouseover="showTip(event, 'fst15', 25)" class="i">deflections</span>
<span class="fsi"> val it : float = 0.2283333333 </span>
<span class="c">// omit missing values</span>
<span onmouseout="hideTip(event, 'fst15', 26)" onmouseover="showTip(event, 'fst15', 26)" class="i">deflections</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst16', 27)" onmouseover="showTip(event, 'fst16', 27)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst18', 28)" onmouseover="showTip(event, 'fst18', 28)" class="i">dropMissing</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst16', 29)" onmouseover="showTip(event, 'fst16', 29)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst17', 30)" onmouseover="showTip(event, 'fst17', 30)" class="i">mean</span>
<span class="fsi"> val it : float = 0.2283333333 </span>
<span class="c">// fill missing values by copying forward</span>
<span onmouseout="hideTip(event, 'fst15', 31)" onmouseover="showTip(event, 'fst15', 31)" class="i">deflections</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst16', 32)" onmouseover="showTip(event, 'fst16', 32)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst19', 33)" onmouseover="showTip(event, 'fst19', 33)" class="i">fillMissing</span> <span onmouseout="hideTip(event, 'fst20', 34)" onmouseover="showTip(event, 'fst20', 34)" class="i">Direction</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst21', 35)" onmouseover="showTip(event, 'fst21', 35)" class="i">Forward</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst16', 36)" onmouseover="showTip(event, 'fst16', 36)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst17', 37)" onmouseover="showTip(event, 'fst17', 37)" class="i">mean</span>
<span class="fsi"> val it : float = 0.2542857143 </span>
</pre></td></tr></table>    

Now let's check if there's any relation between the deflection and load. In theory, it's supposed to be linear and we're going to test that with a linear regression.  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span class="c">/// Find slope and intercept with linear regression</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst22', 38)" onmouseover="showTip(event, 'fst22', 38)" class="i">linearRegression</span> <span onmouseout="hideTip(event, 'fst23', 39)" onmouseover="showTip(event, 'fst23', 39)" class="i">xs</span> <span onmouseout="hideTip(event, 'fst24', 40)" onmouseover="showTip(event, 'fst24', 40)" class="i">ys</span> <span class="o">=</span> <span id="fstt25" onmouseout="hideTip(event, 'fst25', 41)" onmouseover="showTip(event, 'fst25', 41, document.getElementById('fstt25'))" class="omitted">(...)</span>
<span class="c">// drop rows with missing values</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst26', 42)" onmouseover="showTip(event, 'fst26', 42)" class="i">bonesreg</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 43)" onmouseover="showTip(event, 'fst4', 43)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst27', 44)" onmouseover="showTip(event, 'fst27', 44)" class="i">dropSparseRows</span> <span onmouseout="hideTip(event, 'fst13', 45)" onmouseover="showTip(event, 'fst13', 45)" class="i">bones</span>
<span class="fsi">val bonesreg : Frame&lt;int,string&gt; =</span>
<span class="fsi">        sample type p    deflection&lt;mm&gt; </span>
<span class="fsi">   0 -&gt; bone        0.98 0.06           </span>
<span class="fsi"> ... -&gt; ...         ...  ...            </span>
<span class="fsi">   5 -&gt; bone        5.88 0.41           </span>

<span class="k">let</span> <span onmouseout="hideTip(event, 'fst28', 46)" onmouseover="showTip(event, 'fst28', 46)" class="i">load</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst16', 47)" onmouseover="showTip(event, 'fst16', 47)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst29', 48)" onmouseover="showTip(event, 'fst29', 48)" class="i">values</span> <span onmouseout="hideTip(event, 'fst26', 49)" onmouseover="showTip(event, 'fst26', 49)" class="i">bonesreg</span><span class="o">?</span><span class="i">p</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst30', 50)" onmouseover="showTip(event, 'fst30', 50)" class="i">defl</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst16', 51)" onmouseover="showTip(event, 'fst16', 51)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst29', 52)" onmouseover="showTip(event, 'fst29', 52)" class="i">values</span> <span onmouseout="hideTip(event, 'fst26', 53)" onmouseover="showTip(event, 'fst26', 53)" class="i">bonesreg</span><span class="o">?</span><span class="i">``deflection&lt;mm&gt;``</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst31', 54)" onmouseover="showTip(event, 'fst31', 54)" class="i">slope</span>, <span onmouseout="hideTip(event, 'fst32', 55)" onmouseover="showTip(event, 'fst32', 55)" class="i">intercept</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst22', 56)" onmouseover="showTip(event, 'fst22', 56)" class="i">linearRegression</span> <span onmouseout="hideTip(event, 'fst28', 57)" onmouseover="showTip(event, 'fst28', 57)" class="i">load</span> <span onmouseout="hideTip(event, 'fst30', 58)" onmouseover="showTip(event, 'fst30', 58)" class="i">defl</span>   
<span class="fsi">val slope : float = 0.07142857143 </span>
<span class="fsi">val intercept : float = -0.01666666667 </span>
</pre></td></tr></table>  

![Chart]({{ site.url }}/images/deedle_chart_small.png)
{: .image-right}  

Does this line make a good fit? Here is a chart with a couple of samples from this dataset.  

On the other hand a classical metric like R^2 can help to answer this question too, especially when it's extremely simple to add a new column to the dataframe and perform some operations.  

The new library is tried out, the lab is completed - everyone is happy ^_^  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span onmouseout="hideTip(event, 'fst26', 59)" onmouseover="showTip(event, 'fst26', 59)" class="i">bonesreg</span><span class="o">?</span><span class="i">prediction</span> <span class="o">&lt;-</span> <span onmouseout="hideTip(event, 'fst32', 60)" onmouseover="showTip(event, 'fst32', 60)" class="i">intercept</span> <span class="o">+</span> <span onmouseout="hideTip(event, 'fst31', 61)" onmouseover="showTip(event, 'fst31', 61)" class="i">slope</span> <span class="o">*</span> <span onmouseout="hideTip(event, 'fst26', 62)" onmouseover="showTip(event, 'fst26', 62)" class="i">bonesreg</span><span class="o">?</span><span class="i">p</span> 
<span onmouseout="hideTip(event, 'fst26', 63)" onmouseover="showTip(event, 'fst26', 63)" class="i">bonesreg</span><span class="o">?</span><span class="i">residualsq</span> <span class="o">&lt;-</span> <span onmouseout="hideTip(event, 'fst26', 64)" onmouseover="showTip(event, 'fst26', 64)" class="i">bonesreg</span><span class="o">?</span><span class="i">prediction</span> <span class="o">-</span> <span onmouseout="hideTip(event, 'fst26', 65)" onmouseover="showTip(event, 'fst26', 65)" class="i">bonesreg</span><span class="o">?</span><span class="i">``deflection&lt;mm&gt;``</span> 
                       <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst16', 66)" onmouseover="showTip(event, 'fst16', 66)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst33', 67)" onmouseover="showTip(event, 'fst33', 67)" class="i">mapValues</span> (<span class="k">fun</span> <span onmouseout="hideTip(event, 'fst34', 68)" onmouseover="showTip(event, 'fst34', 68)" class="i">x</span> <span class="k">-&gt;</span> <span onmouseout="hideTip(event, 'fst34', 69)" onmouseover="showTip(event, 'fst34', 69)" class="i">x</span><span class="o">*</span><span onmouseout="hideTip(event, 'fst34', 70)" onmouseover="showTip(event, 'fst34', 70)" class="i">x</span>)
<span onmouseout="hideTip(event, 'fst26', 71)" onmouseover="showTip(event, 'fst26', 71)" class="i">bonesreg</span>
<span class="fsi">val it : Frame&lt;int,string&gt; =</span>
<span class="fsi">       sample type p    deflection&lt;mm&gt; prediction         residualsq           </span>
<span class="fsi">  0 -&gt; bone        0.98 0.06           0.0533333333333334 4.44444444444441E-05 </span>
<span class="fsi">  1 -&gt; bone        1.96 0.12           0.123333333333333  1.11111111111111E-05 </span>
<span class="fsi"> ...-&gt; bone        ...  ...            ...                ...                  </span>

<span class="k">let</span> <span onmouseout="hideTip(event, 'fst35', 72)" onmouseover="showTip(event, 'fst35', 72)" class="i">sdvs</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 73)" onmouseover="showTip(event, 'fst4', 73)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst36', 74)" onmouseover="showTip(event, 'fst36', 74)" class="i">sdv</span> <span onmouseout="hideTip(event, 'fst26', 75)" onmouseover="showTip(event, 'fst26', 75)" class="i">bonesreg</span>
<span class="fsi">val sdvs : Series&lt;string,float&gt; =</span>
<span class="fsi">  sample type    -&gt; &lt;missing&gt;           </span>
<span class="fsi">  p              -&gt; 1.83341211951923    </span>
<span class="fsi">  deflection&lt;mm&gt; -&gt; 0.131059782796503   </span>
<span class="fsi">  prediction     -&gt; 0.130958008537088   </span>
<span class="fsi">  residualsq     -&gt; 1.72132593164778E-05</span>

<span class="c">// compute the metrics:</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst37', 76)" onmouseover="showTip(event, 'fst37', 76)" class="i">rsquare</span> <span class="o">=</span> <span class="k">let</span> <span onmouseout="hideTip(event, 'fst34', 77)" onmouseover="showTip(event, 'fst34', 77)" class="i">x</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst35', 78)" onmouseover="showTip(event, 'fst35', 78)" class="i">sdvs</span><span class="o">.</span>[<span class="s">&quot;</span><span class="s">prediction</span><span class="s">&quot;</span>] <span class="o">/</span> <span onmouseout="hideTip(event, 'fst35', 79)" onmouseover="showTip(event, 'fst35', 79)" class="i">sdvs</span><span class="o">.</span>[<span class="s">&quot;</span><span class="s">deflection</span><span class="s">&lt;</span><span class="s">mm</span><span class="s">&gt;</span><span class="s">&quot;</span>] <span class="k">in</span> <span onmouseout="hideTip(event, 'fst34', 80)" onmouseover="showTip(event, 'fst34', 80)" class="i">x</span> <span class="o">*</span> <span onmouseout="hideTip(event, 'fst34', 81)" onmouseover="showTip(event, 'fst34', 81)" class="i">x</span>
<span class="fsi">val rsquare : float = 0.9984475063 </span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst38', 82)" onmouseover="showTip(event, 'fst38', 82)" class="i">df</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst4', 83)" onmouseover="showTip(event, 'fst4', 83)" class="i">Frame</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst39', 84)" onmouseover="showTip(event, 'fst39', 84)" class="i">countRows</span> <span onmouseout="hideTip(event, 'fst26', 85)" onmouseover="showTip(event, 'fst26', 85)" class="i">bonesreg</span> <span class="o">-</span> <span class="n">2</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst40', 86)" onmouseover="showTip(event, 'fst40', 86)" class="i">float</span>
<span class="fsi">val df : float = 4.0 </span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst41', 87)" onmouseover="showTip(event, 'fst41', 87)" class="i">tvalue</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst42', 88)" onmouseover="showTip(event, 'fst42', 88)" class="i">sqrt</span> (<span onmouseout="hideTip(event, 'fst37', 89)" onmouseover="showTip(event, 'fst37', 89)" class="i">rsquare</span> <span class="o">/</span> (<span class="n">1.</span> <span class="o">-</span> <span onmouseout="hideTip(event, 'fst37', 90)" onmouseover="showTip(event, 'fst37', 90)" class="i">rsquare</span>) <span class="o">*</span> <span onmouseout="hideTip(event, 'fst38', 91)" onmouseover="showTip(event, 'fst38', 91)" class="i">df</span>)
<span class="fsi">val tvalue : float = 50.71981861 </span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst43', 92)" onmouseover="showTip(event, 'fst43', 92)" class="i">se</span> <span class="o">=</span> (<span onmouseout="hideTip(event, 'fst16', 93)" onmouseover="showTip(event, 'fst16', 93)" class="i">Series</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst44', 94)" onmouseover="showTip(event, 'fst44', 94)" class="i">sum</span> <span onmouseout="hideTip(event, 'fst26', 95)" onmouseover="showTip(event, 'fst26', 95)" class="i">bonesreg</span><span class="o">?</span><span class="i">residualsq</span>) <span class="o">/</span> <span onmouseout="hideTip(event, 'fst38', 96)" onmouseover="showTip(event, 'fst38', 96)" class="i">df</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, 'fst42', 97)" onmouseover="showTip(event, 'fst42', 97)" class="i">sqrt</span>
<span class="fsi">val se : float = 0.005773502692 </span>
</pre></td></tr></table>    

Yes, it's that simple.  

<br/><br />
<br/><br />

[^1]: to be honest, still don't get why anyone would need that, maybe that's the point where being a PhD helps?  

<div class="tip" id="fst1">namespace System</div>
<div class="tip" id="fst2">namespace Deedle</div>
<div class="tip" id="fst3">val ds : Frame&lt;int,string&gt;</p>
<p>Full name: Regression.ds</p></div>
<div class="tip" id="fst4">Multiple items<br />module Frame</p>
<p>from Deedle</p>
<p>--------------------<br />type Frame&lt;&#39;TRowKey,&#39;TColumnKey (requires equality and equality)&gt; =<br />&#160;&#160;interface IDynamicMetaObjectProvider<br />&#160;&#160;interface INotifyCollectionChanged<br />&#160;&#160;interface IFsiFormattable<br />&#160;&#160;interface IFrame<br />&#160;&#160;new : names:seq&lt;&#39;TColumnKey&gt; * columns:seq&lt;ISeries&lt;&#39;TRowKey&gt;&gt; -&gt; Frame&lt;&#39;TRowKey,&#39;TColumnKey&gt;<br />&#160;&#160;private new : rowIndex:IIndex&lt;&#39;TRowKey&gt; * columnIndex:IIndex&lt;&#39;TColumnKey&gt; * data:IVector&lt;IVector&gt; -&gt; Frame&lt;&#39;TRowKey,&#39;TColumnKey&gt;<br />&#160;&#160;member AddSeries : column:&#39;TColumnKey * series:ISeries&lt;&#39;TRowKey&gt; -&gt; unit<br />&#160;&#160;member AddSeries : column:&#39;TColumnKey * series:seq&lt;&#39;V&gt; -&gt; unit<br />&#160;&#160;member AddSeries : column:&#39;TColumnKey * series:ISeries&lt;&#39;TRowKey&gt; * lookup:Lookup -&gt; unit<br />&#160;&#160;member AddSeries : column:&#39;TColumnKey * series:seq&lt;&#39;V&gt; * lookup:Lookup -&gt; unit<br />&#160;&#160;...</p>
<p>Full name: Deedle.Frame&lt;_,_&gt;</p>
<p>--------------------<br />type Frame =<br />&#160;&#160;static member CreateEmpty : unit -&gt; Frame&lt;&#39;R,&#39;C&gt; (requires equality and equality)<br />&#160;&#160;static member FromColumns : cols:Series&lt;&#39;TColKey,Series&lt;&#39;TRowKey,&#39;V&gt;&gt; -&gt; Frame&lt;&#39;TRowKey,&#39;TColKey&gt; (requires equality and equality)<br />&#160;&#160;static member FromColumns : cols:Series&lt;&#39;TColKey,ObjectSeries&lt;&#39;TRowKey&gt;&gt; -&gt; Frame&lt;&#39;TRowKey,&#39;TColKey&gt; (requires equality and equality)<br />&#160;&#160;static member FromColumns : columns:seq&lt;KeyValuePair&lt;&#39;ColKey,ObjectSeries&lt;&#39;RowKey&gt;&gt;&gt; -&gt; Frame&lt;&#39;RowKey,&#39;ColKey&gt; (requires equality and equality)<br />&#160;&#160;static member FromColumns : columns:seq&lt;KeyValuePair&lt;&#39;ColKey,Series&lt;&#39;RowKey,&#39;V&gt;&gt;&gt; -&gt; Frame&lt;&#39;RowKey,&#39;ColKey&gt; (requires equality and equality)<br />&#160;&#160;static member FromColumns : rows:seq&lt;Series&lt;&#39;ColKey,&#39;V&gt;&gt; -&gt; Frame&lt;&#39;ColKey,int&gt; (requires equality)<br />&#160;&#160;static member FromRecords : values:seq&lt;&#39;T&gt; -&gt; Frame&lt;int,string&gt;<br />&#160;&#160;static member FromRecords : series:Series&lt;&#39;K,&#39;R&gt; -&gt; Frame&lt;&#39;K,string&gt; (requires equality)<br />&#160;&#160;static member FromRowKeys : keys:seq&lt;&#39;K&gt; -&gt; Frame&lt;&#39;K,string&gt; (requires equality)<br />&#160;&#160;static member FromRows : rows:Series&lt;&#39;TColKey,Series&lt;&#39;TRowKey,&#39;V&gt;&gt; -&gt; Frame&lt;&#39;TColKey,&#39;TRowKey&gt; (requires equality and equality)<br />&#160;&#160;...</p>
<p>Full name: Deedle.Frame</p>
<p>--------------------<br />new : names:seq&lt;&#39;TColumnKey&gt; * columns:seq&lt;ISeries&lt;&#39;TRowKey&gt;&gt; -&gt; Frame&lt;&#39;TRowKey,&#39;TColumnKey&gt;</div>
<div class="tip" id="fst5">static member Frame.ReadCsv : path:string * ?hasHeaders:bool * ?inferTypes:bool * ?inferRows:int * ?schema:string * ?separators:string * ?culture:string -&gt; Frame&lt;int,string&gt;<br />static member Frame.ReadCsv : stream:IO.Stream * ?hasHeaders:bool * ?inferTypes:bool * ?inferRows:int * ?schema:string * ?separators:string * ?culture:string -&gt; Frame&lt;int,string&gt;</div>
<div class="tip" id="fst6">val bySample : Frame&lt;(string * int),string&gt;</p>
<p>Full name: Regression.bySample</p></div>
<div class="tip" id="fst7">property Frame.Columns: ColumnSeries&lt;int,string&gt;</div>
<div class="tip" id="fst8">val groupRowsByString : column:&#39;a -&gt; frame:Frame&lt;&#39;b,&#39;a&gt; -&gt; Frame&lt;(string * &#39;b),&#39;a&gt; (requires equality and equality)</p>
<p>Full name: Deedle.Frame.groupRowsByString</p></div>
<div class="tip" id="fst9">property Frame.Columns: ColumnSeries&lt;(string * int),string&gt;</div>
<div class="tip" id="fst10">val meanLevel : keySelector:(&#39;R -&gt; &#39;a) -&gt; frame:Frame&lt;&#39;R,&#39;C&gt; -&gt; Frame&lt;&#39;a,&#39;C&gt; (requires equality and equality and equality)</p>
<p>Full name: Deedle.Frame.meanLevel</p></div>
<div class="tip" id="fst11">module Pair</p>
<p>from Deedle</p></div>
<div class="tip" id="fst12">val get1Of2 : v:&#39;a * &#39;b -&gt; &#39;a</p>
<p>Full name: Deedle.Pair.get1Of2</p></div>
<div class="tip" id="fst13">val bones : Frame&lt;int,string&gt;</p>
<p>Full name: Regression.bones</p></div>
<div class="tip" id="fst14">val nest : frame:Frame&lt;(&#39;R1 * &#39;R2),&#39;C&gt; -&gt; Series&lt;&#39;R1,Frame&lt;&#39;R2,&#39;C&gt;&gt; (requires equality and equality and equality)</p>
<p>Full name: Deedle.Frame.nest</p></div>
<div class="tip" id="fst15">val deflections : Series&lt;int,float&gt;</p>
<p>Full name: Regression.deflections</p></div>
<div class="tip" id="fst16">Multiple items<br />module Series</p>
<p>from Deedle</p>
<p>--------------------<br />type Series&lt;&#39;K,&#39;V (requires equality)&gt; =<br />&#160;&#160;interface IFsiFormattable<br />&#160;&#160;interface ISeries&lt;&#39;K&gt;<br />&#160;&#160;new : pairs:seq&lt;KeyValuePair&lt;&#39;K,&#39;V&gt;&gt; -&gt; Series&lt;&#39;K,&#39;V&gt;<br />&#160;&#160;new : keys:seq&lt;&#39;K&gt; * values:seq&lt;&#39;V&gt; -&gt; Series&lt;&#39;K,&#39;V&gt;<br />&#160;&#160;new : index:IIndex&lt;&#39;K&gt; * vector:IVector&lt;&#39;V&gt; * vectorBuilder:IVectorBuilder * indexBuilder:IIndexBuilder -&gt; Series&lt;&#39;K,&#39;V&gt;<br />&#160;&#160;member Aggregate : aggregation:Aggregation&lt;&#39;K&gt; * observationSelector:Func&lt;DataSegment&lt;Series&lt;&#39;K,&#39;V&gt;&gt;,KeyValuePair&lt;&#39;TNewKey,OptionalValue&lt;&#39;R&gt;&gt;&gt; -&gt; Series&lt;&#39;TNewKey,&#39;R&gt; (requires equality)<br />&#160;&#160;member Aggregate : aggregation:Aggregation&lt;&#39;K&gt; * keySelector:Func&lt;DataSegment&lt;Series&lt;&#39;K,&#39;V&gt;&gt;,&#39;TNewKey&gt; * valueSelector:Func&lt;DataSegment&lt;Series&lt;&#39;K,&#39;V&gt;&gt;,&#39;R&gt; -&gt; Series&lt;&#39;TNewKey,&#39;R&gt; (requires equality)<br />&#160;&#160;member Append : otherSeries:Series&lt;&#39;K,&#39;V&gt; -&gt; Series&lt;&#39;K,&#39;V&gt;<br />&#160;&#160;member AsyncMaterialize : unit -&gt; Async&lt;Series&lt;&#39;K,&#39;V&gt;&gt;<br />&#160;&#160;override Equals : another:obj -&gt; bool<br />&#160;&#160;...</p>
<p>Full name: Deedle.Series&lt;_,_&gt;</p>
<p>--------------------<br />type Series =<br />&#160;&#160;static member ofNullables : values:seq&lt;Nullable&lt;&#39;a0&gt;&gt; -&gt; Series&lt;int,&#39;a0&gt; (requires default constructor and value type and &#39;a0 :&gt; ValueType)<br />&#160;&#160;static member ofObservations : observations:seq&lt;&#39;a0 * &#39;a1&gt; -&gt; Series&lt;&#39;a0,&#39;a1&gt; (requires equality)<br />&#160;&#160;static member ofOptionalObservations : observations:seq&lt;&#39;K * OptionalValue&lt;&#39;a1&gt;&gt; -&gt; Series&lt;&#39;K,&#39;a1&gt; (requires equality)<br />&#160;&#160;static member ofValues : values:seq&lt;&#39;a0&gt; -&gt; Series&lt;int,&#39;a0&gt;</p>
<p>Full name: Deedle.FSharpSeriesExtensions.Series</p>
<p>--------------------<br />new : pairs:seq&lt;Collections.Generic.KeyValuePair&lt;&#39;K,&#39;V&gt;&gt; -&gt; Series&lt;&#39;K,&#39;V&gt;<br />new : keys:seq&lt;&#39;K&gt; * values:seq&lt;&#39;V&gt; -&gt; Series&lt;&#39;K,&#39;V&gt;<br />new : index:Indices.IIndex&lt;&#39;K&gt; * vector:IVector&lt;&#39;V&gt; * vectorBuilder:Vectors.IVectorBuilder * indexBuilder:Indices.IIndexBuilder -&gt; Series&lt;&#39;K,&#39;V&gt;</div>
<div class="tip" id="fst17">val mean : series:Series&lt;&#39;K,&#39;V&gt; -&gt; &#39;V (requires equality and member ( + ) and member DivideByInt and member get_Zero)</p>
<p>Full name: Deedle.Series.mean</p></div>
<div class="tip" id="fst18">val dropMissing : series:Series&lt;&#39;K,&#39;T&gt; -&gt; Series&lt;&#39;K,&#39;T&gt; (requires equality)</p>
<p>Full name: Deedle.Series.dropMissing</p></div>
<div class="tip" id="fst19">val fillMissing : direction:Direction -&gt; series:Series&lt;&#39;K,&#39;T&gt; -&gt; Series&lt;&#39;K,&#39;T&gt; (requires equality)</p>
<p>Full name: Deedle.Series.fillMissing</p></div>
<div class="tip" id="fst20">type Direction =<br />&#160;&#160;|  Backward  =  0<br />&#160;&#160;|  Forward  =  1</p>
<p>Full name: Deedle.Direction</p></div>
<div class="tip" id="fst21">Direction.Forward: Direction = 1</div>
<div class="tip" id="fst22">val linearRegression : xs:seq&lt;float&gt; -&gt; ys:seq&lt;float&gt; -&gt; float * float</p>
<p>Full name: Regression.linearRegression<br /><em></p>
<p>&#160;Find slope and intercept with linear regression</em></div>
<div class="tip" id="fst23">val xs : seq&lt;float&gt;</div>
<div class="tip" id="fst24">val ys : seq&lt;float&gt;</div>
<div class="tip" id="fst25">let x, y, xx, xy, n =<br />&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;Seq.zip xs ys<br />&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;|&gt; Seq.fold (fun (xsum, ysum, xxsum, xysum, n) (x, y) -&gt;<br />&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;xsum+x, ysum+y, xxsum+x*x, xysum+x*y, n+1.) (0.,0.,0.,0.,0.)<br />&#160;&#160;&#160;&#160;let slope = (n * xy - x * y) / (n * xx - x * x)<br />&#160;&#160;&#160;&#160;let intercept = (y - slope * x) / n<br />&#160;&#160;&#160;&#160;slope, intercept</div>
<div class="tip" id="fst26">val bonesreg : Frame&lt;int,string&gt;</p>
<p>Full name: Regression.bonesreg</p></div>
<div class="tip" id="fst27">val dropSparseRows : frame:Frame&lt;&#39;R,&#39;C&gt; -&gt; Frame&lt;&#39;R,&#39;C&gt; (requires equality and equality)</p>
<p>Full name: Deedle.Frame.dropSparseRows</p></div>
<div class="tip" id="fst28">val load : seq&lt;float&gt;</p>
<p>Full name: Regression.load</p></div>
<div class="tip" id="fst29">val values : series:Series&lt;&#39;K,&#39;T&gt; -&gt; seq&lt;&#39;T&gt; (requires equality)</p>
<p>Full name: Deedle.Series.values</p></div>
<div class="tip" id="fst30">val defl : seq&lt;float&gt;</p>
<p>Full name: Regression.defl</p></div>
<div class="tip" id="fst31">val slope : float</p>
<p>Full name: Regression.slope</p></div>
<div class="tip" id="fst32">val intercept : float</p>
<p>Full name: Regression.intercept</p></div>
<div class="tip" id="fst33">val mapValues : f:(&#39;T -&gt; &#39;R) -&gt; series:Series&lt;&#39;K,&#39;T&gt; -&gt; Series&lt;&#39;K,&#39;R&gt; (requires equality)</p>
<p>Full name: Deedle.Series.mapValues</p></div>
<div class="tip" id="fst34">val x : float</div>
<div class="tip" id="fst35">val sdvs : Series&lt;string,float&gt;</p>
<p>Full name: Regression.sdvs</p></div>
<div class="tip" id="fst36">val sdv : frame:Frame&lt;&#39;R,&#39;C&gt; -&gt; Series&lt;&#39;C,float&gt; (requires equality and equality)</p>
<p>Full name: Deedle.Frame.sdv</p></div>
<div class="tip" id="fst37">val rsquare : float</p>
<p>Full name: Regression.rsquare</p></div>
<div class="tip" id="fst38">val df : float</p>
<p>Full name: Regression.df</p></div>
<div class="tip" id="fst39">val countRows : frame:Frame&lt;&#39;R,&#39;C&gt; -&gt; int (requires equality and equality)</p>
<p>Full name: Deedle.Frame.countRows</p></div>
<div class="tip" id="fst40">Multiple items<br />val float : value:&#39;T -&gt; float (requires member op_Explicit)</p>
<p>Full name: Microsoft.FSharp.Core.Operators.float</p>
<p>--------------------<br />type float&lt;&#39;Measure&gt; = float</p>
<p>Full name: Microsoft.FSharp.Core.float&lt;_&gt;</p>
<p>--------------------<br />type float = Double</p>
<p>Full name: Microsoft.FSharp.Core.float</p></div>
<div class="tip" id="fst41">val tvalue : float</p>
<p>Full name: Regression.tvalue</p></div>
<div class="tip" id="fst42">val sqrt : value:&#39;T -&gt; &#39;U (requires member Sqrt)</p>
<p>Full name: Microsoft.FSharp.Core.Operators.sqrt</p></div>
<div class="tip" id="fst43">val se : float</p>
<p>Full name: Regression.se</p></div>
<div class="tip" id="fst44">val sum : series:Series&lt;&#39;K,&#39;V&gt; -&gt; &#39;V (requires equality and member ( + ) and member get_Zero)</p>
<p>Full name: Deedle.Series.sum</p></div>
