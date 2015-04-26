---
layout: post
title: "Traditions vs Statistics: Sechseläuten"
modified:
categories: 
description:
tags: Zurich statistics fsharp deedle R
image:
  feature: sechselaeuten/header.jpg
  credit: Böögg (zuerich.com)
  creditlink: https://www.zuerich.com/en/visit/sechselaeuten
comments:
share:
date: 2015-04-25T00:44:52+02:00
---
What do you expect from this summer? Should you worry about _20:39_ or not? Sunglasses or umbrella? Let's find out! [^1]

#### Traditions  

Each country has its own fun traditions and festivals, and Switzerland is not an exception. One of the most exciting events is the spring festival, [Sechseläuten](https://www.zuerich.com/en/visit/history-tradition), which is celebrated in Zürich in April. And it's also a day when you realize, that there're some people in town except the tourists walking along the Bahnhofstrasse on Saturdays! The trees become green, the flags appear here and there, the city becomes lively and colourful. And this year the day was just perfectly sunny. Not the best one for the drivers though because of the [parade of Guilds](https://www.youtube.com/watch?v=VHGKHn-Om-o) (everyone I showed the video especially likes the bear ~1:57 :).  

![Parade](https://static1.zuerich.com/sites/default/files/styles/keyvisual_m/public/keyvisual/web_zurich_events_sechselaeuten_04.jpg?itok=01ryPrDs) 
{: .image-center}  

The culmination of Sechseläuten is the burning of [Böögg](https://www.zuerich.com/en/visit/who-or-what-is-the-boeoegg), a figure of snowman - the winter symbol. There's a belief, that the faster the head explodes, the nicer summer is going to be: say, below 10 min is for sunny and warm weather, above 15 min - for rains and cold... This year it took 20 min 39 s! Doesn't look very nice, huh?  

#### Statistics  

So is there any correlation between the Böögg's forecast and actual weather? I couldn't resist checking the facts and downloaded the daily data for the Zürich weather station from the [European Climate Assessment & Dataset](http://eca.knmi.nl/) - it's free and contains a lot of information for the 20th century, including the period we're interested in, I was lucky to find it rather quickly! One can find the yearly Sechseläuten statistics, including the burning times, on the official [Sechseläuten website](http://www.sechselaeuten.ch/sechselaeuten/statistik.asp) (in German).

The criteria for what makes a _nice summer_ are pretty subjective - I picked the mean, min and max temperature, hours of sunshine, cloud cover, humidity and precipitation. Let's assume the _cold summer_ means lower temperatures, or more clouds, or less sunshine, or all of these together.  

Now when we know more or less what we'd like to look at, we need to prepare the extract that information from the data files - a bunch of simple csv files.  

The weather datasets uses _-9999_ as indication of missing data, and another relevant column contains quality codes. As there's no requirement for each day's data to be present, we'll select only the valid records.

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
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="k">type</span> <span class="i">QualityCode</span> <span class="o">=</span>
  | <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">Valid</span> <span class="o">=</span> <span class="n">0</span>
  | <span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">Suspect</span> <span class="o">=</span> <span class="n">1</span>
  | <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">Missing</span> <span class="o">=</span> <span class="n">9</span>
  
<span class="c">// read the dataset from a file, index by DATE and remove unnecessary columns</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst4', 4)" onmouseover="showTip(event, 'fst4', 4)" class="i">readData</span> (<span onmouseout="hideTip(event, 'fst5', 5)" onmouseover="showTip(event, 'fst5', 5)" class="i">fileName</span><span class="o">:</span> <span onmouseout="hideTip(event, 'fst6', 6)" onmouseover="showTip(event, 'fst6', 6)" class="i">string</span>) <span class="o">=</span> 
    <span class="i">Frame</span><span class="o">.</span><span class="i">ReadCsv</span> <span onmouseout="hideTip(event, 'fst5', 7)" onmouseover="showTip(event, 'fst5', 7)" class="i">fileName</span>  
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">indexRowsInt</span> <span class="s">&quot;</span><span class="s">DATE</span><span class="s">&quot;</span>
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">filterRowValues</span> (<span class="k">fun</span> <span class="i">r</span> <span class="k">-&gt;</span> <span class="i">r</span><span class="o">?</span><span class="i">Q</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fst7', 8)" onmouseover="showTip(event, 'fst7', 8)" class="i">float</span> <span onmouseout="hideTip(event, 'fst8', 9)" onmouseover="showTip(event, 'fst8', 9)" class="i">QualityCode</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst1', 10)" onmouseover="showTip(event, 'fst1', 10)" class="i">Valid</span>)
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">dropCol</span> <span class="s">&quot;</span><span class="s">SOUID</span><span class="s">&quot;</span>
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">dropCol</span> <span class="s">&quot;</span><span class="s">Q</span><span class="s">&quot;</span>

<span class="k">let</span> <span onmouseout="hideTip(event, 'fst9', 11)" onmouseover="showTip(event, 'fst9', 11)" class="i">dataFrames</span> <span class="o">=</span> 
    <span onmouseout="hideTip(event, 'fst10', 12)" onmouseover="showTip(event, 'fst10', 12)" class="i">List</span><span class="o">.</span><span onmouseout="hideTip(event, 'fst11', 13)" onmouseover="showTip(event, 'fst11', 13)" class="i">map</span> <span onmouseout="hideTip(event, 'fst4', 14)" onmouseover="showTip(event, 'fst4', 14)" class="i">readData</span>
        [
            <span class="s">&quot;</span><span class="s">mean_temperature</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span> <span class="c">// TG, in 0.1 C</span>
            <span class="s">&quot;</span><span class="s">min_temperature</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span>  <span class="c">// TN, in 0.1 C</span>
            <span class="s">&quot;</span><span class="s">max_temperature</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span>  <span class="c">// TX, in 0.1 C</span>
            <span class="s">&quot;</span><span class="s">precipitation</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span>    <span class="c">// RR, in 0.1 mm</span>
            <span class="s">&quot;</span><span class="s">sunshine</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span>         <span class="c">// SS, in 0.1 hrs</span>
            <span class="s">&quot;</span><span class="s">humidity</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span>         <span class="c">// HU, in 1%</span>
            <span class="s">&quot;</span><span class="s">clouds</span><span class="s">.</span><span class="s">txt</span><span class="s">&quot;</span>           <span class="c">// CC, in oktas</span>
        ]</pre>
</td>
</tr>
</table>


Cloud cover is in [oktas](http://en.wikipedia.org/wiki/Okta), where 0 means perfectly clear sky and 8 - completely cloudy.  

Now we merge all these datasets, select only the summer months, and transform the temperatures to be in degrees C, sunshine - in hours (per day) and so on:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
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
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">weatherData</span> <span class="o">=</span> 
    <span class="i">Frame</span><span class="o">.</span><span class="i">mergeAll</span> <span class="i">dataFrames</span> 
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">filterRows</span> (<span class="k">fun</span> <span class="i">date</span> _ <span class="k">-&gt;</span> <span class="k">let</span> <span class="i">m</span> <span class="o">=</span> <span class="i">date</span> <span class="o">/</span> <span class="n">100</span> <span class="o">%</span> <span class="n">100</span> <span class="k">in</span> <span class="n">6</span> <span class="o">&lt;=</span> <span class="i">m</span> <span class="o">&amp;&amp;</span> <span class="i">m</span> <span class="o">&lt;=</span> <span class="n">8</span>)
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">mapCols</span> (<span class="k">fun</span> <span class="i">col</span> <span class="i">vs</span> <span class="k">-&gt;</span> 
        <span class="k">let</span> <span class="i">series</span> <span class="o">=</span> <span class="i">vs</span><span class="o">.</span><span class="i">As</span>() 
        <span class="k">match</span> <span class="i">col</span> <span class="k">with</span>
        | <span class="s">&quot;</span><span class="s">HU</span><span class="s">&quot;</span> <span class="k">-&gt;</span> <span class="i">series</span> <span class="o">/</span> <span class="n">100.0</span>
        | <span class="s">&quot;</span><span class="s">CC</span><span class="s">&quot;</span> <span class="k">-&gt;</span> <span class="i">series</span>
        | _ <span class="k">-&gt;</span> <span class="i">series</span> <span class="o">/</span> <span class="n">10.0</span>)  </pre>
</td>
</tr>
</table>

Finally, we combine the burning times and weather averages for corresponding years:

<table class="pre"><tr><td class="lines"><pre class="fssnip">
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
<span class="k">let</span> <span onmouseout="hideTip(event, 'fst1', 1)" onmouseover="showTip(event, 'fst1', 1)" class="i">weatherDataByYear</span> <span class="o">=</span>
    <span class="i">weatherData</span>
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">groupRowsByIndex</span> (<span class="k">fun</span> <span class="i">date</span> <span class="k">-&gt;</span> <span class="i">date</span> <span class="o">/</span> <span class="n">10000</span>)
    <span class="o">|&gt;</span> <span class="i">Frame</span><span class="o">.</span><span class="i">applyLevel</span> <span onmouseout="hideTip(event, 'fst2', 2)" onmouseover="showTip(event, 'fst2', 2)" class="i">fst</span> <span class="i">Stats</span><span class="o">.</span><span class="i">mean</span>

<span class="k">let</span> <span onmouseout="hideTip(event, 'fst3', 3)" onmouseover="showTip(event, 'fst3', 3)" class="i">fullStats</span> <span class="o">=</span> <span class="i">Frame</span><span class="o">.</span><span class="i">join</span> <span class="i">JoinKind</span><span class="o">.</span><span class="i">Inner</span> <span class="i">boeoeggStats</span> <span onmouseout="hideTip(event, 'fst1', 4)" onmouseover="showTip(event, 'fst1', 4)" class="i">weatherDataByYear</span>
<span onmouseout="hideTip(event, 'fst3', 5)" onmouseover="showTip(event, 'fst3', 5)" class="i">fullStats</span><span class="o">.</span><span class="i">SaveCsv</span>(<span class="s">&quot;</span><span class="s">fullStats</span><span class="s">.</span><span class="s">csv</span><span class="s">&quot;</span>, <span class="i">keyNames</span> <span class="o">=</span> [<span class="s">&quot;</span><span class="s">year</span><span class="s">&quot;</span>])</pre>
</td>
</tr>
</table>

Now that we have something to look at, let's start with the mean temperatures. We will use R for creating the charts:

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
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="i">meanTempPlot</span> <span class="o">=</span> <span class="i">qplot</span>(<span class="i">duration</span>, 
                     <span class="i">TG</span>, 
                     <span class="i">data</span>   <span class="o">=</span> <span class="i">stats</span>, 
                     <span class="i">geom</span>   <span class="o">=</span> <span class="i">c</span>(<span class="s">&quot;</span><span class="s">point</span><span class="s">&quot;</span>, <span class="s">&quot;</span><span class="s">smooth</span><span class="s">&quot;</span>), 
                     <span class="i">method</span> <span class="o">=</span> <span class="s">&quot;</span><span class="s">lm</span><span class="s">&quot;</span>, 
                     <span class="i">color</span>  <span class="o">=</span> <span class="i">TG</span>,
                     <span class="i">main</span>   <span class="o">=</span> <span class="s">&quot;</span><span class="s">Mean</span><span class="s"> </span><span class="s">Temperature</span><span class="s"> </span><span class="s">~</span><span class="s"> </span><span class="s">Duration</span><span class="s">&quot;</span>, 
                     <span class="i">xlab</span>   <span class="o">=</span> <span class="s">&quot;</span><span class="s">Duration</span><span class="s">,</span><span class="s"> </span><span class="s">min</span><span class="s">&quot;</span>, 
                     <span class="i">ylab</span>   <span class="o">=</span> <span class="s">&quot;</span><span class="s">Temperature</span><span class="s">,</span><span class="s"> </span><span class="s">C</span><span class="s">&quot;</span>)

<span class="i">meanTempBoxplot</span> <span class="o">=</span> <span class="i">qplot</span>(<span class="i">duration</span>, 
                        <span class="i">TG</span>, 
                        <span class="i">data</span> <span class="o">=</span> <span class="i">stats</span>, 
                        <span class="i">geom</span> <span class="o">=</span> <span class="i">c</span>(<span class="s">&quot;</span><span class="s">boxplot</span><span class="s">&quot;</span>, <span class="s">&quot;</span><span class="s">jitter</span><span class="s">&quot;</span>), 
                        <span class="i">outlier</span><span class="o">.</span><span class="i">colour</span> <span class="o">=</span> <span class="s">&quot;</span><span class="s">red</span><span class="s">&quot;</span>, 
                        <span class="i">fill</span> <span class="o">=</span> <span class="i">I</span>(<span class="s">&quot;</span><span class="s">#</span><span class="s">3366</span><span class="s">FFAF</span><span class="s">&quot;</span>),
                        <span class="i">main</span> <span class="o">=</span> <span class="s">&quot;</span><span class="s">&quot;</span>, 
                        <span class="i">xlab</span> <span class="o">=</span> <span class="s">&quot;</span><span class="s">Duration</span><span class="s">,</span><span class="s"> </span><span class="s">min</span><span class="s">&quot;</span>, 
                        <span class="i">ylab</span> <span class="o">=</span> <span class="s">&quot;</span><span class="s">Temperature</span><span class="s">,</span><span class="s"> </span><span class="s">C</span><span class="s">&quot;</span>)

<span class="i">multiplot</span>(<span class="i">meanTempPlot</span>, <span class="i">meanTempBoxplot</span>)</pre>
</td>
</tr>
</table>

![Mean Temperature]({{ site.url }}/images/sechselaeuten/mean_temperature.png)
{: .image-center}

It's hard not to notice that there's no dependency between the mean temperature and duration. However, there're some outliers (the red dots): for example, the point in the top-left with the highest temperature is too far from the clustered group. Ironically, that was the most "accurate" prediction, in the year 2003, when it took less than 6 min for the head to explode and the summer was very warm and sunny.  
  
The next two "best" years are in the rows with 20+ min. Even though in 2013 the weather was not that bad despite of 35 min for burning. The data in the table below is sorted by mean temperatures:

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l">1: </span>
</pre>
</td>
<td class="snippet"><pre class="fssnip">
<span class="i">statsByTG</span> <span class="o">=</span> <span class="i">stats</span>[<span class="i">order</span>(<span class="i">stats</span><span class="o">$</span><span class="i">TG</span>),]  </pre>
</td>
</tr>
</table>


| year | time	| avg T	| min T	| max T	|precipitation|sunshine|humidity|cloud cover|
|      | (min)  | (C)   | (C)   | (C)   |(mm)         |(hrs)   |        |(oktas)    |
|------|--------|-------|-------|-------|-------------|--------|--------|-----------|  
| 1956 | 4.00	| 15.07	| 11.12	| 21.13	| 5.70	      |  5.64  | 0.74   | 5.66	    |
| 1978 | 12.00	| 15.38	| 11.42	| 20.27	| 4.52	      |  5.53  | 0.73   | 5.02	    |
| 1980 | 17.00	| 15.63	| 12.10	| 20.24	| 3.91	      |  4.69  | 0.75   | 5.30	    |
| 1972 | 8.00	| 15.77	| 11.86	| 20.70	| 4.84	      |  6.28  | 0.78   | 5.36	    |
| 1965 | 20.00	| 15.84	| 11.77	| 22.21	| 5.17	      |  6.15  | 0.77   | 5.32	    |
| 1966 | 16.00	| 15.84	| 11.62	| 22.12	| 5.24	      |  6.36  | 0.74   | 5.21	    |
| 1968 | 5.00	| 16.03	| 11.72	| 22.48	| 3.91	      |  6.94  | 0.71   | 5.20	    |
| ...  |		| 		|      	|      	|     	      |	       |		|           |
| 2013 | 35.18	| 18.53	| NA   	| NA   	| NA  	      |  8.14  | 0.70   | 4.21	    |
| 1952 | 6.00	| 18.75	| 13.68	| 25.73	| 3.05	      |  8.79  | 0.65   | 4.14	    |
| 1950 | 50.00	| 18.76	| 13.57	| 25.87	| 3.43	      |  8.72  | 0.65   | 4.08	    |
| 1983 | 24.33	| 19.06	| 14.15	| 24.63	| 1.72	      |  6.77  | 0.67   | 4.02	    |
| 1994 | 21.92	| 19.15	| 14.36	| 24.77	| 2.77	      |  7.09  | 0.72   | 4.34	    |
| 2003 | 5.70	| 21.66	| 16.53	| 28.02	| 2.29	      |  9.20  | 0.63   | 3.68	    |

<p>&nbsp;</p>

As we see, the weather tends to be average - very dry, or rainy, or hot summers are exceptions. If you try the random forests (regression) with this data, _% Var explained_ is negative.

![Weather]({{ site.url }}/images/sechselaeuten/weather.png)
{: .image-center}

One can argue, that for a number of reasons (the quality of weather measures, different structure of the Böögg itself or anything else) the years 1950 and 2014 are not comparable. I tried to run the same experiment with the data for the last 15 years, removing outliers and adding the labels for bad/ok/nice weather to make it a classification problem instead of regression - but the main conclusion is the same. Take a look at the historical averages for each of the summer days (the dark-blue line): the normal range (steel-blue) is quite narrow, even though the lowest and highest points (light-blue) may vary a lot[^2]:

![Historical Data]({{ site.url }}/images/sechselaeuten/historical.png)
{: .image-center}

#### Expectations

The results are pretty reassuring: we can be optimistic and hope that this summer will be great, or at least average - which is also quite nice. The weather is not an easy thing to predict months in advance!

<p>&nbsp;</p>
<p>&nbsp;</p>


[^1]: I realized that I forgot to publish this post on time (the date of event) only several days ago, then I decided to convert the data processing part and use Deedle... Maybe that's because of sunny Milan, but it took some time to figure out which functions to use for relatively simple groupings and aggregations.  

[^2]: To see how you can create similar kinds of plots check [this post](http://rpubs.com/bradleyboehmke/weather_graphic).  
