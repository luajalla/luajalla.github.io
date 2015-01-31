---
layout: post
status: publish
published: true
title: Log(music) with Undertone
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 2292
wordpress_url: http://luajalla.azurewebsites.net/?p=2292
date: '2013-12-26 23:55:27 +0100'
date_gmt: '2013-12-26 23:55:27 +0100'
image:
  feature: header5.jpg
  credit: Basel, Rhein
tags:
- fsharp
- math
- R
- music
- undertone
- matlab
comments: []
---

Whether you're a physicist, biologist, economist, data scientist, whatever,  the chances are you'll meet the Markov chains sooner or later. And today we'll try them on... music!  

![Christmas]({{ site.url }}/images/christmas-musical.jpg)
{: .image-right}

If you're not up to the math part, feel free to [skip it](#music).

*[Originally posted [here](http://luajalla.azurewebsites.net/log-music-with-undertone/)].* 


<h2 id="math">Math</h2>  

The system states changes are represented with a help of transition matrix, describing the probabilities of possible transitions. This matrix is a key to determine the state of the model after n steps.  

The math behind that is very straightforward - we need just a matrix power. Oh, wait - what if it is fractional, for example, when representing time? There're many ways to compute it, let's take a look at some of them.  

#### 1. Eigen vectors  

This method is based on properties of eigen values decomposition:  

$$
\begin{array}{1}
\text{V - eigen vectors}\\
A * v_j = \lambda_j * v_j\\
A * V = V * D \text{, where } D = diag(\lambda_1, ..., \lambda_n)\\
A^n = V * (D ^ n) * V^{-1}
\end{array}
$$

That's actually how Matlab ````mpower```` function [looks like](http://www.mathworks.com/help/matlab/ref/mpower.html):  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">[V,D] </span><span class="o">=</span><span class="i"> eig(M)</span>
<span class="l"> 2: </span><span class="i">An </span><span class="o">=</span><span class="i"> V </span><span class="o">*</span> (D <span class="o">.^</span><span class="i"> n) </span><span class="o">*</span><span class="i"> inv(V) </span></pre>  

We can take advantage of Math.NET matrix decompositions to implement this method:  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span id="t1" onmouseout="hideTip(event, '1', 1)" onmouseover="showTip(event, '1', 1, document.getElementById('t1'))" class="omitted">open namespaces, define constants</span>

<span class="c">/// Matrix power: eigen values decomposition method</span>
<span class="c">/// M^p = V * (D .^ p) / V</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '2', 2)" onmouseover="showTip(event, '2', 2)" class="i">mpowEig</span> (<span onmouseout="hideTip(event, '3', 3)" onmouseover="showTip(event, '3', 3)" class="i">m</span><span class="o">:</span> <span onmouseout="hideTip(event, '4', 4)" onmouseover="showTip(event, '4', 4)" class="i">SparseMatrix</span>) <span onmouseout="hideTip(event, '5', 5)" onmouseover="showTip(event, '5', 5)" class="i">p</span> <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '6', 6)" onmouseover="showTip(event, '6', 6)" class="i">evd</span> <span class="o">=</span> <span onmouseout="hideTip(event, '3', 7)" onmouseover="showTip(event, '3', 7)" class="i">m</span><span class="o">.</span><span onmouseout="hideTip(event, '7', 8)" onmouseover="showTip(event, '7', 8)" class="i">Evd</span>()
    <span class="k">let</span> <span onmouseout="hideTip(event, '8', 9)" onmouseover="showTip(event, '8', 9)" class="i">v</span>, <span onmouseout="hideTip(event, '9', 10)" onmouseover="showTip(event, '9', 10)" class="i">d</span> <span class="o">=</span> <span onmouseout="hideTip(event, '6', 11)" onmouseover="showTip(event, '6', 11)" class="i">evd</span><span class="o">.</span><span onmouseout="hideTip(event, '10', 12)" onmouseover="showTip(event, '10', 12)" class="i">EigenVectors</span>(), <span onmouseout="hideTip(event, '6', 13)" onmouseover="showTip(event, '6', 13)" class="i">evd</span><span class="o">.</span><span onmouseout="hideTip(event, '11', 14)" onmouseover="showTip(event, '11', 14)" class="i">D</span>()
    <span onmouseout="hideTip(event, '12', 15)" onmouseover="showTip(event, '12', 15)" class="i">mapInPlace</span> (<span class="k">fun</span> <span onmouseout="hideTip(event, '13', 16)" onmouseover="showTip(event, '13', 16)" class="i">x</span> <span class="k">-&gt;</span> <span onmouseout="hideTip(event, '13', 17)" onmouseover="showTip(event, '13', 17)" class="i">x</span> <span class="o">**</span> <span onmouseout="hideTip(event, '5', 18)" onmouseover="showTip(event, '5', 18)" class="i">p</span>) <span onmouseout="hideTip(event, '9', 19)" onmouseover="showTip(event, '9', 19)" class="i">d</span>
    <span onmouseout="hideTip(event, '8', 20)" onmouseover="showTip(event, '8', 20)" class="i">v</span> <span class="o">*</span> <span onmouseout="hideTip(event, '9', 21)" onmouseover="showTip(event, '9', 21)" class="i">d</span> <span class="o">*</span> <span onmouseout="hideTip(event, '8', 22)" onmouseover="showTip(event, '8', 22)" class="i">v</span><span class="o">.</span><span onmouseout="hideTip(event, '14', 23)" onmouseover="showTip(event, '14', 23)" class="i">Inverse</span>()
</pre></td></tr></table>  

What are the pros/cons?  

**+** the method is rather efficient  
**-** inverse matrix $$V$$ might not exist [^1]  

$$A = \begin{bmatrix} 0.78 & 0.2 & 0.12\\ 0 & 0.78 & 0.32\\0 & 0 & 0.78 \end{bmatrix}$$  

$$V = \begin{bmatrix} 1.0000 & -1.0000\\ 0.0000 & 0.0000 \end{bmatrix}$$  

**-** some matrices cannot be diagonalised:  

$$A = \begin{bmatrix} 3 & 2\\0 & 3 \end{bmatrix}$$  

#### 2. Generator matrices  

If we can find a matrix $$G$$, such as $$A = exp(G)$$, then (remember series?)  

$$A^x = exp(x*G) = I + (x*G) + (x*G)^2/2! + (x*G)^3/3! + ...$$  

We are interested in finding a generator $$G$$ with zero row sums and nonnegative off-diagonal entries. So we'll use Mercator's series:  

$$log(1+x) = x - x^2/2 + x^3/3 - x^4/4 + ...$$  

In matrix case it's  

$$G = log(I + (A-I)) = (A-I) - (A-I)^2/2 + (A-I)^3/3 - ...$$  

The common concern is that the series methods are not very efficient and may be not accurate enough. It's easy to compare the methods by computing the squared error between the actual and expected values (say, compute $$(A^{1/3})^3$$).  
 
Another problem is that a generator matrix (and power too) might have negative elements - not really what you want when dealing with probabilities. We can make them non-negative and keep the rows sums equal to zero... but the power properties won't be preserved. For example, you can find out that $$A^7 <> A^{3.5} * A^{3.5}$$.

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
</pre></td><td class="snippets"><pre class="fssnip">
<span class="c">/// generator matrix</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '15', 24)" onmouseover="showTip(event, '15', 24)" class="i">genm</span> (<span onmouseout="hideTip(event, '16', 25)" onmouseover="showTip(event, '16', 25)" class="i">a</span><span class="o">:</span> <span onmouseout="hideTip(event, '4', 26)" onmouseover="showTip(event, '4', 26)" class="i">SparseMatrix</span>) <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '17', 27)" onmouseover="showTip(event, '17', 27)" class="i">size</span> <span class="o">=</span> <span onmouseout="hideTip(event, '16', 28)" onmouseover="showTip(event, '16', 28)" class="i">a</span><span class="o">.</span><span onmouseout="hideTip(event, '18', 29)" onmouseover="showTip(event, '18', 29)" class="i">RowCount</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '19', 30)" onmouseover="showTip(event, '19', 30)" class="i">mid</span> <span class="o">=</span> <span onmouseout="hideTip(event, '20', 31)" onmouseover="showTip(event, '20', 31)" class="i">constDiag</span> <span onmouseout="hideTip(event, '17', 32)" onmouseover="showTip(event, '17', 32)" class="i">size</span> <span class="n">1.0</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '21', 33)" onmouseover="showTip(event, '21', 33)" class="i">a_i</span> <span class="o">=</span> <span onmouseout="hideTip(event, '16', 34)" onmouseover="showTip(event, '16', 34)" class="i">a</span> <span class="o">-</span> <span onmouseout="hideTip(event, '19', 35)" onmouseover="showTip(event, '19', 35)" class="i">mid</span>

    <span class="k">let</span> <span class="k">rec</span> <span onmouseout="hideTip(event, '22', 36)" onmouseover="showTip(event, '22', 36)" class="i">logm</span> (<span onmouseout="hideTip(event, '23', 37)" onmouseover="showTip(event, '23', 37)" class="i">i</span>, <span onmouseout="hideTip(event, '24', 38)" onmouseover="showTip(event, '24', 38)" class="i">qpow</span><span class="o">:</span> <span onmouseout="hideTip(event, '4', 39)" onmouseover="showTip(event, '4', 39)" class="i">SparseMatrix</span>) <span onmouseout="hideTip(event, '25', 40)" onmouseover="showTip(event, '25', 40)" class="i">res</span> <span class="o">=</span>
        <span class="k">if</span> <span onmouseout="hideTip(event, '23', 41)" onmouseover="showTip(event, '23', 41)" class="i">i</span> <span class="o">=</span> <span onmouseout="hideTip(event, '26', 42)" onmouseover="showTip(event, '26', 42)" class="i">MAX_ITER</span> <span class="k">then</span> <span onmouseout="hideTip(event, '25', 43)" onmouseover="showTip(event, '25', 43)" class="i">res</span>
        <span class="k">else</span>
           <span class="k">let</span> <span onmouseout="hideTip(event, '27', 44)" onmouseover="showTip(event, '27', 44)" class="i">qdiv</span> <span class="o">=</span> <span onmouseout="hideTip(event, '24', 45)" onmouseover="showTip(event, '24', 45)" class="i">qpow</span><span class="o">.</span><span onmouseout="hideTip(event, '28', 46)" onmouseover="showTip(event, '28', 46)" class="i">Divide</span> (<span onmouseout="hideTip(event, '29', 47)" onmouseover="showTip(event, '29', 47)" class="i">float</span> <span onmouseout="hideTip(event, '23', 48)" onmouseover="showTip(event, '23', 48)" class="i">i</span>)
           <span class="k">if</span> <span onmouseout="hideTip(event, '30', 49)" onmouseover="showTip(event, '30', 49)" class="i">sumSq</span> <span onmouseout="hideTip(event, '27', 50)" onmouseover="showTip(event, '27', 50)" class="i">qdiv</span> <span class="o">&lt;</span> <span onmouseout="hideTip(event, '31', 51)" onmouseover="showTip(event, '31', 51)" class="i">PREC</span> <span class="k">then</span> <span onmouseout="hideTip(event, '25', 52)" onmouseover="showTip(event, '25', 52)" class="i">res</span> <span class="o">+</span> <span onmouseout="hideTip(event, '27', 53)" onmouseover="showTip(event, '27', 53)" class="i">qdiv</span>
           <span class="k">else</span> 
               <span onmouseout="hideTip(event, '22', 54)" onmouseover="showTip(event, '22', 54)" class="i">logm</span> (<span onmouseout="hideTip(event, '23', 55)" onmouseover="showTip(event, '23', 55)" class="i">i</span><span class="o">+</span><span class="n">1</span>, <span class="o">-</span><span onmouseout="hideTip(event, '24', 56)" onmouseover="showTip(event, '24', 56)" class="i">qpow</span> <span class="o">*</span> <span onmouseout="hideTip(event, '21', 57)" onmouseover="showTip(event, '21', 57)" class="i">a_i</span>) (<span onmouseout="hideTip(event, '25', 58)" onmouseover="showTip(event, '25', 58)" class="i">res</span> <span class="o">+</span> <span onmouseout="hideTip(event, '27', 59)" onmouseover="showTip(event, '27', 59)" class="i">qdiv</span>)
    <span class="c">// find log(A - I)</span>
    <span class="c">// update negative off-diagonal entries, the row-sum = 0</span>
    <span onmouseout="hideTip(event, '22', 60)" onmouseover="showTip(event, '22', 60)" class="i">logm</span> (<span class="n">1</span>, <span onmouseout="hideTip(event, '21', 61)" onmouseover="showTip(event, '21', 61)" class="i">a_i</span>) (<span onmouseout="hideTip(event, '32', 62)" onmouseover="showTip(event, '32', 62)" class="i">zeroCreate</span> <span onmouseout="hideTip(event, '17', 63)" onmouseover="showTip(event, '17', 63)" class="i">size</span> <span onmouseout="hideTip(event, '17', 64)" onmouseover="showTip(event, '17', 64)" class="i">size</span>) <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '33', 65)" onmouseover="showTip(event, '33', 65)" class="i">updateneg</span>

<span class="c">/// exp series approximation</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '34', 66)" onmouseover="showTip(event, '34', 66)" class="i">expSeries</span> (<span onmouseout="hideTip(event, '3', 67)" onmouseover="showTip(event, '3', 67)" class="i">m</span><span class="o">:</span> <span onmouseout="hideTip(event, '4', 68)" onmouseover="showTip(event, '4', 68)" class="i">SparseMatrix</span>)  <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '17', 69)" onmouseover="showTip(event, '17', 69)" class="i">size</span> <span class="o">=</span> <span onmouseout="hideTip(event, '3', 70)" onmouseover="showTip(event, '3', 70)" class="i">m</span><span class="o">.</span><span onmouseout="hideTip(event, '18', 71)" onmouseover="showTip(event, '18', 71)" class="i">RowCount</span>
    
    <span class="k">let</span> <span class="k">rec</span> <span onmouseout="hideTip(event, '35', 72)" onmouseover="showTip(event, '35', 72)" class="i">sum</span> (<span onmouseout="hideTip(event, '23', 73)" onmouseover="showTip(event, '23', 73)" class="i">i</span>, <span onmouseout="hideTip(event, '36', 74)" onmouseover="showTip(event, '36', 74)" class="i">k</span>, <span onmouseout="hideTip(event, '37', 75)" onmouseover="showTip(event, '37', 75)" class="i">mpow</span><span class="o">:</span> <span onmouseout="hideTip(event, '4', 76)" onmouseover="showTip(event, '4', 76)" class="i">SparseMatrix</span>) <span onmouseout="hideTip(event, '25', 77)" onmouseover="showTip(event, '25', 77)" class="i">res</span> <span class="o">=</span>
        <span class="k">if</span> <span onmouseout="hideTip(event, '23', 78)" onmouseover="showTip(event, '23', 78)" class="i">i</span> <span class="o">=</span> <span onmouseout="hideTip(event, '26', 79)" onmouseover="showTip(event, '26', 79)" class="i">MAX_ITER</span> <span class="k">then</span> <span onmouseout="hideTip(event, '25', 80)" onmouseover="showTip(event, '25', 80)" class="i">res</span>
        <span class="k">else</span>
            <span class="k">let</span> <span onmouseout="hideTip(event, '38', 81)" onmouseover="showTip(event, '38', 81)" class="i">mpowi</span>, <span onmouseout="hideTip(event, '39', 82)" onmouseover="showTip(event, '39', 82)" class="i">ki</span> <span class="o">=</span> <span onmouseout="hideTip(event, '37', 83)" onmouseover="showTip(event, '37', 83)" class="i">mpow</span> <span class="o">*</span> <span onmouseout="hideTip(event, '3', 84)" onmouseover="showTip(event, '3', 84)" class="i">m</span>, <span onmouseout="hideTip(event, '36', 85)" onmouseover="showTip(event, '36', 85)" class="i">k</span> <span class="o">*</span> <span onmouseout="hideTip(event, '29', 86)" onmouseover="showTip(event, '29', 86)" class="i">float</span> <span onmouseout="hideTip(event, '23', 87)" onmouseover="showTip(event, '23', 87)" class="i">i</span>
            <span class="k">let</span> <span onmouseout="hideTip(event, '40', 88)" onmouseover="showTip(event, '40', 88)" class="i">mdiv</span> <span class="o">=</span> <span onmouseout="hideTip(event, '38', 89)" onmouseover="showTip(event, '38', 89)" class="i">mpowi</span><span class="o">.</span><span onmouseout="hideTip(event, '28', 90)" onmouseover="showTip(event, '28', 90)" class="i">Divide</span> <span onmouseout="hideTip(event, '39', 91)" onmouseover="showTip(event, '39', 91)" class="i">ki</span>
            <span class="k">if</span> <span onmouseout="hideTip(event, '30', 92)" onmouseover="showTip(event, '30', 92)" class="i">sumSq</span> <span onmouseout="hideTip(event, '40', 93)" onmouseover="showTip(event, '40', 93)" class="i">mdiv</span> <span class="o">&lt;</span> <span onmouseout="hideTip(event, '31', 94)" onmouseover="showTip(event, '31', 94)" class="i">PREC</span> <span class="k">then</span> <span onmouseout="hideTip(event, '25', 95)" onmouseover="showTip(event, '25', 95)" class="i">res</span> <span class="o">+</span> <span onmouseout="hideTip(event, '40', 96)" onmouseover="showTip(event, '40', 96)" class="i">mdiv</span>
            <span class="k">else</span>
                <span onmouseout="hideTip(event, '35', 97)" onmouseover="showTip(event, '35', 97)" class="i">sum</span> (<span onmouseout="hideTip(event, '23', 98)" onmouseover="showTip(event, '23', 98)" class="i">i</span><span class="o">+</span><span class="n">1</span>, <span onmouseout="hideTip(event, '39', 99)" onmouseover="showTip(event, '39', 99)" class="i">ki</span>, <span onmouseout="hideTip(event, '38', 100)" onmouseover="showTip(event, '38', 100)" class="i">mpowi</span>) (<span onmouseout="hideTip(event, '25', 101)" onmouseover="showTip(event, '25', 101)" class="i">res</span> <span class="o">+</span> <span onmouseout="hideTip(event, '40', 102)" onmouseover="showTip(event, '40', 102)" class="i">mdiv</span>) 

    <span class="k">let</span> <span onmouseout="hideTip(event, '19', 103)" onmouseover="showTip(event, '19', 103)" class="i">mid</span> <span class="o">=</span> <span onmouseout="hideTip(event, '20', 104)" onmouseover="showTip(event, '20', 104)" class="i">constDiag</span> <span onmouseout="hideTip(event, '17', 105)" onmouseover="showTip(event, '17', 105)" class="i">size</span> <span class="n">1.0</span>
    <span onmouseout="hideTip(event, '35', 106)" onmouseover="showTip(event, '35', 106)" class="i">sum</span> (<span class="n">1</span>, <span class="n">1.0</span>, <span onmouseout="hideTip(event, '19', 107)" onmouseover="showTip(event, '19', 107)" class="i">mid</span>) <span onmouseout="hideTip(event, '19', 108)" onmouseover="showTip(event, '19', 108)" class="i">mid</span>

<span class="c">/// Matrix power: series method</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '41', 109)" onmouseover="showTip(event, '41', 109)" class="i">mpowSeries</span> <span onmouseout="hideTip(event, '3', 110)" onmouseover="showTip(event, '3', 110)" class="i">m</span> (<span onmouseout="hideTip(event, '5', 111)" onmouseover="showTip(event, '5', 111)" class="i">p</span><span class="o">:</span> <span onmouseout="hideTip(event, '29', 112)" onmouseover="showTip(event, '29', 112)" class="i">float</span>) <span class="o">=</span> <span onmouseout="hideTip(event, '4', 113)" onmouseover="showTip(event, '4', 113)" class="i">SparseMatrix</span><span class="o">.</span><span onmouseout="hideTip(event, '42', 114)" onmouseover="showTip(event, '42', 114)" class="i">OfMatrix</span> (<span onmouseout="hideTip(event, '15', 115)" onmouseover="showTip(event, '15', 115)" class="i">genm</span> <span onmouseout="hideTip(event, '3', 116)" onmouseover="showTip(event, '3', 116)" class="i">m</span> <span class="o">*</span> <span onmouseout="hideTip(event, '5', 117)" onmouseover="showTip(event, '5', 117)" class="i">p</span>) <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '34', 118)" onmouseover="showTip(event, '34', 118)" class="i">expSeries</span></pre></td></tr></table>  

**+** Works for non-diagonizable matrices  
**-** Not very efficient  
**-** May be not accurate enough or even fail to converge  

These are just the most simple algorithms - would be interesting to check the others, but let's move on.  

#### References  
1. <a href="http://www.cs.cornell.edu/cv/researchpdf/19ways+.pdf">19 Dubious Ways to Compute the Exponential of a Matrix</a>  
2. <a href="http://www.math.ubc.ca/~israel/irw/">Finding Generators for Markov Chains via empirical transition matrices, with applications to credit ratings</a>  

<h2 id="music">Music</h2>

It's not a secret, that Markov models are often used for generative music, here's one of the examples: <a href="http://vikparuchuri.com/blog/making-instrumental-music-from-scratch/">Programming Instrumental Music From Scratch</a> (there're also some interesting references in the comments). We'll do a different thing, though: given notes, we can calculate the transition probabilities and... generate new melodies.  

But everything in its time.  

We need to somehow define the sequences of notes and play them. And thanks to <a href="https://github.com/robertpi/Undertone">Undertone</a> that's pretty easy! First of all, let's download the piano samples - University of Iowa has a collection of recordings <a href="http://theremin.music.uiowa.edu/MISpiano.html">here</a>. You can get them manually or using <a href="https://github.com/robertpi/Undertone/blob/master/src/Undertone/DownloadsSamples.fsx">this script</a>. It's also ok to generate the notes programmatically - but somewhat a bit more realistic is more fun ^_^  

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
<span class="l">40: </span>
<span class="l">41: </span>
<span class="l">42: </span>
<span class="l">43: </span>
<span class="l">44: </span>
<span class="l">45: </span>
<span class="l">46: </span>
<span class="l">47: </span>
<span class="l">48: </span>
<span class="l">49: </span>
<span class="l">50: </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span id="t43" onmouseout="hideTip(event, '43', 119)" onmouseover="showTip(event, '43', 119, document.getElementById('t43'))" class="omitted">open namespaces</span>

<span class="c">// Size: 2/4</span>

<span class="c">/// 1/16 (semiquaver)</span>
[&lt;<span onmouseout="hideTip(event, '44', 120)" onmouseover="showTip(event, '44', 120)" class="i">Literal</span>&gt;] 
<span class="k">let</span> <span onmouseout="hideTip(event, '45', 121)" onmouseover="showTip(event, '45', 121)" class="i">Sq</span> <span class="o">=</span> <span class="n">0.0625</span>
<span class="c">/// 1/8 (quaver)</span>
[&lt;<span onmouseout="hideTip(event, '44', 122)" onmouseover="showTip(event, '44', 122)" class="i">Literal</span>&gt;] 
<span class="k">let</span> <span onmouseout="hideTip(event, '46', 123)" onmouseover="showTip(event, '46', 123)" class="i">Q1</span> <span class="o">=</span> <span class="n">0.125</span>
<span class="c">/// 3/8</span>
[&lt;<span onmouseout="hideTip(event, '44', 124)" onmouseover="showTip(event, '44', 124)" class="i">Literal</span>&gt;] 
<span class="k">let</span> <span onmouseout="hideTip(event, '47', 125)" onmouseover="showTip(event, '47', 125)" class="i">Q3</span> <span class="o">=</span> <span class="n">0.375</span>

<span id="t48" onmouseout="hideTip(event, '48', 126)" onmouseover="showTip(event, '48', 126, document.getElementById('t48'))" class="omitted">...</span>

<span class="c">/// Mapping between note enum and file name convention </span>
<span class="k">let</span> <span onmouseout="hideTip(event, '49', 127)" onmouseover="showTip(event, '49', 127)" class="i">noteToPianoName</span> <span onmouseout="hideTip(event, '50', 128)" onmouseover="showTip(event, '50', 128)" class="i">note</span> <span class="o">=</span>
    <span class="k">match</span> <span onmouseout="hideTip(event, '50', 129)" onmouseover="showTip(event, '50', 129)" class="i">note</span> <span class="k">with</span>
    | <span onmouseout="hideTip(event, '51', 130)" onmouseover="showTip(event, '51', 130)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '52', 131)" onmouseover="showTip(event, '52', 131)" class="i">C</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">C</span><span class="s">&quot;</span> 
    | <span onmouseout="hideTip(event, '51', 132)" onmouseover="showTip(event, '51', 132)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '53', 133)" onmouseover="showTip(event, '53', 133)" class="i">Csharp</span>    <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">Db</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 134)" onmouseover="showTip(event, '51', 134)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '54', 135)" onmouseover="showTip(event, '54', 135)" class="i">D</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">D</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 136)" onmouseover="showTip(event, '51', 136)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '55', 137)" onmouseover="showTip(event, '55', 137)" class="i">Dsharp</span>    <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">Eb</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 138)" onmouseover="showTip(event, '51', 138)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '56', 139)" onmouseover="showTip(event, '56', 139)" class="i">E</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">E</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 140)" onmouseover="showTip(event, '51', 140)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '57', 141)" onmouseover="showTip(event, '57', 141)" class="i">F</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">F</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 142)" onmouseover="showTip(event, '51', 142)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '58', 143)" onmouseover="showTip(event, '58', 143)" class="i">Fsharp</span>    <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">Gb</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 144)" onmouseover="showTip(event, '51', 144)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '59', 145)" onmouseover="showTip(event, '59', 145)" class="i">G</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">G</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 146)" onmouseover="showTip(event, '51', 146)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '60', 147)" onmouseover="showTip(event, '60', 147)" class="i">Gsharp</span>    <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">Ab</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 148)" onmouseover="showTip(event, '51', 148)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '61', 149)" onmouseover="showTip(event, '61', 149)" class="i">A</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">A</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 150)" onmouseover="showTip(event, '51', 150)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '62', 151)" onmouseover="showTip(event, '62', 151)" class="i">Asharp</span>    <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">Bb</span><span class="s">&quot;</span>
    | <span onmouseout="hideTip(event, '51', 152)" onmouseover="showTip(event, '51', 152)" class="i">Note</span><span class="o">.</span><span onmouseout="hideTip(event, '63', 153)" onmouseover="showTip(event, '63', 153)" class="i">B</span>         <span class="k">-&gt;</span> <span class="s">&quot;</span><span class="s">B</span><span class="s">&quot;</span>
    | _ <span class="k">-&gt;</span> <span onmouseout="hideTip(event, '64', 154)" onmouseover="showTip(event, '64', 154)" class="i">failwith</span> <span class="s">&quot;</span><span class="s">invalid</span><span class="s"> </span><span class="s">note</span><span class="s">&quot;</span>

<span class="c">/// Read piano note from .aiff file</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '65', 155)" onmouseover="showTip(event, '65', 155)" class="i">readPianoNote</span>() <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '66', 156)" onmouseover="showTip(event, '66', 156)" class="i">cache</span> <span class="o">=</span> <span onmouseout="hideTip(event, '67', 157)" onmouseover="showTip(event, '67', 157)" class="i">Dictionary</span><span class="o">&lt;</span>_,_<span class="o">&gt;</span> <span onmouseout="hideTip(event, '68', 158)" onmouseover="showTip(event, '68', 158)" class="i">HashIdentity</span><span class="o">.</span><span onmouseout="hideTip(event, '69', 159)" onmouseover="showTip(event, '69', 159)" class="i">Structural</span>
    <span class="k">let</span> <span class="k">inline</span> <span onmouseout="hideTip(event, '70', 160)" onmouseover="showTip(event, '70', 160)" class="i">path</span> (<span onmouseout="hideTip(event, '71', 161)" onmouseover="showTip(event, '71', 161)" class="i">noteName</span>, <span onmouseout="hideTip(event, '72', 162)" onmouseover="showTip(event, '72', 162)" class="i">octave</span>) <span class="o">=</span> 
        <span onmouseout="hideTip(event, '73', 163)" onmouseover="showTip(event, '73', 163)" class="i">Path</span><span class="o">.</span><span onmouseout="hideTip(event, '74', 164)" onmouseover="showTip(event, '74', 164)" class="i">Combine</span>(<span onmouseout="hideTip(event, '75', 165)" onmouseover="showTip(event, '75', 165)" class="i">dir</span>, <span class="s">&quot;</span><span class="s">Piano</span><span class="s">.</span><span class="s">ff</span><span class="s">.</span><span class="s">&quot;</span> <span class="o">+</span> <span onmouseout="hideTip(event, '71', 166)" onmouseover="showTip(event, '71', 166)" class="i">noteName</span> <span class="o">+</span> <span onmouseout="hideTip(event, '76', 167)" onmouseover="showTip(event, '76', 167)" class="i">string</span> <span onmouseout="hideTip(event, '72', 168)" onmouseover="showTip(event, '72', 168)" class="i">octave</span> <span class="o">+</span> <span class="s">&quot;</span><span class="s">.</span><span class="s">aiff</span><span class="s">&quot;</span>)

    <span class="k">fun</span> (<span onmouseout="hideTip(event, '50', 169)" onmouseover="showTip(event, '50', 169)" class="i">note</span>, <span onmouseout="hideTip(event, '77', 170)" onmouseover="showTip(event, '77', 170)" class="i">octave</span>) <span class="k">-&gt;</span>
        <span class="k">let</span> <span onmouseout="hideTip(event, '78', 171)" onmouseover="showTip(event, '78', 171)" class="i">noteKey</span> <span class="o">=</span> <span onmouseout="hideTip(event, '49', 172)" onmouseover="showTip(event, '49', 172)" class="i">noteToPianoName</span> <span onmouseout="hideTip(event, '50', 173)" onmouseover="showTip(event, '50', 173)" class="i">note</span>, <span onmouseout="hideTip(event, '77', 174)" onmouseover="showTip(event, '77', 174)" class="i">octave</span>
        <span class="k">match</span> <span onmouseout="hideTip(event, '66', 175)" onmouseover="showTip(event, '66', 175)" class="i">cache</span><span class="o">.</span><span onmouseout="hideTip(event, '79', 176)" onmouseover="showTip(event, '79', 176)" class="i">TryGetValue</span> <span onmouseout="hideTip(event, '78', 177)" onmouseover="showTip(event, '78', 177)" class="i">noteKey</span> <span class="k">with</span>
        | <span class="k">true</span>, <span onmouseout="hideTip(event, '80', 178)" onmouseover="showTip(event, '80', 178)" class="i">wave</span> <span class="k">-&gt;</span> <span onmouseout="hideTip(event, '80', 179)" onmouseover="showTip(event, '80', 179)" class="i">wave</span>
        | _ <span class="k">-&gt;</span> 
            <span class="k">let</span> <span onmouseout="hideTip(event, '80', 180)" onmouseover="showTip(event, '80', 180)" class="i">wave</span> <span class="o">=</span> <span onmouseout="hideTip(event, '81', 181)" onmouseover="showTip(event, '81', 181)" class="i">IO</span><span class="o">.</span><span onmouseout="hideTip(event, '82', 182)" onmouseover="showTip(event, '82', 182)" class="i">read</span> (<span onmouseout="hideTip(event, '70', 183)" onmouseover="showTip(event, '70', 183)" class="i">path</span> <span onmouseout="hideTip(event, '78', 184)" onmouseover="showTip(event, '78', 184)" class="i">noteKey</span>) <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '83', 185)" onmouseover="showTip(event, '83', 185)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '84', 186)" onmouseover="showTip(event, '84', 186)" class="i">toArray</span>
            <span onmouseout="hideTip(event, '66', 187)" onmouseover="showTip(event, '66', 187)" class="i">cache</span><span class="o">.</span><span onmouseout="hideTip(event, '85', 188)" onmouseover="showTip(event, '85', 188)" class="i">Add</span>(<span onmouseout="hideTip(event, '78', 189)" onmouseover="showTip(event, '78', 189)" class="i">noteKey</span>, <span onmouseout="hideTip(event, '80', 190)" onmouseover="showTip(event, '80', 190)" class="i">wave</span>)
            <span onmouseout="hideTip(event, '80', 191)" onmouseover="showTip(event, '80', 191)" class="i">wave</span>

<span class="k">let</span> <span onmouseout="hideTip(event, '86', 192)" onmouseover="showTip(event, '86', 192)" class="i">makeNote</span> <span class="o">=</span> <span onmouseout="hideTip(event, '65', 193)" onmouseover="showTip(event, '65', 193)" class="i">readPianoNote</span>() 
<span class="k">let</span> <span onmouseout="hideTip(event, '87', 194)" onmouseover="showTip(event, '87', 194)" class="i">getMelody</span> <span class="o">=</span> <span onmouseout="hideTip(event, '83', 195)" onmouseover="showTip(event, '83', 195)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '88', 196)" onmouseover="showTip(event, '88', 196)" class="i">collect</span> (<span class="k">fun</span> (<span onmouseout="hideTip(event, '89', 197)" onmouseover="showTip(event, '89', 197)" class="i">noct</span>, <span onmouseout="hideTip(event, '90', 198)" onmouseover="showTip(event, '90', 198)" class="i">time</span>) <span class="k">-&gt;</span> <span onmouseout="hideTip(event, '86', 199)" onmouseover="showTip(event, '86', 199)" class="i">makeNote</span> <span onmouseout="hideTip(event, '89', 200)" onmouseover="showTip(event, '89', 200)" class="i">noct</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '83', 201)" onmouseover="showTip(event, '83', 201)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '91', 202)" onmouseover="showTip(event, '91', 202)" class="i">take</span> (<span onmouseout="hideTip(event, '92', 203)" onmouseover="showTip(event, '92', 203)" class="i">noteValue</span> <span onmouseout="hideTip(event, '90', 204)" onmouseover="showTip(event, '90', 204)" class="i">time</span>))
</pre></td></tr></table>  

The note is defined with its symbol (C = Do, F# = Fis = Fa-diesis = F sharp), octave and time (♪ - 1/8, ♬ - 2 notes * 1/16). The sequence of notes gives a melody, which can be played and even saved later. Can you guess the following one?  

[Original]({{ site.url }}/sounds/original.mp3)  

Sounds recognizable, but awkwardly stumbling - makes me feel *much* better about my piano skills. I'd recommend to watch [this performace](http://www.youtube.com/watch?v=7cFkae0j_Ns), it's amazing! (and that pianist-feeling suddenly goes down again)). I believe there's a way to make the generated sound smoother and so on, but that's not the goal now.  

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
</pre></td><td class="snippets"><pre class="fssnip">
<span id="t93" onmouseout="hideTip(event, '93', 205)" onmouseover="showTip(event, '93', 205, document.getElementById('t93'))" class="omitted">Define commonly used notes</span>

<span class="c">/// Sample melody</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '94', 206)" onmouseover="showTip(event, '94', 206)" class="i">ent</span> <span class="o">=</span> [
    <span onmouseout="hideTip(event, '95', 207)" onmouseover="showTip(event, '95', 207)" class="i">D3</span>,<span onmouseout="hideTip(event, '45', 208)" onmouseover="showTip(event, '45', 208)" class="i">Sq</span>; <span onmouseout="hideTip(event, '96', 209)" onmouseover="showTip(event, '96', 209)" class="i">Ds3</span>,<span onmouseout="hideTip(event, '45', 210)" onmouseover="showTip(event, '45', 210)" class="i">Sq</span>
    <span onmouseout="hideTip(event, '97', 211)" onmouseover="showTip(event, '97', 211)" class="i">E3</span>,<span onmouseout="hideTip(event, '45', 212)" onmouseover="showTip(event, '45', 212)" class="i">Sq</span>; <span onmouseout="hideTip(event, '98', 213)" onmouseover="showTip(event, '98', 213)" class="i">C4</span>,<span onmouseout="hideTip(event, '46', 214)" onmouseover="showTip(event, '46', 214)" class="i">Q1</span>; <span onmouseout="hideTip(event, '97', 215)" onmouseover="showTip(event, '97', 215)" class="i">E3</span>,<span onmouseout="hideTip(event, '45', 216)" onmouseover="showTip(event, '45', 216)" class="i">Sq</span>; <span onmouseout="hideTip(event, '98', 217)" onmouseover="showTip(event, '98', 217)" class="i">C4</span>,<span onmouseout="hideTip(event, '46', 218)" onmouseover="showTip(event, '46', 218)" class="i">Q1</span>; <span onmouseout="hideTip(event, '97', 219)" onmouseover="showTip(event, '97', 219)" class="i">E3</span>,<span onmouseout="hideTip(event, '45', 220)" onmouseover="showTip(event, '45', 220)" class="i">Sq</span>; <span onmouseout="hideTip(event, '98', 221)" onmouseover="showTip(event, '98', 221)" class="i">C4</span>,<span onmouseout="hideTip(event, '47', 222)" onmouseover="showTip(event, '47', 222)" class="i">Q3</span>
    <span onmouseout="hideTip(event, '98', 223)" onmouseover="showTip(event, '98', 223)" class="i">C4</span>,<span onmouseout="hideTip(event, '45', 224)" onmouseover="showTip(event, '45', 224)" class="i">Sq</span>; <span onmouseout="hideTip(event, '99', 225)" onmouseover="showTip(event, '99', 225)" class="i">D4</span>,<span onmouseout="hideTip(event, '45', 226)" onmouseover="showTip(event, '45', 226)" class="i">Sq</span>; <span onmouseout="hideTip(event, '100', 227)" onmouseover="showTip(event, '100', 227)" class="i">Ds4</span>,<span onmouseout="hideTip(event, '45', 228)" onmouseover="showTip(event, '45', 228)" class="i">Sq</span>
    <span onmouseout="hideTip(event, '101', 229)" onmouseover="showTip(event, '101', 229)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 230)" onmouseover="showTip(event, '45', 230)" class="i">Sq</span>; <span onmouseout="hideTip(event, '98', 231)" onmouseover="showTip(event, '98', 231)" class="i">C4</span>,<span onmouseout="hideTip(event, '45', 232)" onmouseover="showTip(event, '45', 232)" class="i">Sq</span>; <span onmouseout="hideTip(event, '99', 233)" onmouseover="showTip(event, '99', 233)" class="i">D4</span>,<span onmouseout="hideTip(event, '45', 234)" onmouseover="showTip(event, '45', 234)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 235)" onmouseover="showTip(event, '101', 235)" class="i">E4</span>,<span onmouseout="hideTip(event, '46', 236)" onmouseover="showTip(event, '46', 236)" class="i">Q1</span>; <span onmouseout="hideTip(event, '98', 237)" onmouseover="showTip(event, '98', 237)" class="i">C4</span>,<span onmouseout="hideTip(event, '45', 238)" onmouseover="showTip(event, '45', 238)" class="i">Sq</span>; <span onmouseout="hideTip(event, '99', 239)" onmouseover="showTip(event, '99', 239)" class="i">D4</span>,<span onmouseout="hideTip(event, '46', 240)" onmouseover="showTip(event, '46', 240)" class="i">Q1</span>
    <span onmouseout="hideTip(event, '98', 241)" onmouseover="showTip(event, '98', 241)" class="i">C4</span>,<span onmouseout="hideTip(event, '45', 242)" onmouseover="showTip(event, '45', 242)" class="i">Sq</span>; <span onmouseout="hideTip(event, '95', 243)" onmouseover="showTip(event, '95', 243)" class="i">D3</span>,<span onmouseout="hideTip(event, '45', 244)" onmouseover="showTip(event, '45', 244)" class="i">Sq</span>; <span onmouseout="hideTip(event, '96', 245)" onmouseover="showTip(event, '96', 245)" class="i">Ds3</span>,<span onmouseout="hideTip(event, '45', 246)" onmouseover="showTip(event, '45', 246)" class="i">Sq</span> <span id="t102" onmouseout="hideTip(event, '102', 247)" onmouseover="showTip(event, '102', 247, document.getElementById('t102'))" class="omitted">...</span>
    <span onmouseout="hideTip(event, '98', 248)" onmouseover="showTip(event, '98', 248)" class="i">C4</span>,<span onmouseout="hideTip(event, '47', 249)" onmouseover="showTip(event, '47', 249)" class="i">Q3</span>
]

<span onmouseout="hideTip(event, '103', 250)" onmouseover="showTip(event, '103', 250)" class="i">Player</span><span class="o">.</span><span onmouseout="hideTip(event, '104', 251)" onmouseover="showTip(event, '104', 251)" class="i">Play</span> (<span onmouseout="hideTip(event, '87', 252)" onmouseover="showTip(event, '87', 252)" class="i">getMelody</span> <span onmouseout="hideTip(event, '94', 253)" onmouseover="showTip(event, '94', 253)" class="i">ent</span>)</pre></td></tr></table>  

*(Note, the example above has 3 times faster tempo that the one you'll get with downloaded notes)*  

The next thing to do is to calculate the transition probabilities. Consider the following sequence: *E3 - 1/16, C4 - 1/8, E3 - 1/16, C4 - 3/8, C4 - 1/16*.  


The first option is to simply count transitions between notes and then normalize the rows, so the sum are equal to 1:  

$$
\begin{matrix} & E3 & C4 \\ E3 & 0 & 0 \\ C4 & 0 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 1 \\ C4 & 0 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 1 \\ C4 & 1 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 2 \\ C4 & 1 & 0 \end{matrix} \rightarrow$$  
$$
\begin{matrix} & E3 & C4 \\ E3 & 0 & 2 \\ C4 & 1 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 2 \\ C4 & 1 & 1 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 1 \\ C4 & 0.5 & 0.5 \end{matrix} $$  

But wait, what about time? There're different notes, we can take that into account too: if the 'smallest' length is 1/16, then we can count, how many such intervals each note takes, multiplying by 16[^2]:  

*E3 - 1/16, C4 - 1/8, E3 - 1/16, C4 - 3/8, C4 - 1/16*  

$$
\begin{matrix} & E3 & C4 \\ E3 & 0 & 0 \\ C4 & 0 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 1 \\ C4 & 0 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 1 \\ C4 & 1 & 1 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 2 \\ C4 & 1 & 1 \end{matrix} \rightarrow$$ 
$$
\begin{matrix} & E3 & C4 \\ E3 & 0 & 2 \\ C4 & 1 & 7 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 0 & 1 \\ C4 & 0.125 & 0.875 \end{matrix}$$  

So, we know the transitions and times when the next note should be played. A uniform random number generator will choose this next note: e.g. if transitions are 0.4 0.2 0.1 0.3 and generated number is 0.65, that's the 3rd one (this operation is called ````choose```` below).  
The note at time ````t````, given transition matrix ````P```` is  

$$ n_t = choose(P^t_{n_{t-1}}) $$  

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
<span class="l">40: </span>
<span class="l">41: </span>
<span class="l">42: </span>
<span class="l">43: </span>
<span class="l">44: </span>
<span class="l">45: </span>
<span class="l">46: </span>
<span class="l">47: </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="c">/// Compute the times and probabilities of transitions between notes</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '105', 254)" onmouseover="showTip(event, '105', 254)" class="i">transitions</span> <span onmouseout="hideTip(event, '106', 255)" onmouseover="showTip(event, '106', 255)" class="i">xs</span> <span class="o">=</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '107', 256)" onmouseover="showTip(event, '107', 256)" class="i">noteToId</span> <span class="o">=</span> <span onmouseout="hideTip(event, '83', 257)" onmouseover="showTip(event, '83', 257)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '108', 258)" onmouseover="showTip(event, '108', 258)" class="i">distinctBy</span> <span onmouseout="hideTip(event, '109', 259)" onmouseover="showTip(event, '109', 259)" class="i">fst</span> <span onmouseout="hideTip(event, '106', 260)" onmouseover="showTip(event, '106', 260)" class="i">xs</span> <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '83', 261)" onmouseover="showTip(event, '83', 261)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '110', 262)" onmouseover="showTip(event, '110', 262)" class="i">mapi</span> (<span class="k">fun</span> <span onmouseout="hideTip(event, '23', 263)" onmouseover="showTip(event, '23', 263)" class="i">i</span> (<span onmouseout="hideTip(event, '111', 264)" onmouseover="showTip(event, '111', 264)" class="i">note</span>, _) <span class="k">-&gt;</span> <span onmouseout="hideTip(event, '111', 265)" onmouseover="showTip(event, '111', 265)" class="i">note</span>, <span onmouseout="hideTip(event, '23', 266)" onmouseover="showTip(event, '23', 266)" class="i">i</span>) <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '112', 267)" onmouseover="showTip(event, '112', 267)" class="i">dict</span>

    <span class="c">// # of unique notes and # of notes in melody</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '113', 268)" onmouseover="showTip(event, '113', 268)" class="i">n</span>, <span onmouseout="hideTip(event, '114', 269)" onmouseover="showTip(event, '114', 269)" class="i">m</span> <span class="o">=</span> <span onmouseout="hideTip(event, '107', 270)" onmouseover="showTip(event, '107', 270)" class="i">noteToId</span><span class="o">.</span><span onmouseout="hideTip(event, '115', 271)" onmouseover="showTip(event, '115', 271)" class="i">Count</span>, <span onmouseout="hideTip(event, '116', 272)" onmouseover="showTip(event, '116', 272)" class="i">List</span><span class="o">.</span><span onmouseout="hideTip(event, '117', 273)" onmouseover="showTip(event, '117', 273)" class="i">length</span> <span onmouseout="hideTip(event, '106', 274)" onmouseover="showTip(event, '106', 274)" class="i">xs</span>

    <span class="k">let</span> <span onmouseout="hideTip(event, '118', 275)" onmouseover="showTip(event, '118', 275)" class="i">matrix</span> <span class="o">=</span> <span onmouseout="hideTip(event, '119', 276)" onmouseover="showTip(event, '119', 276)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, '120', 277)" onmouseover="showTip(event, '120', 277)" class="i">init</span> <span onmouseout="hideTip(event, '113', 278)" onmouseover="showTip(event, '113', 278)" class="i">n</span> (<span class="k">fun</span> _ <span class="k">-&gt;</span> <span onmouseout="hideTip(event, '119', 279)" onmouseover="showTip(event, '119', 279)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, '121', 280)" onmouseover="showTip(event, '121', 280)" class="i">zeroCreate</span> <span onmouseout="hideTip(event, '113', 281)" onmouseover="showTip(event, '113', 281)" class="i">n</span>)
    <span class="k">let</span> <span onmouseout="hideTip(event, '122', 282)" onmouseover="showTip(event, '122', 282)" class="i">times</span> <span class="o">=</span> <span onmouseout="hideTip(event, '119', 283)" onmouseover="showTip(event, '119', 283)" class="i">Array</span><span class="o">.</span><span onmouseout="hideTip(event, '123', 284)" onmouseover="showTip(event, '123', 284)" class="i">create</span> <span onmouseout="hideTip(event, '114', 285)" onmouseover="showTip(event, '114', 285)" class="i">m</span> <span class="n">0.</span>

    <span class="c">// fill the matrix</span>
    <span onmouseout="hideTip(event, '83', 286)" onmouseover="showTip(event, '83', 286)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '124', 287)" onmouseover="showTip(event, '124', 287)" class="i">pairwise</span> <span onmouseout="hideTip(event, '106', 288)" onmouseover="showTip(event, '106', 288)" class="i">xs</span>
    <span class="o">|&gt;</span> <span onmouseout="hideTip(event, '83', 289)" onmouseover="showTip(event, '83', 289)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '125', 290)" onmouseover="showTip(event, '125', 290)" class="i">iteri</span> (<span class="k">fun</span> <span onmouseout="hideTip(event, '23', 291)" onmouseover="showTip(event, '23', 291)" class="i">i</span> ((<span onmouseout="hideTip(event, '126', 292)" onmouseover="showTip(event, '126', 292)" class="i">note1</span>, <span onmouseout="hideTip(event, '127', 293)" onmouseover="showTip(event, '127', 293)" class="i">time1</span>), (<span onmouseout="hideTip(event, '128', 294)" onmouseover="showTip(event, '128', 294)" class="i">note2</span>, _)) <span class="k">-&gt;</span>
        <span class="k">let</span> <span onmouseout="hideTip(event, '129', 295)" onmouseover="showTip(event, '129', 295)" class="i">n1</span>, <span onmouseout="hideTip(event, '130', 296)" onmouseover="showTip(event, '130', 296)" class="i">n2</span> <span class="o">=</span> <span onmouseout="hideTip(event, '107', 297)" onmouseover="showTip(event, '107', 297)" class="i">noteToId</span><span class="o">.</span>[<span onmouseout="hideTip(event, '126', 298)" onmouseover="showTip(event, '126', 298)" class="i">note1</span>], <span onmouseout="hideTip(event, '107', 299)" onmouseover="showTip(event, '107', 299)" class="i">noteToId</span><span class="o">.</span>[<span onmouseout="hideTip(event, '128', 300)" onmouseover="showTip(event, '128', 300)" class="i">note2</span>]
        <span class="c">// option #1 - ignore times</span>
        <span class="c">//   matrix.[n1].[n2] &lt;- matrix.[n1].[n2] + 1.</span>
        <span class="c">// option #2 - add extra probability for transition to the same note</span>
        <span class="c">//    matrix.[n1].[n1] &lt;- matrix.[n1].[n1] + time1 * 16.0</span>
        <span class="c">//    matrix.[n1].[n2] &lt;- matrix.[n1].[n2] + 1.0</span>
        <span class="c">// option #3 - take time into account (scale by 16)</span>
        <span class="k">let</span> <span onmouseout="hideTip(event, '5', 301)" onmouseover="showTip(event, '5', 301)" class="i">p</span> <span class="o">=</span> <span onmouseout="hideTip(event, '127', 302)" onmouseover="showTip(event, '127', 302)" class="i">time1</span><span class="o">*</span><span class="n">16.</span><span class="o">-</span><span class="n">1.</span> <span class="k">in</span> <span class="k">if</span> <span onmouseout="hideTip(event, '5', 303)" onmouseover="showTip(event, '5', 303)" class="i">p</span> <span class="o">&gt;</span> <span class="n">0.</span> <span class="k">then</span> <span onmouseout="hideTip(event, '118', 304)" onmouseover="showTip(event, '118', 304)" class="i">matrix</span><span class="o">.</span>[<span onmouseout="hideTip(event, '129', 305)" onmouseover="showTip(event, '129', 305)" class="i">n1</span>]<span class="o">.</span>[<span onmouseout="hideTip(event, '129', 306)" onmouseover="showTip(event, '129', 306)" class="i">n1</span>] <span class="o">&lt;-</span> <span onmouseout="hideTip(event, '118', 307)" onmouseover="showTip(event, '118', 307)" class="i">matrix</span><span class="o">.</span>[<span onmouseout="hideTip(event, '129', 308)" onmouseover="showTip(event, '129', 308)" class="i">n1</span>]<span class="o">.</span>[<span onmouseout="hideTip(event, '129', 309)" onmouseover="showTip(event, '129', 309)" class="i">n1</span>] <span class="o">+</span> <span onmouseout="hideTip(event, '5', 310)" onmouseover="showTip(event, '5', 310)" class="i">p</span>
        <span onmouseout="hideTip(event, '118', 311)" onmouseover="showTip(event, '118', 311)" class="i">matrix</span><span class="o">.</span>[<span onmouseout="hideTip(event, '129', 312)" onmouseover="showTip(event, '129', 312)" class="i">n1</span>]<span class="o">.</span>[<span onmouseout="hideTip(event, '130', 313)" onmouseover="showTip(event, '130', 313)" class="i">n2</span>] <span class="o">&lt;-</span> <span onmouseout="hideTip(event, '118', 314)" onmouseover="showTip(event, '118', 314)" class="i">matrix</span><span class="o">.</span>[<span onmouseout="hideTip(event, '129', 315)" onmouseover="showTip(event, '129', 315)" class="i">n1</span>]<span class="o">.</span>[<span onmouseout="hideTip(event, '130', 316)" onmouseover="showTip(event, '130', 316)" class="i">n2</span>] <span class="o">+</span> <span class="n">1.0</span>
        
        <span onmouseout="hideTip(event, '122', 317)" onmouseover="showTip(event, '122', 317)" class="i">times</span><span class="o">.</span>[<span onmouseout="hideTip(event, '23', 318)" onmouseover="showTip(event, '23', 318)" class="i">i</span><span class="o">+</span><span class="n">1</span>] <span class="o">&lt;-</span> <span onmouseout="hideTip(event, '122', 319)" onmouseover="showTip(event, '122', 319)" class="i">times</span><span class="o">.</span>[<span onmouseout="hideTip(event, '23', 320)" onmouseover="showTip(event, '23', 320)" class="i">i</span>] <span class="o">+</span> <span onmouseout="hideTip(event, '127', 321)" onmouseover="showTip(event, '127', 321)" class="i">time1</span>)
    
    <span onmouseout="hideTip(event, '131', 322)" onmouseover="showTip(event, '131', 322)" class="i">normalizeRows</span> <span onmouseout="hideTip(event, '118', 323)" onmouseover="showTip(event, '118', 323)" class="i">matrix</span>, <span onmouseout="hideTip(event, '122', 324)" onmouseover="showTip(event, '122', 324)" class="i">times</span>, <span onmouseout="hideTip(event, '83', 325)" onmouseover="showTip(event, '83', 325)" class="i">Seq</span><span class="o">.</span><span onmouseout="hideTip(event, '84', 326)" onmouseover="showTip(event, '84', 326)" class="i">toArray</span> <span onmouseout="hideTip(event, '107', 327)" onmouseover="showTip(event, '107', 327)" class="i">noteToId</span><span class="o">.</span><span onmouseout="hideTip(event, '132', 328)" onmouseover="showTip(event, '132', 328)" class="i">Keys</span>

<span class="k">let</span> <span onmouseout="hideTip(event, '133', 329)" onmouseover="showTip(event, '133', 329)" class="i">matrix</span>, <span onmouseout="hideTip(event, '134', 330)" onmouseover="showTip(event, '134', 330)" class="i">times</span>, <span onmouseout="hideTip(event, '135', 331)" onmouseover="showTip(event, '135', 331)" class="i">idToNote</span> <span class="o">=</span> <span onmouseout="hideTip(event, '105', 332)" onmouseover="showTip(event, '105', 332)" class="i">transitions</span> <span onmouseout="hideTip(event, '94', 333)" onmouseover="showTip(event, '94', 333)" class="i">ent</span>

<span class="c">/// Find index of the next note given probability</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '136', 334)" onmouseover="showTip(event, '136', 334)" class="i">transTo</span> (<span onmouseout="hideTip(event, '137', 335)" onmouseover="showTip(event, '137', 335)" class="i">ps</span><span class="o">:</span> _[]) <span class="o">=</span> <span id="t138" onmouseout="hideTip(event, '138', 336)" onmouseover="showTip(event, '138', 336, document.getElementById('t138'))" class="omitted">...</span>

<span class="c">/// Generate music from transition matrices in a file</span>
<span class="k">let</span> <span onmouseout="hideTip(event, '139', 337)" onmouseover="showTip(event, '139', 337)" class="i">genMusic</span> <span onmouseout="hideTip(event, '140', 338)" onmouseover="showTip(event, '140', 338)" class="i">matricesFileName</span> (<span onmouseout="hideTip(event, '141', 339)" onmouseover="showTip(event, '141', 339)" class="i">fstNote</span>, <span onmouseout="hideTip(event, '142', 340)" onmouseover="showTip(event, '142', 340)" class="i">idToNote</span><span class="o">:</span> _[]) <span class="o">=</span>
    <span class="c">// transition matrices and times</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '143', 341)" onmouseover="showTip(event, '143', 341)" class="i">ps</span>, <span onmouseout="hideTip(event, '144', 342)" onmouseover="showTip(event, '144', 342)" class="i">ts</span> <span class="o">=</span> <span onmouseout="hideTip(event, '145', 343)" onmouseover="showTip(event, '145', 343)" class="i">readMatrices</span> <span onmouseout="hideTip(event, '140', 344)" onmouseover="showTip(event, '140', 344)" class="i">matricesFileName</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '113', 345)" onmouseover="showTip(event, '113', 345)" class="i">n</span> <span class="o">=</span> <span onmouseout="hideTip(event, '143', 346)" onmouseover="showTip(event, '143', 346)" class="i">ps</span><span class="o">.</span><span onmouseout="hideTip(event, '146', 347)" onmouseover="showTip(event, '146', 347)" class="i">Count</span>
    <span class="k">let</span> <span onmouseout="hideTip(event, '147', 348)" onmouseover="showTip(event, '147', 348)" class="i">rand</span> <span class="o">=</span> <span onmouseout="hideTip(event, '148', 349)" onmouseover="showTip(event, '148', 349)" class="i">System</span><span class="o">.</span><span onmouseout="hideTip(event, '149', 350)" onmouseover="showTip(event, '149', 350)" class="i">Random</span>()
    
    <span class="k">let</span> <span class="k">rec</span> <span onmouseout="hideTip(event, '150', 351)" onmouseover="showTip(event, '150', 351)" class="i">gen</span> <span onmouseout="hideTip(event, '23', 352)" onmouseover="showTip(event, '23', 352)" class="i">i</span> <span onmouseout="hideTip(event, '151', 353)" onmouseover="showTip(event, '151', 353)" class="i">prevInd</span> <span onmouseout="hideTip(event, '152', 354)" onmouseover="showTip(event, '152', 354)" class="i">res</span> <span class="o">=</span>
        <span class="k">if</span> <span onmouseout="hideTip(event, '23', 355)" onmouseover="showTip(event, '23', 355)" class="i">i</span> <span class="o">&gt;</span><span class="o">=</span> <span onmouseout="hideTip(event, '113', 356)" onmouseover="showTip(event, '113', 356)" class="i">n</span><span class="o">-</span><span class="n">1</span> <span class="k">then</span> <span onmouseout="hideTip(event, '116', 357)" onmouseover="showTip(event, '116', 357)" class="i">List</span><span class="o">.</span><span onmouseout="hideTip(event, '153', 358)" onmouseover="showTip(event, '153', 358)" class="i">rev</span> <span onmouseout="hideTip(event, '152', 359)" onmouseover="showTip(event, '152', 359)" class="i">res</span>
        <span class="k">else</span>
            <span class="k">let</span> <span onmouseout="hideTip(event, '154', 360)" onmouseover="showTip(event, '154', 360)" class="i">j</span> <span class="o">=</span> <span onmouseout="hideTip(event, '136', 361)" onmouseover="showTip(event, '136', 361)" class="i">transTo</span> <span onmouseout="hideTip(event, '143', 362)" onmouseover="showTip(event, '143', 362)" class="i">ps</span><span class="o">.</span>[<span onmouseout="hideTip(event, '23', 363)" onmouseover="showTip(event, '23', 363)" class="i">i</span>]<span class="o">.</span>[<span onmouseout="hideTip(event, '151', 364)" onmouseover="showTip(event, '151', 364)" class="i">prevInd</span>] (<span onmouseout="hideTip(event, '147', 365)" onmouseover="showTip(event, '147', 365)" class="i">rand</span><span class="o">.</span><span onmouseout="hideTip(event, '155', 366)" onmouseover="showTip(event, '155', 366)" class="i">NextDouble</span>())
            <span class="k">let</span> <span onmouseout="hideTip(event, '156', 367)" onmouseover="showTip(event, '156', 367)" class="i">next</span> <span class="o">=</span> <span onmouseout="hideTip(event, '142', 368)" onmouseover="showTip(event, '142', 368)" class="i">idToNote</span><span class="o">.</span>[<span onmouseout="hideTip(event, '154', 369)" onmouseover="showTip(event, '154', 369)" class="i">j</span>], <span onmouseout="hideTip(event, '144', 370)" onmouseover="showTip(event, '144', 370)" class="i">ts</span><span class="o">.</span>[<span onmouseout="hideTip(event, '23', 371)" onmouseover="showTip(event, '23', 371)" class="i">i</span>]
            <span onmouseout="hideTip(event, '150', 372)" onmouseover="showTip(event, '150', 372)" class="i">gen</span> (<span onmouseout="hideTip(event, '23', 373)" onmouseover="showTip(event, '23', 373)" class="i">i</span><span class="o">+</span><span class="n">1</span>) <span onmouseout="hideTip(event, '154', 374)" onmouseover="showTip(event, '154', 374)" class="i">j</span> (<span onmouseout="hideTip(event, '156', 375)" onmouseover="showTip(event, '156', 375)" class="i">next</span><span class="o">::</span><span onmouseout="hideTip(event, '152', 376)" onmouseover="showTip(event, '152', 376)" class="i">res</span>)

    <span onmouseout="hideTip(event, '150', 377)" onmouseover="showTip(event, '150', 377)" class="i">gen</span> <span class="n">0</span> <span class="n">0</span> [<span onmouseout="hideTip(event, '141', 378)" onmouseover="showTip(event, '141', 378)" class="i">fstNote</span>] </pre></td></tr></table>  

I tried to compute the transition matrix - and both method failed! Turns out this is the real-life 'unlucky' matrix. To be sure that doesn't work, checked R's <a href="http://cran.r-project.org/web/packages/expm/index.html">expm</a> package:  

<table class="pre"><tr><td class="lines"><pre class="fssnip">
<span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
</pre></td><td class="snippets"><pre class="fssnip">
<span class="i">logm(x, method </span><span class="o">=</span> <span class="s">"Eigen"</span><span class="i">)</span>
<span class="fsi">Error in logm(x, method = "Eigen") : non diagonalisable matrix</span>
<span class="i">logm(x, method </span><span class="o">=</span> <span class="s">"Higham08"</span><span class="i">)</span>
<span class="fsi">Error in solve.default(X[ii, ii] + X[ij, ij], S[ii, ij] - sumU) :</span>
<span class="fsi">system is computationally singular: reciprocal condition number = 0</span>
<span class="fsi">In addition: Warning messages:</span>
<span class="fsi">1: In sqrt(S[ij, ij]) : NaNs produced</span>
<span class="fsi">2: In sqrt(S[ij, ij]) : NaNs produced</span>
</pre></td></tr></table>  

Fortunately, we don't need fraction power here: if we count time in 1/16th, it becomes integer! 1/16 is 1, 3/8 is 6 and so on. Good old multiplication saves the situation.  

Here's several examples of generated melodies - there're only 10 notes are in the game, but the results are already pretty different from the original:  

[Generated-1]({{ site-url }}/sounds/gen1.mp3)   
[Generated-2]({{ site-url }}/sounds/gen2.mp3)   
[Generated-3]({{ site-url }}/sounds/gen3.mp3)   

What if we add an 'extra-probability' for note to stay in the same state?  

*E3 - 1/16, C4 - 1/8, E3 - 1/16, C4 - 3/8, C4 - 1/16*  

$$
\begin{matrix} & E3 & C4 \\ E3 & 0 & 0 \\ C4 & 0 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 1 & 1 \\ C4 & 0 & 0 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 1 & 1 \\ C4 & 1 & 2 \end{matrix} \rightarrow  
\begin{matrix} & E3 & C4 \\ E3 & 2 & 2 \\ C4 & 1 & 2 \end{matrix} \rightarrow $$
$$  
\begin{matrix} & E3 & C4 \\ E3 & 2 & 2 \\ C4 & 1 & 9 \end{matrix} \rightarrow
\begin{matrix} & E3 & C4 \\ E3 & 0.5 & 0.5 \\ C4 & 0.1 & 0.9 \end{matrix} $$  

For this matrix, the generator does exist and we can programmatically compose something like that:  

[Generated-series-1]({{ site-url }}/sounds/genseries1.mp3)  
[Generated-series-2]({{ site-url }}/sounds/genseries2.mp3)  

Why stop here? We could define the similar transitions for note length; or add chords - say, generated according to the current tonality; or switch tonalities/modes... Well, there's a whole 'musical math', solfeggio, full of different functions - plenty of space to experiment!  

[Happy Holidays!]({{ site-url }}/sounds/jingleBells.mp3)

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
</pre></td><td class="snippets"><pre class="fssnip">
<span class="k">let</span> <span onmouseout="hideTip(event, '157', 379)" onmouseover="showTip(event, '157', 379)" class="i">jingleBells</span> <span class="o">=</span> [
    <span onmouseout="hideTip(event, '101', 380)" onmouseover="showTip(event, '101', 380)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 381)" onmouseover="showTip(event, '45', 381)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 382)" onmouseover="showTip(event, '101', 382)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 383)" onmouseover="showTip(event, '45', 383)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 384)" onmouseover="showTip(event, '101', 384)" class="i">E4</span>,<span onmouseout="hideTip(event, '46', 385)" onmouseover="showTip(event, '46', 385)" class="i">Q1</span>
    <span onmouseout="hideTip(event, '101', 386)" onmouseover="showTip(event, '101', 386)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 387)" onmouseover="showTip(event, '45', 387)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 388)" onmouseover="showTip(event, '101', 388)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 389)" onmouseover="showTip(event, '45', 389)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 390)" onmouseover="showTip(event, '101', 390)" class="i">E4</span>,<span onmouseout="hideTip(event, '46', 391)" onmouseover="showTip(event, '46', 391)" class="i">Q1</span>
    <span onmouseout="hideTip(event, '101', 392)" onmouseover="showTip(event, '101', 392)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 393)" onmouseover="showTip(event, '45', 393)" class="i">Sq</span>; <span onmouseout="hideTip(event, '158', 394)" onmouseover="showTip(event, '158', 394)" class="i">G4</span>,<span onmouseout="hideTip(event, '45', 395)" onmouseover="showTip(event, '45', 395)" class="i">Sq</span>; <span onmouseout="hideTip(event, '98', 396)" onmouseover="showTip(event, '98', 396)" class="i">C4</span>,<span onmouseout="hideTip(event, '45', 397)" onmouseover="showTip(event, '45', 397)" class="i">Sq</span>; <span onmouseout="hideTip(event, '99', 398)" onmouseover="showTip(event, '99', 398)" class="i">D4</span>,<span onmouseout="hideTip(event, '45', 399)" onmouseover="showTip(event, '45', 399)" class="i">Sq</span>
    <span onmouseout="hideTip(event, '101', 400)" onmouseover="showTip(event, '101', 400)" class="i">E4</span>,<span onmouseout="hideTip(event, '46', 401)" onmouseover="showTip(event, '46', 401)" class="i">Q1</span><span class="o">*</span><span class="n">2.0</span>
    <span onmouseout="hideTip(event, '159', 402)" onmouseover="showTip(event, '159', 402)" class="i">F4</span>,<span onmouseout="hideTip(event, '45', 403)" onmouseover="showTip(event, '45', 403)" class="i">Sq</span>; <span onmouseout="hideTip(event, '159', 404)" onmouseover="showTip(event, '159', 404)" class="i">F4</span>,<span onmouseout="hideTip(event, '45', 405)" onmouseover="showTip(event, '45', 405)" class="i">Sq</span>; <span onmouseout="hideTip(event, '159', 406)" onmouseover="showTip(event, '159', 406)" class="i">F4</span>,<span onmouseout="hideTip(event, '45', 407)" onmouseover="showTip(event, '45', 407)" class="i">Sq</span>; <span onmouseout="hideTip(event, '159', 408)" onmouseover="showTip(event, '159', 408)" class="i">F4</span>,<span onmouseout="hideTip(event, '45', 409)" onmouseover="showTip(event, '45', 409)" class="i">Sq</span>
    <span onmouseout="hideTip(event, '159', 410)" onmouseover="showTip(event, '159', 410)" class="i">F4</span>,<span onmouseout="hideTip(event, '45', 411)" onmouseover="showTip(event, '45', 411)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 412)" onmouseover="showTip(event, '101', 412)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 413)" onmouseover="showTip(event, '45', 413)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 414)" onmouseover="showTip(event, '101', 414)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 415)" onmouseover="showTip(event, '45', 415)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 416)" onmouseover="showTip(event, '101', 416)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 417)" onmouseover="showTip(event, '45', 417)" class="i">Sq</span>
    <span onmouseout="hideTip(event, '101', 418)" onmouseover="showTip(event, '101', 418)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 419)" onmouseover="showTip(event, '45', 419)" class="i">Sq</span>; <span onmouseout="hideTip(event, '99', 420)" onmouseover="showTip(event, '99', 420)" class="i">D4</span>,<span onmouseout="hideTip(event, '45', 421)" onmouseover="showTip(event, '45', 421)" class="i">Sq</span>; <span onmouseout="hideTip(event, '99', 422)" onmouseover="showTip(event, '99', 422)" class="i">D4</span>,<span onmouseout="hideTip(event, '45', 423)" onmouseover="showTip(event, '45', 423)" class="i">Sq</span>; <span onmouseout="hideTip(event, '101', 424)" onmouseover="showTip(event, '101', 424)" class="i">E4</span>,<span onmouseout="hideTip(event, '45', 425)" onmouseover="showTip(event, '45', 425)" class="i">Sq</span>
    <span onmouseout="hideTip(event, '99', 426)" onmouseover="showTip(event, '99', 426)" class="i">D4</span>,<span onmouseout="hideTip(event, '46', 427)" onmouseover="showTip(event, '46', 427)" class="i">Q1</span>; <span onmouseout="hideTip(event, '158', 428)" onmouseover="showTip(event, '158', 428)" class="i">G4</span>,<span onmouseout="hideTip(event, '46', 429)" onmouseover="showTip(event, '46', 429)" class="i">Q1</span>
]

<span onmouseout="hideTip(event, '103', 430)" onmouseover="showTip(event, '103', 430)" class="i">Player</span><span class="o">.</span><span onmouseout="hideTip(event, '104', 431)" onmouseover="showTip(event, '104', 431)" class="i">Play</span> (<span onmouseout="hideTip(event, '87', 432)" onmouseover="showTip(event, '87', 432)" class="i">getMelody</span> <span onmouseout="hideTip(event, '157', 433)" onmouseover="showTip(event, '157', 433)" class="i">jingleBells</span>)</pre></td></tr></table>  

<p>
&nbsp;
&nbsp;
&nbsp;
</p>

[^1]: the example matrix isn't actually a transition matrix - the row sums should be equal to 1 - it's here just to demonstrate a possible problem.
[^2]: the sequence C4 - 1/8, E3 - 1/16 is almost the same as C4 - 1/16, C4 - 1/16, E3 - 1/16. That gives us 50/50 transitions: after each period of time C4 can either stay as C4 or transform to E3.

<div class="tip" id="1">open MathNet.Numerics.LinearAlgebra.Double<br />open MathNet.Numerics.LinearAlgebra.Generic<br />open Matrix<br />open SparseMatrix</p>
<p>[&lt;Literal&gt;]<br />let MAX_ITER = 10000<br />[&lt;Literal&gt;]<br />let PREC = 1e-60</div>
<div class="tip" id="2">val mpowEig : m:SparseMatrix -&gt; p:float -&gt; Matrix&lt;float&gt;</p>
<p>Full name: LogMusic.mpowEig<br /><em></p>
<p>&#160;Matrix power: eigen values decomposition method<br />&#160;M^p = V * (D .^ p) / V</em></div>
<div class="tip" id="3">val m : SparseMatrix</div>
<div class="tip" id="4">Multiple items<br />type SparseMatrix =<br />&#160;&#160;inherit Matrix<br />&#160;&#160;new : storage:SparseCompressedRowMatrixStorage&lt;float&gt; -&gt; SparseMatrix + 6 overloads<br />&#160;&#160;member CreateMatrix : numberOfRows:int * numberOfColumns:int * ?fullyMutable:bool -&gt; Matrix&lt;float&gt;<br />&#160;&#160;member CreateVector : size:int * ?fullyMutable:bool -&gt; Vector&lt;float&gt;<br />&#160;&#160;member FrobeniusNorm : unit -&gt; float<br />&#160;&#160;member IndexedEnumerator : unit -&gt; IEnumerable&lt;Tuple&lt;int, int, float&gt;&gt;<br />&#160;&#160;member InfinityNorm : unit -&gt; float<br />&#160;&#160;member IsSymmetric : bool<br />&#160;&#160;member KroneckerProduct : other:Matrix&lt;float&gt; * result:Matrix&lt;float&gt; -&gt; unit<br />&#160;&#160;member LowerTriangle : unit -&gt; Matrix&lt;float&gt; + 1 overload<br />&#160;&#160;member NonZerosCount : int<br />&#160;&#160;...</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.SparseMatrix</p>
<p>--------------------<br />SparseMatrix(storage: MathNet.Numerics.LinearAlgebra.Storage.SparseCompressedRowMatrixStorage&lt;float&gt;) : unit<br />SparseMatrix(order: int) : unit<br />SparseMatrix(rows: int, columns: int) : unit</div>
<div class="tip" id="5">val p : float</div>
<div class="tip" id="6">val evd : Factorization.Evd</div>
<div class="tip" id="7">Matrix.Evd() : Factorization.Evd</div>
<div class="tip" id="8">val v : Matrix&lt;float&gt;</div>
<div class="tip" id="9">val d : Matrix&lt;float&gt;</div>
<div class="tip" id="10">Factorization.Evd.EigenVectors() : Matrix&lt;float&gt;</div>
<div class="tip" id="11">Factorization.Evd.D() : Matrix&lt;float&gt;</div>
<div class="tip" id="12">val mapInPlace : f:(float -&gt; float) -&gt; A:#Matrix&lt;float&gt; -&gt; unit</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.Matrix.mapInPlace</p></div>
<div class="tip" id="13">val x : float</div>
<div class="tip" id="14">Matrix.Inverse() : Matrix&lt;float&gt;</div>
<div class="tip" id="15">val genm : a:SparseMatrix -&gt; Matrix&lt;float&gt;</p>
<p>Full name: LogMusic.genm<br /><em></p>
<p>&#160;generator matrix</em></div>
<div class="tip" id="16">val a : SparseMatrix</div>
<div class="tip" id="17">val size : int</div>
<div class="tip" id="18">property Matrix.RowCount: int</div>
<div class="tip" id="19">val mid : SparseMatrix</div>
<div class="tip" id="20">val constDiag : n:int -&gt; f:float -&gt; SparseMatrix</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.SparseMatrix.constDiag</p></div>
<div class="tip" id="21">val a_i : SparseMatrix</div>
<div class="tip" id="22">val logm : (int * SparseMatrix -&gt; Matrix&lt;float&gt; -&gt; Matrix&lt;float&gt;)</div>
<div class="tip" id="23">val i : int</div>
<div class="tip" id="24">val qpow : SparseMatrix</div>
<div class="tip" id="25">val res : Matrix&lt;float&gt;</div>
<div class="tip" id="26">val MAX_ITER : int</p>
<p>Full name: LogMusic.MAX_ITER</p></div>
<div class="tip" id="27">val qdiv : Matrix&lt;float&gt;</div>
<div class="tip" id="28">Matrix.Divide(scalar: float) : Matrix&lt;float&gt;<br />Matrix.Divide(scalar: float, result: Matrix&lt;float&gt;) : unit</div>
<div class="tip" id="29">Multiple items<br />val float : value:&#39;T -&gt; float (requires member op_Explicit)</p>
<p>Full name: Microsoft.FSharp.Core.Operators.float</p>
<p>--------------------<br />type float = System.Double</p>
<p>Full name: Microsoft.FSharp.Core.float</p>
<p>--------------------<br />type float&lt;&#39;Measure&gt; = float</p>
<p>Full name: Microsoft.FSharp.Core.float&lt;_&gt;</p></div>
<div class="tip" id="30">val sumSq : m:Matrix&lt;float&gt; -&gt; float</p>
<p>Full name: LogMusic.sumSq<br /><em></p>
<p>&#160;sum of squares</em></div>
<div class="tip" id="31">val PREC : float</p>
<p>Full name: LogMusic.PREC</p></div>
<div class="tip" id="32">val zeroCreate : rows:int -&gt; cols:int -&gt; SparseMatrix</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.SparseMatrix.zeroCreate</p></div>
<div class="tip" id="33">val updateneg : m:Matrix&lt;float&gt; -&gt; Matrix&lt;float&gt;</p>
<p>Full name: LogMusic.updateneg<br /><em></p>
<p>&#160;update negative </em></div>
<div class="tip" id="34">val expSeries : m:SparseMatrix -&gt; Matrix&lt;float&gt;</p>
<p>Full name: LogMusic.expSeries<br /><em></p>
<p>&#160;exp series approximation</em></div>
<div class="tip" id="35">val sum : (int * float * SparseMatrix -&gt; Matrix&lt;float&gt; -&gt; Matrix&lt;float&gt;)</div>
<div class="tip" id="36">val k : float</div>
<div class="tip" id="37">val mpow : SparseMatrix</div>
<div class="tip" id="38">val mpowi : SparseMatrix</div>
<div class="tip" id="39">val ki : float</div>
<div class="tip" id="40">val mdiv : Matrix&lt;float&gt;</div>
<div class="tip" id="41">val mpowSeries : m:SparseMatrix -&gt; p:float -&gt; Matrix&lt;float&gt;</p>
<p>Full name: LogMusic.mpowSeries<br /><em></p>
<p>&#160;Matrix power: series method</em></div>
<div class="tip" id="42">SparseMatrix.OfMatrix(matrix: Matrix&lt;float&gt;) : SparseMatrix</div>
<div class="tip" id="43">open System.IO<br />open System.Collections.Generic<br />open Undertone<br />open Undertone.Waves</div>
<div class="tip" id="44">Multiple items<br />type LiteralAttribute =<br />&#160;&#160;inherit Attribute<br />&#160;&#160;new : unit -&gt; LiteralAttribute</p>
<p>Full name: Microsoft.FSharp.Core.LiteralAttribute</p>
<p>--------------------<br />new : unit -&gt; LiteralAttribute</div>
<div class="tip" id="45">val Sq : float</p>
<p>Full name: LogMusic.Sq<br /><em></p>
<p>&#160;1/16 (semiquaver)</em></div>
<div class="tip" id="46">val Q1 : float</p>
<p>Full name: LogMusic.Q1<br /><em></p>
<p>&#160;1/8 (quaver)</em></div>
<div class="tip" id="47">val Q3 : float</p>
<p>Full name: LogMusic.Q3<br /><em></p>
<p>&#160;3/8</em></div>
<div class="tip" id="48">let inline noteValue time = Time.noteValue 15. time</p>
<p>/// Note samples directory<br />[&lt;Literal&gt;]<br />let dir = @&quot;C:\samples&quot;</div>
<div class="tip" id="49">val noteToPianoName : note:Note -&gt; string</p>
<p>Full name: LogMusic.noteToPianoName<br /><em></p>
<p>&#160;Mapping between note enum and file name convention </em></div>
<div class="tip" id="50">val note : Note</div>
<div class="tip" id="51">type Note =<br />&#160;&#160;|  Cflat  =  -1<br />&#160;&#160;|  C  =  0<br />&#160;&#160;|  Csharp  =  1<br />&#160;&#160;|  Dflat  =  1<br />&#160;&#160;|  D  =  2<br />&#160;&#160;|  Dsharp  =  3<br />&#160;&#160;|  Eflat  =  3<br />&#160;&#160;|  E  =  4<br />&#160;&#160;|  Fflat  =  4<br />&#160;&#160;|  Esharp  =  5<br />&#160;&#160;|  F  =  5<br />&#160;&#160;|  Fsharp  =  6<br />&#160;&#160;|  Gflat  =  6<br />&#160;&#160;|  G  =  7<br />&#160;&#160;|  Gsharp  =  8<br />&#160;&#160;|  Aflat  =  8<br />&#160;&#160;|  A  =  9<br />&#160;&#160;|  Asharp  =  10<br />&#160;&#160;|  Bflat  =  10<br />&#160;&#160;|  B  =  11<br />&#160;&#160;|  Bsharp  =  12</p>
<p>Full name: Undertone.Note</p></div>
<div class="tip" id="52">Note.C: Note = 0</div>
<div class="tip" id="53">Note.Csharp: Note = 1</div>
<div class="tip" id="54">Note.D: Note = 2</div>
<div class="tip" id="55">Note.Dsharp: Note = 3</div>
<div class="tip" id="56">Note.E: Note = 4</div>
<div class="tip" id="57">Note.F: Note = 5</div>
<div class="tip" id="58">Note.Fsharp: Note = 6</div>
<div class="tip" id="59">Note.G: Note = 7</div>
<div class="tip" id="60">Note.Gsharp: Note = 8</div>
<div class="tip" id="61">Note.A: Note = 9</div>
<div class="tip" id="62">Note.Asharp: Note = 10</div>
<div class="tip" id="63">Note.B: Note = 11</div>
<div class="tip" id="64">val failwith : message:string -&gt; &#39;T</p>
<p>Full name: Microsoft.FSharp.Core.Operators.failwith</p></div>
<div class="tip" id="65">val readPianoNote : unit -&gt; (Note * int -&gt; float [])</p>
<p>Full name: LogMusic.readPianoNote<br /><em></p>
<p>&#160;Read piano note from .aiff file</em></div>
<div class="tip" id="66">val cache : Dictionary&lt;(string * int),float []&gt;</div>
<div class="tip" id="67">Multiple items<br />type Dictionary&lt;&#39;TKey,&#39;TValue&gt; =<br />&#160;&#160;new : unit -&gt; Dictionary&lt;&#39;TKey, &#39;TValue&gt; + 5 overloads<br />&#160;&#160;member Add : key:&#39;TKey * value:&#39;TValue -&gt; unit<br />&#160;&#160;member Clear : unit -&gt; unit<br />&#160;&#160;member Comparer : IEqualityComparer&lt;&#39;TKey&gt;<br />&#160;&#160;member ContainsKey : key:&#39;TKey -&gt; bool<br />&#160;&#160;member ContainsValue : value:&#39;TValue -&gt; bool<br />&#160;&#160;member Count : int<br />&#160;&#160;member GetEnumerator : unit -&gt; Enumerator&lt;&#39;TKey, &#39;TValue&gt;<br />&#160;&#160;member GetObjectData : info:SerializationInfo * context:StreamingContext -&gt; unit<br />&#160;&#160;member Item : &#39;TKey -&gt; &#39;TValue with get, set<br />&#160;&#160;...<br />&#160;&#160;nested type Enumerator<br />&#160;&#160;nested type KeyCollection<br />&#160;&#160;nested type ValueCollection</p>
<p>Full name: System.Collections.Generic.Dictionary&lt;_,_&gt;</p>
<p>--------------------<br />Dictionary() : unit<br />Dictionary(capacity: int) : unit<br />Dictionary(comparer: IEqualityComparer&lt;&#39;TKey&gt;) : unit<br />Dictionary(dictionary: IDictionary&lt;&#39;TKey,&#39;TValue&gt;) : unit<br />Dictionary(capacity: int, comparer: IEqualityComparer&lt;&#39;TKey&gt;) : unit<br />Dictionary(dictionary: IDictionary&lt;&#39;TKey,&#39;TValue&gt;, comparer: IEqualityComparer&lt;&#39;TKey&gt;) : unit</div>
<div class="tip" id="68">module HashIdentity</p>
<p>from Microsoft.FSharp.Collections</p></div>
<div class="tip" id="69">val Structural&lt;&#39;T (requires equality)&gt; : IEqualityComparer&lt;&#39;T&gt; (requires equality)</p>
<p>Full name: Microsoft.FSharp.Collections.HashIdentity.Structural</p></div>
<div class="tip" id="70">val path : (string * &#39;a -&gt; string)</div>
<div class="tip" id="71">val noteName : string</div>
<div class="tip" id="72">val octave : &#39;a</div>
<div class="tip" id="73">type Path =<br />&#160;&#160;static val DirectorySeparatorChar : char<br />&#160;&#160;static val AltDirectorySeparatorChar : char<br />&#160;&#160;static val VolumeSeparatorChar : char<br />&#160;&#160;static val InvalidPathChars : char[]<br />&#160;&#160;static val PathSeparator : char<br />&#160;&#160;static member ChangeExtension : path:string * extension:string -&gt; string<br />&#160;&#160;static member Combine : params paths:string[] -&gt; string + 3 overloads<br />&#160;&#160;static member GetDirectoryName : path:string -&gt; string<br />&#160;&#160;static member GetExtension : path:string -&gt; string<br />&#160;&#160;static member GetFileName : path:string -&gt; string<br />&#160;&#160;...</p>
<p>Full name: System.IO.Path</p></div>
<div class="tip" id="74">Path.Combine(params paths: string []) : string<br />Path.Combine(path1: string, path2: string) : string<br />Path.Combine(path1: string, path2: string, path3: string) : string<br />Path.Combine(path1: string, path2: string, path3: string, path4: string) : string</div>
<div class="tip" id="75">val dir : string</p>
<p>Full name: LogMusic.dir<br /><em></p>
<p>&#160;Note samples directory</em></div>
<div class="tip" id="76">Multiple items<br />val string : value:&#39;T -&gt; string</p>
<p>Full name: Microsoft.FSharp.Core.Operators.string</p>
<p>--------------------<br />type string = System.String</p>
<p>Full name: Microsoft.FSharp.Core.string</p></div>
<div class="tip" id="77">val octave : int</div>
<div class="tip" id="78">val noteKey : string * int</div>
<div class="tip" id="79">Dictionary.TryGetValue(key: string * int, value: byref&lt;float []&gt;) : bool</div>
<div class="tip" id="80">val wave : float []</div>
<div class="tip" id="81">module IO</p>
<p>from Undertone</p></div>
<div class="tip" id="82">val read : file:string -&gt; seq&lt;float&gt;</p>
<p>Full name: Undertone.IO.read</p></div>
<div class="tip" id="83">module Seq</p>
<p>from Microsoft.FSharp.Collections</p></div>
<div class="tip" id="84">val toArray : source:seq&lt;&#39;T&gt; -&gt; &#39;T []</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.toArray</p></div>
<div class="tip" id="85">Dictionary.Add(key: string * int, value: float []) : unit</div>
<div class="tip" id="86">val makeNote : (Note * int -&gt; float [])</p>
<p>Full name: LogMusic.makeNote</p></div>
<div class="tip" id="87">val getMelody : (((Note * int) * float) list -&gt; seq&lt;float&gt;)</p>
<p>Full name: LogMusic.getMelody</p></div>
<div class="tip" id="88">val collect : mapping:(&#39;T -&gt; #seq&lt;&#39;U&gt;) -&gt; source:seq&lt;&#39;T&gt; -&gt; seq&lt;&#39;U&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.collect</p></div>
<div class="tip" id="89">val noct : Note * int</div>
<div class="tip" id="90">val time : float</div>
<div class="tip" id="91">val take : count:int -&gt; source:seq&lt;&#39;T&gt; -&gt; seq&lt;&#39;T&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.take</p></div>
<div class="tip" id="92">val noteValue : time:float -&gt; int</p>
<p>Full name: LogMusic.noteValue</p></div>
<div class="tip" id="93">let D3  = Note.D, 3<br />let Ds3 = Note.Dsharp, 3<br />let E3  = Note.E, 3<br />let Fs3 = Note.Fsharp, 3<br />let G3  = Note.G, 3<br />let A3  = Note.A, 3<br />let C4  = Note.C, 4<br />let D4  = Note.D, 4<br />let Ds4 = Note.Dsharp, 4<br />let E4  = Note.E, 4</p>
<p>let G4 = Note.G, 4<br />let F4 = Note.F, 4</div>
<div class="tip" id="94">val ent : ((Note * int) * float) list</p>
<p>Full name: LogMusic.ent<br /><em></p>
<p>&#160;Sample melody</em></div>
<div class="tip" id="95">val D3 : Note * int</p>
<p>Full name: LogMusic.D3</p></div>
<div class="tip" id="96">val Ds3 : Note * int</p>
<p>Full name: LogMusic.Ds3</p></div>
<div class="tip" id="97">val E3 : Note * int</p>
<p>Full name: LogMusic.E3</p></div>
<div class="tip" id="98">val C4 : Note * int</p>
<p>Full name: LogMusic.C4</p></div>
<div class="tip" id="99">val D4 : Note * int</p>
<p>Full name: LogMusic.D4</p></div>
<div class="tip" id="100">val Ds4 : Note * int</p>
<p>Full name: LogMusic.Ds4</p></div>
<div class="tip" id="101">val E4 : Note * int</p>
<p>Full name: LogMusic.E4</p></div>
<div class="tip" id="102">E3,Sq; C4,Q1; E3,Sq; C4,Q1; E3,Sq; C4,Q3<br />&#160;&#160;&#160;&#160;A3,Sq; G3,Sq<br />&#160;&#160;&#160;&#160;Fs3,Sq; A3,Sq; C4,Sq; E4,Q1; D4,Sq; C4,Sq; A3,Sq<br />&#160;&#160;&#160;&#160;D4,Q3; D3,Sq; Ds3,Sq<br />&#160;&#160;&#160;&#160;E3,Sq; C4,Q1; E3,Sq; C4,Q1; E3,Sq; C4,Q3<br />&#160;&#160;&#160;&#160;C4,Sq; D4,Sq; Ds4,Sq<br />&#160;&#160;&#160;&#160;E4,Sq; C4,Sq; D4,Sq; E4,Q1; C4,Sq; D4,Q1<br />&#160;&#160;&#160;&#160;C4,Q3; D4,Sq; Ds4,Sq<br />&#160;&#160;&#160;&#160;E4,Sq; C4,Sq; D4,Sq; E4,Q1; C4,Sq; D4,Sq; C4,Sq<br />&#160;&#160;&#160;&#160;E4,Sq; C4,Sq; D4,Sq; E4,Q1; C4,Sq; D4,Sq; C4,Sq<br />&#160;&#160;&#160;&#160;E4,Sq; C4,Sq; D4,Sq; E4,Q1; C4,Sq; D4,Q1</div>
<div class="tip" id="103">type Player =<br />&#160;&#160;private new : unit -&gt; Player<br />&#160;&#160;static member Play : sampleSource:seq&lt;float&gt; -&gt; IPlayer</p>
<p>Full name: Undertone.Player</p></div>
<div class="tip" id="104">static member Player.Play : sampleSource:seq&lt;float&gt; -&gt; IPlayer</div>
<div class="tip" id="105">val transitions : xs:(&#39;a * float) list -&gt; float [] [] * float [] * &#39;a [] (requires equality)</p>
<p>Full name: LogMusic.transitions<br /><em></p>
<p>&#160;Compute the times and probabilities of transitions between notes</em></div>
<div class="tip" id="106">val xs : (&#39;a * float) list (requires equality)</div>
<div class="tip" id="107">val noteToId : IDictionary&lt;&#39;a,int&gt; (requires equality)</div>
<div class="tip" id="108">val distinctBy : projection:(&#39;T -&gt; &#39;Key) -&gt; source:seq&lt;&#39;T&gt; -&gt; seq&lt;&#39;T&gt; (requires equality)</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.distinctBy</p></div>
<div class="tip" id="109">val fst : tuple:(&#39;T1 * &#39;T2) -&gt; &#39;T1</p>
<p>Full name: Microsoft.FSharp.Core.Operators.fst</p></div>
<div class="tip" id="110">val mapi : mapping:(int -&gt; &#39;T -&gt; &#39;U) -&gt; source:seq&lt;&#39;T&gt; -&gt; seq&lt;&#39;U&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.mapi</p></div>
<div class="tip" id="111">val note : &#39;a (requires equality)</div>
<div class="tip" id="112">val dict : keyValuePairs:seq&lt;&#39;Key * &#39;Value&gt; -&gt; IDictionary&lt;&#39;Key,&#39;Value&gt; (requires equality)</p>
<p>Full name: Microsoft.FSharp.Core.ExtraTopLevelOperators.dict</p></div>
<div class="tip" id="113">val n : int</div>
<div class="tip" id="114">val m : int</div>
<div class="tip" id="115">property ICollection.Count: int</div>
<div class="tip" id="116">Multiple items<br />type List&lt;&#39;T&gt; =<br />&#160;&#160;new : unit -&gt; List&lt;&#39;T&gt; + 2 overloads<br />&#160;&#160;member Add : item:&#39;T -&gt; unit<br />&#160;&#160;member AddRange : collection:IEnumerable&lt;&#39;T&gt; -&gt; unit<br />&#160;&#160;member AsReadOnly : unit -&gt; ReadOnlyCollection&lt;&#39;T&gt;<br />&#160;&#160;member BinarySearch : item:&#39;T -&gt; int + 2 overloads<br />&#160;&#160;member Capacity : int with get, set<br />&#160;&#160;member Clear : unit -&gt; unit<br />&#160;&#160;member Contains : item:&#39;T -&gt; bool<br />&#160;&#160;member ConvertAll&lt;&#39;TOutput&gt; : converter:Converter&lt;&#39;T, &#39;TOutput&gt; -&gt; List&lt;&#39;TOutput&gt;<br />&#160;&#160;member CopyTo : array:&#39;T[] -&gt; unit + 2 overloads<br />&#160;&#160;...<br />&#160;&#160;nested type Enumerator</p>
<p>Full name: System.Collections.Generic.List&lt;_&gt;</p>
<p>--------------------<br />List() : unit<br />List(capacity: int) : unit<br />List(collection: IEnumerable&lt;&#39;T&gt;) : unit</div>
<div class="tip" id="117">val length : list:&#39;T list -&gt; int</p>
<p>Full name: Microsoft.FSharp.Collections.List.length</p></div>
<div class="tip" id="118">val matrix : float [] []</div>
<div class="tip" id="119">module Array</p>
<p>from Microsoft.FSharp.Collections</p></div>
<div class="tip" id="120">val init : count:int -&gt; initializer:(int -&gt; &#39;T) -&gt; &#39;T []</p>
<p>Full name: Microsoft.FSharp.Collections.Array.init</p></div>
<div class="tip" id="121">val zeroCreate : count:int -&gt; &#39;T []</p>
<p>Full name: Microsoft.FSharp.Collections.Array.zeroCreate</p></div>
<div class="tip" id="122">val times : float []</div>
<div class="tip" id="123">val create : count:int -&gt; value:&#39;T -&gt; &#39;T []</p>
<p>Full name: Microsoft.FSharp.Collections.Array.create</p></div>
<div class="tip" id="124">val pairwise : source:seq&lt;&#39;T&gt; -&gt; seq&lt;&#39;T * &#39;T&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.pairwise</p></div>
<div class="tip" id="125">val iteri : action:(int -&gt; &#39;T -&gt; unit) -&gt; source:seq&lt;&#39;T&gt; -&gt; unit</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.iteri</p></div>
<div class="tip" id="126">val note1 : &#39;a (requires equality)</div>
<div class="tip" id="127">val time1 : float</div>
<div class="tip" id="128">val note2 : &#39;a (requires equality)</div>
<div class="tip" id="129">val n1 : int</div>
<div class="tip" id="130">val n2 : int</div>
<div class="tip" id="131">val normalizeRows : m:float [] [] -&gt; float [] []</p>
<p>Full name: LogMusic.normalizeRows<br /><em></p>
<p>&#160;Normalize rows, so the row-sums are equal to 1</em></div>
<div class="tip" id="132">property IDictionary.Keys: ICollection&lt;&#39;a&gt;</div>
<div class="tip" id="133">val matrix : float [] []</p>
<p>Full name: LogMusic.matrix</p></div>
<div class="tip" id="134">val times : float []</p>
<p>Full name: LogMusic.times</p></div>
<div class="tip" id="135">val idToNote : (Note * int) []</p>
<p>Full name: LogMusic.idToNote</p></div>
<div class="tip" id="136">val transTo : ps:float [] -&gt; (float -&gt; int)</p>
<p>Full name: LogMusic.transTo<br /><em></p>
<p>&#160;Find index of the next note given probability</em></div>
<div class="tip" id="137">val ps : float []</div>
<div class="tip" id="138">let last = ps.Length-1<br />&#160;&#160;&#160;&#160;let rec findj j v =<br />&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;if ps.[j] &gt;= v || j = last then j<br />&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;else findj (j+1) (v - ps.[j])<br />&#160;&#160;&#160;&#160;findj 0</div>
<div class="tip" id="139">val genMusic : matricesFileName:string -&gt; fstNote:(&#39;a * float) * idToNote:&#39;a [] -&gt; (&#39;a * float) list</p>
<p>Full name: LogMusic.genMusic<br /><em></p>
<p>&#160;Generate music from transition matrices in a file</em></div>
<div class="tip" id="140">val matricesFileName : string</div>
<div class="tip" id="141">val fstNote : &#39;a * float</div>
<div class="tip" id="142">val idToNote : &#39;a []</div>
<div class="tip" id="143">val ps : List&lt;ResizeArray&lt;float []&gt;&gt;</div>
<div class="tip" id="144">val ts : float []</div>
<div class="tip" id="145">val readMatrices : path:string -&gt; List&lt;ResizeArray&lt;float []&gt;&gt; * float []</p>
<p>Full name: LogMusic.readMatrices<br /><em></p>
<p>&#160;Read transition matrices and times</em></div>
<div class="tip" id="146">property List.Count: int</div>
<div class="tip" id="147">val rand : System.Random</div>
<div class="tip" id="148">namespace System</div>
<div class="tip" id="149">Multiple items<br />type Random =<br />&#160;&#160;new : unit -&gt; Random + 1 overload<br />&#160;&#160;member Next : unit -&gt; int + 2 overloads<br />&#160;&#160;member NextBytes : buffer:byte[] -&gt; unit<br />&#160;&#160;member NextDouble : unit -&gt; float</p>
<p>Full name: System.Random</p>
<p>--------------------<br />System.Random() : unit<br />System.Random(Seed: int) : unit</div>
<div class="tip" id="150">val gen : (int -&gt; int -&gt; (&#39;a * float) list -&gt; (&#39;a * float) list)</div>
<div class="tip" id="151">val prevInd : int</div>
<div class="tip" id="152">val res : (&#39;a * float) list</div>
<div class="tip" id="153">val rev : list:&#39;T list -&gt; &#39;T list</p>
<p>Full name: Microsoft.FSharp.Collections.List.rev</p></div>
<div class="tip" id="154">val j : int</div>
<div class="tip" id="155">System.Random.NextDouble() : float</div>
<div class="tip" id="156">val next : &#39;a * float</div>
<div class="tip" id="157">val jingleBells : ((Note * int) * float) list</p>
<p>Full name: LogMusic.jingleBells</p></div>
<div class="tip" id="158">val G4 : Note * int</p>
<p>Full name: LogMusic.G4</p></div>
<div class="tip" id="159">val F4 : Note * int</p>
<p>Full name: LogMusic.F4</p></div>
