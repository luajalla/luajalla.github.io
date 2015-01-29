---
layout: post
title: 'LP: Simplex Draft'
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 1392
wordpress_url: http://luajalla.azurewebsites.net/?p=1392
date: '2013-06-22 19:50:42 +0200'
date_gmt: '2013-06-22 19:50:42 +0200'
tags:
- fsharp
- lp
- math
image:
  feature: header3.jpg
  credit: Geneva Lake
---
Everybody solves some optimization problems – the airlines schedule flights, companies manage production facilities, salesman still looks for traveling options… When you need to achieve the best outcome minimizing/maximizing a linear cost functions you meet linear programming.  

*[Originally posted [here](http://luajalla.azurewebsites.net/lp-simplex-draft/)].* 

LP was developed for military purposes in 1939, so it has a long history. Now it is one of the most important problems in operations research and heavily used in different areas directly or as sub-problems.  

###Intro
The standard algorithm here is the simplex method. Lots of information is floating around, but let’s review the basics. There’re several forms for expressing linear programs. Standard form:  

$$
\begin{array}{1}
\text{maximize }{c}'x\\
\text{subject to}\\
Ax <= b\\
x >= 0
\end{array}
$$

We assume that the rows of matrix $$A$$(*m x n*) are linearly independent.  

The form above can be easily converted to slack form with a help of the new variables (slack variables):  

$$
\begin{array}{1}
s = b_{i} - \sum a_{ij}x_{j}\\
s >= 0
\end{array}
$$

So the problem now is  

$$
\begin{array}{1}
\text{maximize }{c}'x\\
\text{subject to}\\
Ax = b\\
x >= 0
\end{array}
$$

![Simplex]({{ site.url }}/images/simplex.png)
{: .image-right}

Geometrical interpretation for 2 variables is very intuitive: all $$x_{1}$$ and $$x_{2}$$ satisfying the constraints are feasible solutions, and an optimal solution – maximum or minimum – is at a vertex. This feasible region, for n variables – in n-dimensional space, is called a simplex. The algorithm terminates when it reaches an optimal objective value at some vertex.  

Why write a custom implementation? For me Excel solver is usually a solution [^1], works like a charm – when it’s not a Mac version. The naïve version of algorithm is quite simple to implement, it’s also a nice refresher and managed to solve the problem, so why not?  

Consider the following problem:  

$$
\begin{array}{1}
\text{minimize }x_{1} + 5*x_{2} - 2*x_{3}\\
\text{subject to}\\
x_{1} + x_{2} + x_{3} <= 4\\
x_{1} <= 2\\
x_{3} <= 3\\
3*x_{2} + x_{3} <= 6\\
x_{1}, x_{2}, x_{3} >= 0
\end{array}
$$

The solution is very straightforward:  
<strong>1.</strong> Convert to slack form  

$$
\begin{array}{l}
\text{minimize } x_{1} + 5x_{2} - 2x_{3}\\
\text{subject to }\\
x_{1} + x_{2} + x_{3} + x_{4} <= 4\\
x_{1} + x_{5} <= 2\\
x_{3} + x_{6} <= 3\\
3*x_{2} + x_{3} + x_{7} <= 6\\
x_{1},\ x_{2},\ x_{3},\ x_{4},\ x_{5},\ x_{6},\ x_{7} <= 0
\end{array}
$$

<strong>2.</strong> We see that the cost can be reduced with increasing $$x_{3}$$ (it’s also obvious that max $$x_{3}$$ value is equal to 3). If the cost is already optimal the solution is found.  

For each $$j$$ the reduced cost is defined as  

$$
\begin{array}{1}
\bar{c_{j}} = c_{j} - {c_{b}}'B^{-1}A_{j}\\
\text{where }c_{b}\text{ – the vector of basic variables costs.}
\end{array}
$$

<strong>3. </strong> Look for a direction of cost decrease, when moving into the new direction we replace a basic variable with a new one; go to the next iteration.  

###Implementation
The first version I came up with was entirely immutable – with all the costs of creating new sets, matrices etc. But there’s no sense to recreate the whole matrix when only one column is changed for the next iteration. This version is listed below – not that functional, more dangerous, with a mutable state, but cares about time/memory. Note, that this implementation doesn’t handle some corner-cases as it was not a part of my goal.  

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
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="omitted" onmouseover="showTip(event, 'fstips17', 35)" onmouseout="hideTip(event, 'fstips17', 35)">(Math.Net references)</span>

<span class="k">type</span> <span class="i" onmouseover="showTip(event, 'fstips18', 36)" onmouseout="hideTip(event, 'fstips18', 36)">SimplexResult</span> <span class="o">=</span>
    | <span class="i" onmouseover="showTip(event, 'fstips19', 37)" onmouseout="hideTip(event, 'fstips19', 37)">Success</span> <span class="k">of</span> <span class="i" onmouseover="showTip(event, 'fstips7', 38)" onmouseout="hideTip(event, 'fstips7', 38)">Vector</span><span class="o">&lt;</span><span class="i" onmouseover="showTip(event, 'fstips6', 39)" onmouseout="hideTip(event, 'fstips6', 39)">float</span><span class="o">&gt;</span>
    | <span class="i" onmouseover="showTip(event, 'fstips20', 40)" onmouseout="hideTip(event, 'fstips20', 40)">Error</span> <span class="k">of</span> <span class="i" onmouseover="showTip(event, 'fstips21', 41)" onmouseout="hideTip(event, 'fstips21', 41)">string</span>

<span class="c">///</span> <span class="c">Simplex</span> <span class="c">method</span> <span class="c">implementation</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips22', 42)" onmouseout="hideTip(event, 'fstips22', 42)">simplexImpl</span> (<span class="i" onmouseover="showTip(event, 'fstips23', 43)" onmouseout="hideTip(event, 'fstips23', 43)">A</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips3', 44)" onmouseout="hideTip(event, 'fstips3', 44)">Matrix</span>) <span class="i" onmouseover="showTip(event, 'fstips24', 45)" onmouseout="hideTip(event, 'fstips24', 45)">b</span> (<span class="i" onmouseover="showTip(event, 'fstips25', 46)" onmouseout="hideTip(event, 'fstips25', 46)">c</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips7', 47)" onmouseout="hideTip(event, 'fstips7', 47)">Vector</span>) (<span class="i" onmouseover="showTip(event, 'fstips26', 48)" onmouseout="hideTip(event, 'fstips26', 48)">x</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips7', 49)" onmouseout="hideTip(event, 'fstips7', 49)">Vector</span>, <span class="i" onmouseover="showTip(event, 'fstips27', 50)" onmouseout="hideTip(event, 'fstips27', 50)">Ib</span><span class="o">:</span> _[], <span class="i" onmouseover="showTip(event, 'fstips28', 51)" onmouseout="hideTip(event, 'fstips28', 51)">In</span><span class="o">:</span> _[]) <span class="o">=</span>
    <span class="omitted" onmouseover="showTip(event, 'fstips29', 52)" onmouseout="hideTip(event, 'fstips29', 52)">...</span>
    <span class="c">//</span> <span class="c">1.</span> <span class="c">start</span> <span class="c">with</span> <span class="c">basic</span> <span class="c">matrix</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips30', 53)" onmouseout="hideTip(event, 'fstips30', 53)">B</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips31', 54)" onmouseout="hideTip(event, 'fstips31', 54)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips32', 55)" onmouseout="hideTip(event, 'fstips32', 55)">map</span> <span class="i" onmouseover="showTip(event, 'fstips33', 56)" onmouseout="hideTip(event, 'fstips33', 56)">A</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips34', 57)" onmouseout="hideTip(event, 'fstips34', 57)">Column</span> <span class="i" onmouseover="showTip(event, 'fstips27', 58)" onmouseout="hideTip(event, 'fstips27', 58)">Ib</span> <span class="o">|&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips35', 59)" onmouseout="hideTip(event, 'fstips35', 59)">DenseMatrix</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips36', 60)" onmouseout="hideTip(event, 'fstips36', 60)">ofColumns</span> <span class="i" onmouseover="showTip(event, 'fstips37', 61)" onmouseout="hideTip(event, 'fstips37', 61)">m</span> <span class="i" onmouseover="showTip(event, 'fstips37', 62)" onmouseout="hideTip(event, 'fstips37', 62)">m</span>
    
    <span class="k">let</span> <span class="k">rec</span> <span class="i" onmouseover="showTip(event, 'fstips38', 63)" onmouseout="hideTip(event, 'fstips38', 63)">calc</span> (<span class="i" onmouseover="showTip(event, 'fstips39', 64)" onmouseout="hideTip(event, 'fstips39', 64)">Binv</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips3', 65)" onmouseout="hideTip(event, 'fstips3', 65)">Matrix</span>) <span class="i" onmouseover="showTip(event, 'fstips9', 66)" onmouseout="hideTip(event, 'fstips9', 66)">iter</span> <span class="o">=</span>
        <span class="c">//</span> <span class="c">2.</span> <span class="c">reduce</span> <span class="c">costs</span> <span class="c">and</span> <span class="c">check</span> <span class="c">optimality</span> <span class="c">conditions</span>
        <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips40', 67)" onmouseout="hideTip(event, 'fstips40', 67)">p</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips41', 68)" onmouseout="hideTip(event, 'fstips41', 68)">cb</span> <span class="o">*</span> <span class="i" onmouseover="showTip(event, 'fstips39', 69)" onmouseout="hideTip(event, 'fstips39', 69)">Binv</span> <span class="o">*</span> <span class="i" onmouseover="showTip(event, 'fstips33', 70)" onmouseout="hideTip(event, 'fstips33', 70)">A</span>
        <span class="i" onmouseover="showTip(event, 'fstips25', 71)" onmouseout="hideTip(event, 'fstips25', 71)">c</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips42', 72)" onmouseout="hideTip(event, 'fstips42', 72)">MapIndexedInplace</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips15', 73)" onmouseout="hideTip(event, 'fstips15', 73)">i</span> <span class="i" onmouseover="showTip(event, 'fstips43', 74)" onmouseout="hideTip(event, 'fstips43', 74)">ci</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips43', 75)" onmouseout="hideTip(event, 'fstips43', 75)">ci</span> <span class="o">-</span> <span class="i" onmouseover="showTip(event, 'fstips40', 76)" onmouseout="hideTip(event, 'fstips40', 76)">p</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips15', 77)" onmouseout="hideTip(event, 'fstips15', 77)">i</span>])

        <span class="k">match</span> <span class="i" onmouseover="showTip(event, 'fstips31', 78)" onmouseout="hideTip(event, 'fstips31', 78)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips44', 79)" onmouseout="hideTip(event, 'fstips44', 79)">tryFindIndex</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips15', 80)" onmouseout="hideTip(event, 'fstips15', 80)">i</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips25', 81)" onmouseout="hideTip(event, 'fstips25', 81)">c</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips15', 82)" onmouseout="hideTip(event, 'fstips15', 82)">i</span>] <span class="o">&lt;</span> <span class="n">0.</span>) <span class="i" onmouseover="showTip(event, 'fstips28', 83)" onmouseout="hideTip(event, 'fstips28', 83)">In</span> <span class="k">with</span>
        | <span class="i" onmouseover="showTip(event, 'fstips45', 84)" onmouseout="hideTip(event, 'fstips45', 84)">Some</span> <span class="i" onmouseover="showTip(event, 'fstips46', 85)" onmouseout="hideTip(event, 'fstips46', 85)">jind</span> <span class="k">-&gt;</span>
            <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips47', 86)" onmouseout="hideTip(event, 'fstips47', 86)">j</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips28', 87)" onmouseout="hideTip(event, 'fstips28', 87)">In</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips46', 88)" onmouseout="hideTip(event, 'fstips46', 88)">jind</span>]
            <span class="c">//</span> <span class="c">3.</span> <span class="c">unboundness</span> <span class="c">check</span>
            <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips5', 89)" onmouseout="hideTip(event, 'fstips5', 89)">u</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips39', 90)" onmouseout="hideTip(event, 'fstips39', 90)">Binv</span> <span class="o">*</span> <span class="i" onmouseover="showTip(event, 'fstips33', 91)" onmouseout="hideTip(event, 'fstips33', 91)">A</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips34', 92)" onmouseout="hideTip(event, 'fstips34', 92)">Column</span> <span class="i" onmouseover="showTip(event, 'fstips47', 93)" onmouseout="hideTip(event, 'fstips47', 93)">j</span>
            <span class="k">if</span> <span class="i" onmouseover="showTip(event, 'fstips31', 94)" onmouseout="hideTip(event, 'fstips31', 94)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips48', 95)" onmouseout="hideTip(event, 'fstips48', 95)">forall</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips49', 96)" onmouseout="hideTip(event, 'fstips49', 96)">ui</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips49', 97)" onmouseout="hideTip(event, 'fstips49', 97)">ui</span> <span class="o">&lt;=</span> <span class="n">0.</span>) <span class="i" onmouseover="showTip(event, 'fstips5', 98)" onmouseout="hideTip(event, 'fstips5', 98)">u</span> <span class="k">then</span> <span class="i" onmouseover="showTip(event, 'fstips20', 99)" onmouseout="hideTip(event, 'fstips20', 99)">Error</span> <span class="s">"</span><span class="s">cost</span> <span class="s">unbounded</span><span class="s">"</span>
            <span class="k">else</span>
                <span class="c">//</span> <span class="c">4.</span> <span class="c">improvement</span>
                <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips8', 100)" onmouseout="hideTip(event, 'fstips8', 100)">l</span>, <span class="i" onmouseover="showTip(event, 'fstips50', 101)" onmouseout="hideTip(event, 'fstips50', 101)">theta</span> <span class="o">=</span>
                    <span class="i" onmouseover="showTip(event, 'fstips31', 102)" onmouseout="hideTip(event, 'fstips31', 102)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips51', 103)" onmouseout="hideTip(event, 'fstips51', 103)">mapi</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips15', 104)" onmouseout="hideTip(event, 'fstips15', 104)">i</span> <span class="i" onmouseover="showTip(event, 'fstips49', 105)" onmouseout="hideTip(event, 'fstips49', 105)">ui</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips15', 106)" onmouseout="hideTip(event, 'fstips15', 106)">i</span>, <span class="i" onmouseover="showTip(event, 'fstips26', 107)" onmouseout="hideTip(event, 'fstips26', 107)">x</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips15', 108)" onmouseout="hideTip(event, 'fstips15', 108)">i</span>] <span class="o">/</span> <span class="i" onmouseover="showTip(event, 'fstips49', 109)" onmouseout="hideTip(event, 'fstips49', 109)">ui</span>) <span class="i" onmouseover="showTip(event, 'fstips5', 110)" onmouseout="hideTip(event, 'fstips5', 110)">u</span>
                    <span class="o">|&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips31', 111)" onmouseout="hideTip(event, 'fstips31', 111)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips52', 112)" onmouseout="hideTip(event, 'fstips52', 112)">filter</span> (<span class="k">fun</span> (_, <span class="i" onmouseover="showTip(event, 'fstips53', 113)" onmouseout="hideTip(event, 'fstips53', 113)">di</span>) <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips53', 114)" onmouseout="hideTip(event, 'fstips53', 114)">di</span> <span class="o">&gt;</span> <span class="n">0.</span>)
                    <span class="o">|&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips31', 115)" onmouseout="hideTip(event, 'fstips31', 115)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips54', 116)" onmouseout="hideTip(event, 'fstips54', 116)">minBy</span> <span class="i" onmouseover="showTip(event, 'fstips55', 117)" onmouseout="hideTip(event, 'fstips55', 117)">snd</span>
               
                <span class="c">//</span> <span class="c">5.</span> <span class="c">update</span> <span class="c">solution</span>
                <span class="i" onmouseover="showTip(event, 'fstips26', 118)" onmouseout="hideTip(event, 'fstips26', 118)">x</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips42', 119)" onmouseout="hideTip(event, 'fstips42', 119)">MapIndexedInplace</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips15', 120)" onmouseout="hideTip(event, 'fstips15', 120)">i</span> <span class="i" onmouseover="showTip(event, 'fstips56', 121)" onmouseout="hideTip(event, 'fstips56', 121)">xi</span> <span class="k">-&gt;</span> <span class="k">if</span> <span class="i" onmouseover="showTip(event, 'fstips15', 122)" onmouseout="hideTip(event, 'fstips15', 122)">i</span><span class="o">=</span><span class="i" onmouseover="showTip(event, 'fstips8', 123)" onmouseout="hideTip(event, 'fstips8', 123)">l</span> <span class="k">then</span> <span class="i" onmouseover="showTip(event, 'fstips50', 124)" onmouseout="hideTip(event, 'fstips50', 124)">theta</span> <span class="k">else</span> <span class="i" onmouseover="showTip(event, 'fstips56', 125)" onmouseout="hideTip(event, 'fstips56', 125)">xi</span> <span class="o">-</span> <span class="i" onmouseover="showTip(event, 'fstips50', 126)" onmouseout="hideTip(event, 'fstips50', 126)">theta</span><span class="o">*</span><span class="i" onmouseover="showTip(event, 'fstips5', 127)" onmouseout="hideTip(event, 'fstips5', 127)">u</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips15', 128)" onmouseout="hideTip(event, 'fstips15', 128)">i</span>])

                <span class="c">//</span> <span class="c">6.</span> <span class="c">update</span> <span class="c">basis,</span> <span class="c">indices</span> <span class="c">and</span> <span class="c">cost</span>
                <span class="i" onmouseover="showTip(event, 'fstips30', 129)" onmouseout="hideTip(event, 'fstips30', 129)">B</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips57', 130)" onmouseout="hideTip(event, 'fstips57', 130)">SetColumn</span>(<span class="i" onmouseover="showTip(event, 'fstips8', 131)" onmouseout="hideTip(event, 'fstips8', 131)">l</span>, <span class="i" onmouseover="showTip(event, 'fstips33', 132)" onmouseout="hideTip(event, 'fstips33', 132)">A</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips34', 133)" onmouseout="hideTip(event, 'fstips34', 133)">Column</span> <span class="i" onmouseover="showTip(event, 'fstips47', 134)" onmouseout="hideTip(event, 'fstips47', 134)">j</span>)
                <span class="i" onmouseover="showTip(event, 'fstips28', 135)" onmouseout="hideTip(event, 'fstips28', 135)">In</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips46', 136)" onmouseout="hideTip(event, 'fstips46', 136)">jind</span>] <span class="o">&lt;-</span> <span class="i" onmouseover="showTip(event, 'fstips27', 137)" onmouseout="hideTip(event, 'fstips27', 137)">Ib</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips8', 138)" onmouseout="hideTip(event, 'fstips8', 138)">l</span>]
                <span class="i" onmouseover="showTip(event, 'fstips27', 139)" onmouseout="hideTip(event, 'fstips27', 139)">Ib</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips8', 140)" onmouseout="hideTip(event, 'fstips8', 140)">l</span>] <span class="o">&lt;-</span> <span class="i" onmouseover="showTip(event, 'fstips47', 141)" onmouseout="hideTip(event, 'fstips47', 141)">j</span>
                <span class="i" onmouseover="showTip(event, 'fstips41', 142)" onmouseout="hideTip(event, 'fstips41', 142)">cb</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips8', 143)" onmouseout="hideTip(event, 'fstips8', 143)">l</span>] <span class="o">&lt;-</span> <span class="i" onmouseover="showTip(event, 'fstips25', 144)" onmouseout="hideTip(event, 'fstips25', 144)">c</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips47', 145)" onmouseout="hideTip(event, 'fstips47', 145)">j</span>]

                <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips39', 146)" onmouseout="hideTip(event, 'fstips39', 146)">Binv</span> <span class="o">=</span> <span class="omitted" onmouseover="showTip(event, 'fstips58', 147)" onmouseout="hideTip(event, 'fstips58', 147)">inverse B</span>
                <span class="i" onmouseover="showTip(event, 'fstips38', 148)" onmouseout="hideTip(event, 'fstips38', 148)">calc</span> <span class="i" onmouseover="showTip(event, 'fstips39', 149)" onmouseout="hideTip(event, 'fstips39', 149)">Binv</span> (<span class="i" onmouseover="showTip(event, 'fstips9', 150)" onmouseout="hideTip(event, 'fstips9', 150)">iter</span> <span class="o">+</span> <span class="n">1</span>)
        | _ <span class="k">-&gt;</span> 
            <span class="c">//</span> <span class="c">fill</span> <span class="c">solution</span> <span class="c">vector</span> <span class="c">x0,</span> <span class="c">x1,</span> <span class="c">...,</span> <span class="c">xn</span>
            <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips59', 151)" onmouseout="hideTip(event, 'fstips59', 151)">res</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips60', 152)" onmouseout="hideTip(event, 'fstips60', 152)">DenseVector</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips61', 153)" onmouseout="hideTip(event, 'fstips61', 153)">zeroCreate</span> <span class="i" onmouseover="showTip(event, 'fstips62', 154)" onmouseout="hideTip(event, 'fstips62', 154)">n</span>
            <span class="i" onmouseover="showTip(event, 'fstips31', 155)" onmouseout="hideTip(event, 'fstips31', 155)">Seq</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips63', 156)" onmouseout="hideTip(event, 'fstips63', 156)">iteri</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips15', 157)" onmouseout="hideTip(event, 'fstips15', 157)">i</span> <span class="i" onmouseover="showTip(event, 'fstips64', 158)" onmouseout="hideTip(event, 'fstips64', 158)">ib</span> <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips59', 159)" onmouseout="hideTip(event, 'fstips59', 159)">res</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips64', 160)" onmouseout="hideTip(event, 'fstips64', 160)">ib</span>] <span class="o">&lt;-</span> <span class="i" onmouseover="showTip(event, 'fstips26', 161)" onmouseout="hideTip(event, 'fstips26', 161)">x</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips15', 162)" onmouseout="hideTip(event, 'fstips15', 162)">i</span>]) <span class="i" onmouseover="showTip(event, 'fstips27', 163)" onmouseout="hideTip(event, 'fstips27', 163)">Ib</span>
            <span class="i" onmouseover="showTip(event, 'fstips19', 164)" onmouseout="hideTip(event, 'fstips19', 164)">Success</span> <span class="i" onmouseover="showTip(event, 'fstips59', 165)" onmouseout="hideTip(event, 'fstips59', 165)">res</span>

    <span class="i" onmouseover="showTip(event, 'fstips38', 166)" onmouseout="hideTip(event, 'fstips38', 166)">calc</span> (<span class="i" onmouseover="showTip(event, 'fstips30', 167)" onmouseout="hideTip(event, 'fstips30', 167)">B</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips10', 168)" onmouseout="hideTip(event, 'fstips10', 168)">Inverse</span>()) <span class="n">1</span></pre></td>
</tr></table>


For simplicity we use the naïve initialization – all given variables become nonbasic, the slack ones – basic and their values are equal to constraints vector $$b$$. The full method returns the solution vector and cost function value:  

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
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="c">///</span> <span class="c">naive</span> <span class="c">initialization</span> <span class="c">function</span> <span class="c">-</span> <span class="c">simply</span> <span class="c">set</span> <span class="c">basic</span> <span class="c">x</span> <span class="c">to</span> <span class="c">b</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips65', 169)" onmouseout="hideTip(event, 'fstips65', 169)">initSimplex</span> (<span class="i" onmouseover="showTip(event, 'fstips66', 170)" onmouseout="hideTip(event, 'fstips66', 170)">A0</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips3', 171)" onmouseout="hideTip(event, 'fstips3', 171)">Matrix</span>) (<span class="i" onmouseover="showTip(event, 'fstips67', 172)" onmouseout="hideTip(event, 'fstips67', 172)">b0</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips7', 173)" onmouseout="hideTip(event, 'fstips7', 173)">Vector</span>) (<span class="i" onmouseover="showTip(event, 'fstips68', 174)" onmouseout="hideTip(event, 'fstips68', 174)">c0</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips7', 175)" onmouseout="hideTip(event, 'fstips7', 175)">Vector</span>) <span class="o">=</span> <span class="omitted" onmouseover="showTip(event, 'fstips69', 176)" onmouseout="hideTip(event, 'fstips69', 176)">(...)</span>

<span class="c">///</span> <span class="c">Revised</span> <span class="c">Simplex</span> <span class="c">implementation:</span> <span class="c">min</span> <span class="c">cx,</span> <span class="c">Ax</span> <span class="c">&lt;=</span> <span class="c">b,</span> <span class="c">x</span> <span class="c">&gt;=</span> <span class="c">0,</span> <span class="c">b</span> <span class="c">&gt;=</span> <span class="c">0</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips70', 177)" onmouseout="hideTip(event, 'fstips70', 177)">simplex</span> <span class="i" onmouseover="showTip(event, 'fstips66', 178)" onmouseout="hideTip(event, 'fstips66', 178)">A0</span> <span class="i" onmouseover="showTip(event, 'fstips67', 179)" onmouseout="hideTip(event, 'fstips67', 179)">b0</span> (<span class="i" onmouseover="showTip(event, 'fstips68', 180)" onmouseout="hideTip(event, 'fstips68', 180)">c0</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips7', 181)" onmouseout="hideTip(event, 'fstips7', 181)">Vector</span>) <span class="o">=</span>
    <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips71', 182)" onmouseout="hideTip(event, 'fstips71', 182)">A</span>, <span class="i" onmouseover="showTip(event, 'fstips72', 183)" onmouseout="hideTip(event, 'fstips72', 183)">b</span>, <span class="i" onmouseover="showTip(event, 'fstips73', 184)" onmouseout="hideTip(event, 'fstips73', 184)">c</span>, <span class="i" onmouseover="showTip(event, 'fstips74', 185)" onmouseout="hideTip(event, 'fstips74', 185)">x</span>, <span class="i" onmouseover="showTip(event, 'fstips27', 186)" onmouseout="hideTip(event, 'fstips27', 186)">Ib</span>, <span class="i" onmouseover="showTip(event, 'fstips28', 187)" onmouseout="hideTip(event, 'fstips28', 187)">In</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips65', 188)" onmouseout="hideTip(event, 'fstips65', 188)">initSimplex</span> <span class="i" onmouseover="showTip(event, 'fstips66', 189)" onmouseout="hideTip(event, 'fstips66', 189)">A0</span> <span class="i" onmouseover="showTip(event, 'fstips67', 190)" onmouseout="hideTip(event, 'fstips67', 190)">b0</span> <span class="i" onmouseover="showTip(event, 'fstips68', 191)" onmouseout="hideTip(event, 'fstips68', 191)">c0</span>
    <span class="k">match</span> <span class="i" onmouseover="showTip(event, 'fstips22', 192)" onmouseout="hideTip(event, 'fstips22', 192)">simplexImpl</span> <span class="i" onmouseover="showTip(event, 'fstips71', 193)" onmouseout="hideTip(event, 'fstips71', 193)">A</span> <span class="i" onmouseover="showTip(event, 'fstips72', 194)" onmouseout="hideTip(event, 'fstips72', 194)">b</span> <span class="i" onmouseover="showTip(event, 'fstips73', 195)" onmouseout="hideTip(event, 'fstips73', 195)">c</span> (<span class="i" onmouseover="showTip(event, 'fstips74', 196)" onmouseout="hideTip(event, 'fstips74', 196)">x</span>, <span class="i" onmouseover="showTip(event, 'fstips27', 197)" onmouseout="hideTip(event, 'fstips27', 197)">Ib</span>, <span class="i" onmouseover="showTip(event, 'fstips28', 198)" onmouseout="hideTip(event, 'fstips28', 198)">In</span>) <span class="k">with</span>
    | <span class="i" onmouseover="showTip(event, 'fstips19', 199)" onmouseout="hideTip(event, 'fstips19', 199)">Success</span> <span class="i" onmouseover="showTip(event, 'fstips75', 200)" onmouseout="hideTip(event, 'fstips75', 200)">xs</span> <span class="k">-&gt;</span> 
        <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips76', 201)" onmouseout="hideTip(event, 'fstips76', 201)">x0</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips75', 202)" onmouseout="hideTip(event, 'fstips75', 202)">xs</span><span class="o">.</span>[ <span class="o">..</span> <span class="i" onmouseover="showTip(event, 'fstips68', 203)" onmouseout="hideTip(event, 'fstips68', 203)">c0</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips77', 204)" onmouseout="hideTip(event, 'fstips77', 204)">Count</span><span class="o">-</span><span class="n">1</span>]
        <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips78', 205)" onmouseout="hideTip(event, 'fstips78', 205)">cx</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips68', 206)" onmouseout="hideTip(event, 'fstips68', 206)">c0</span> <span class="o">*</span> <span class="i" onmouseover="showTip(event, 'fstips76', 207)" onmouseout="hideTip(event, 'fstips76', 207)">x0</span>
        <span class="i" onmouseover="showTip(event, 'fstips45', 208)" onmouseout="hideTip(event, 'fstips45', 208)">Some</span> (<span class="i" onmouseover="showTip(event, 'fstips76', 209)" onmouseout="hideTip(event, 'fstips76', 209)">x0</span>, <span class="i" onmouseover="showTip(event, 'fstips78', 210)" onmouseout="hideTip(event, 'fstips78', 210)">cx</span>)
    | _ <span class="k">-&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips79', 211)" onmouseout="hideTip(event, 'fstips79', 211)">None</span></pre></td>
</tr></table>

And now time for my favorite part of the method and one the best ways to improve performance – reuse the data you already have to avoid recomputations whenever it’s possible. In this case it’s about inverting the basis matrix. We know that $latex B$ at the next iteration is the same as current, except one column. There’s also the current $$B^{-1}$$. The naïve simplex + math = revised simplex method.  

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
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="c">///</span> <span class="c">invert</span> <span class="c">matrix</span> <span class="c">given</span> <span class="c">inverse</span> <span class="c">of</span> <span class="c">one</span> <span class="c">column</span> <span class="c">different</span> <span class="c">matrix</span>
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips1', 1)" onmouseout="hideTip(event, 'fstips1', 1)">inv</span> (<span class="i" onmouseover="showTip(event, 'fstips2', 2)" onmouseout="hideTip(event, 'fstips2', 2)">a</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips3', 3)" onmouseout="hideTip(event, 'fstips3', 3)">Matrix</span>) (<span class="i" onmouseover="showTip(event, 'fstips4', 4)" onmouseout="hideTip(event, 'fstips4', 4)">aprev</span><span class="o">:</span> _ <span class="i" onmouseover="showTip(event, 'fstips3', 5)" onmouseout="hideTip(event, 'fstips3', 5)">Matrix</span>, <span class="i" onmouseover="showTip(event, 'fstips5', 6)" onmouseout="hideTip(event, 'fstips5', 6)">u</span><span class="o">:</span> <span class="i" onmouseover="showTip(event, 'fstips6', 7)" onmouseout="hideTip(event, 'fstips6', 7)">float</span> <span class="i" onmouseover="showTip(event, 'fstips7', 8)" onmouseout="hideTip(event, 'fstips7', 8)">Vector</span>, <span class="i" onmouseover="showTip(event, 'fstips8', 9)" onmouseout="hideTip(event, 'fstips8', 9)">l</span>) <span class="i" onmouseover="showTip(event, 'fstips9', 10)" onmouseout="hideTip(event, 'fstips9', 10)">iter</span> <span class="o">=</span>
    <span class="c">//</span> <span class="c">recompute</span> <span class="c">from</span> <span class="c">scratch,</span> <span class="c">because</span> <span class="c">errors</span> <span class="c">accumulate</span>
    <span class="k">if</span> <span class="i" onmouseover="showTip(event, 'fstips9', 11)" onmouseout="hideTip(event, 'fstips9', 11)">iter</span> <span class="o">%</span> <span class="n">20</span> <span class="o">=</span> <span class="n">0</span> <span class="k">then</span> <span class="i" onmouseover="showTip(event, 'fstips2', 12)" onmouseout="hideTip(event, 'fstips2', 12)">a</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips10', 13)" onmouseout="hideTip(event, 'fstips10', 13)">Inverse</span>() 
    <span class="k">else</span>
        <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips11', 14)" onmouseout="hideTip(event, 'fstips11', 14)">ul</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips5', 15)" onmouseout="hideTip(event, 'fstips5', 15)">u</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips8', 16)" onmouseout="hideTip(event, 'fstips8', 16)">l</span>]
        <span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips12', 17)" onmouseout="hideTip(event, 'fstips12', 17)">lrow</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips4', 18)" onmouseout="hideTip(event, 'fstips4', 18)">aprev</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips13', 19)" onmouseout="hideTip(event, 'fstips13', 19)">Row</span> <span class="i" onmouseover="showTip(event, 'fstips8', 20)" onmouseout="hideTip(event, 'fstips8', 20)">l</span>
        <span class="i" onmouseover="showTip(event, 'fstips3', 21)" onmouseout="hideTip(event, 'fstips3', 21)">Matrix</span><span class="o">.</span><span class="i" onmouseover="showTip(event, 'fstips14', 22)" onmouseout="hideTip(event, 'fstips14', 22)">mapRows</span> (<span class="k">fun</span> <span class="i" onmouseover="showTip(event, 'fstips15', 23)" onmouseout="hideTip(event, 'fstips15', 23)">i</span> <span class="i" onmouseover="showTip(event, 'fstips16', 24)" onmouseout="hideTip(event, 'fstips16', 24)">row</span> <span class="k">-&gt;</span> 
            <span class="k">if</span> <span class="i" onmouseover="showTip(event, 'fstips15', 25)" onmouseout="hideTip(event, 'fstips15', 25)">i</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips8', 26)" onmouseout="hideTip(event, 'fstips8', 26)">l</span> <span class="k">then</span> <span class="i" onmouseover="showTip(event, 'fstips16', 27)" onmouseout="hideTip(event, 'fstips16', 27)">row</span> <span class="o">/</span> <span class="i" onmouseover="showTip(event, 'fstips11', 28)" onmouseout="hideTip(event, 'fstips11', 28)">ul</span> <span class="k">else</span> <span class="i" onmouseover="showTip(event, 'fstips16', 29)" onmouseout="hideTip(event, 'fstips16', 29)">row</span> <span class="o">-</span> <span class="i" onmouseover="showTip(event, 'fstips12', 30)" onmouseout="hideTip(event, 'fstips12', 30)">lrow</span> <span class="o">*</span> <span class="i" onmouseover="showTip(event, 'fstips5', 31)" onmouseout="hideTip(event, 'fstips5', 31)">u</span><span class="o">.</span>[<span class="i" onmouseover="showTip(event, 'fstips15', 32)" onmouseout="hideTip(event, 'fstips15', 32)">i</span>] <span class="o">/</span> <span class="i" onmouseover="showTip(event, 'fstips11', 33)" onmouseout="hideTip(event, 'fstips11', 33)">ul</span>) <span class="i" onmouseover="showTip(event, 'fstips4', 34)" onmouseout="hideTip(event, 'fstips4', 34)">aprev</span></pre></td>
</tr></table>

### Sample

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
</pre></td>
<td class="snippet"><pre class="fssnip">
<span class="omitted" onmouseover="showTip(event, 'fstips80', 212)" onmouseout="hideTip(event, 'fstips80', 212)">standard problem form</span>   
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips81', 213)" onmouseout="hideTip(event, 'fstips81', 213)">A</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips82', 214)" onmouseout="hideTip(event, 'fstips82', 214)">matrix</span> [[<span class="n">1.</span>; <span class="n">1.</span>; <span class="n">1.</span>]
                [<span class="n">1.</span>; <span class="n">0.</span>; <span class="n">0.</span>]
                [<span class="n">0.</span>; <span class="n">0.</span>; <span class="n">1.</span>]
                [<span class="n">0.</span>; <span class="n">3.</span>; <span class="n">1.</span>]]

<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips83', 215)" onmouseout="hideTip(event, 'fstips83', 215)">c</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips84', 216)" onmouseout="hideTip(event, 'fstips84', 216)">vector</span> [<span class="n">1.</span>; <span class="n">5.</span>; <span class="o">-</span><span class="n">2.</span>]
<span class="k">let</span> <span class="i" onmouseover="showTip(event, 'fstips85', 217)" onmouseout="hideTip(event, 'fstips85', 217)">b</span> <span class="o">=</span> <span class="i" onmouseover="showTip(event, 'fstips84', 218)" onmouseout="hideTip(event, 'fstips84', 218)">vector</span> [<span class="n">4.</span>; <span class="n">2.</span>; <span class="n">3.</span>; <span class="n">6.</span>]
    
<span class="i" onmouseover="showTip(event, 'fstips70', 219)" onmouseout="hideTip(event, 'fstips70', 219)">simplex</span> <span class="i" onmouseover="showTip(event, 'fstips81', 220)" onmouseout="hideTip(event, 'fstips81', 220)">A</span> <span class="i" onmouseover="showTip(event, 'fstips85', 221)" onmouseout="hideTip(event, 'fstips85', 221)">b</span> <span class="i" onmouseover="showTip(event, 'fstips83', 222)" onmouseout="hideTip(event, 'fstips83', 222)">c</span> <span class="o">|&gt;</span> <span class="i" onmouseover="showTip(event, 'fstips86', 223)" onmouseout="hideTip(event, 'fstips86', 223)">printfn</span> <span class="s">"</span><span class="s">%</span><span class="s">A</span><span class="s">"</span> <span class="c">//</span><span class="c">[0.0;</span><span class="c">0.0;</span><span class="c">3.0],</span><span class="c">-6.0</span></pre></td>
</tr></table>  

Complete snippet version is available at <a title="Simplex method" href="https://github.com/luajalla/snippets/blob/master/Simplex.fsx" target="_blank">github.</a>  



### Summing up  

- mutability may be evil, but sometimes it’s a way to go;  
- reusing the computation results and the parts of datastructures ftw.  

### What to look at
- <a title="MS Solver Foundation" href="http://msdn.microsoft.com/en-us/devlabs/hh145003" target="_blank">MS Solver Foundation</a> – quite a nice tool to check too (with msi downloads, but I tried it a long time ago with mono – and it worked).  
- some theory: Introduction to Linear Optimization book (Dimitris Bertsimas and John N. Tsitsiklis) or anything else.  
<p>&nbsp;</p>
<p>&nbsp;</p>

[^1]: one of the side-effects of my work are numerous spreadsheets, and Solver proved to be incredibly helpful.

<div class="tip" id="fstips1">
<p>val inv : Matrix&lt;float&gt; -&gt; Matrix&lt;float&gt; * Vector&lt;float&gt; * int -&gt; int -&gt; Matrix&lt;float&gt;</p>
<p>Full name: Simplex2.inv</p>
<p><em>invert matrix given inverse of one column different matrix</em></p>
</div>
<div class="tip" id="fstips2">
<p>val a : Matrix&lt;float&gt;</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips3">
<p>Multiple items</p>
<p>type Matrix&lt;'T (requires default constructor and value type and 'T :&gt; System.ValueType and 'T :&gt; System.IEquatable&lt;'T&gt; and 'T :&gt; System.IFormattable)&gt; =<br />
class<br />
member Add : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Add : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Append : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Append : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member At : int * int -&gt; 'T<br />
member At : int * int * 'T -&gt; unit<br />
member Clear : unit -&gt; unit<br />
member ClearColumn : int -&gt; unit<br />
member ClearRow : int -&gt; unit<br />
member ClearSubMatrix : int * int * int * int -&gt; unit<br />
member Clone : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Column : int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Column : int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Column : int * int * int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Column : int * int * int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member ColumnCount : int with get, set<br />
member ColumnEnumerator : unit -&gt; System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;&gt;&gt;<br />
member ColumnEnumerator : int * int -&gt; System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;&gt;&gt;<br />
member ConditionNumber : unit -&gt; 'T<br />
member Conjugate : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Conjugate : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member ConjugateTranspose : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member CopyTo : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member CreateMatrix : int * int * bool -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member CreateVector : int * bool -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Determinant : unit -&gt; 'T<br />
member Diagonal : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member DiagonalStack : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member DiagonalStack : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Divide : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Divide : 'T * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Equals : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; bool<br />
member Equals : obj -&gt; bool<br />
member FrobeniusNorm : unit -&gt; 'T<br />
member GetHashCode : unit -&gt; int<br />
member IndexedEnumerator : unit -&gt; System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,int,'T&gt;&gt;<br />
member InfinityNorm : unit -&gt; 'T<br />
member InsertColumn : int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member InsertRow : int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Inverse : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member IsSymmetric : bool<br />
member Item : int * int -&gt; 'T with get, set<br />
member KroneckerProduct : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member KroneckerProduct : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member L1Norm : unit -&gt; 'T<br />
member L2Norm : unit -&gt; 'T<br />
member LeftMultiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member LeftMultiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member LowerTriangle : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member LowerTriangle : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member MapIndexedInplace : System.Func&lt;int,int,'T,'T&gt; * bool -&gt; unit<br />
member MapInplace : System.Func&lt;'T,'T&gt; * bool -&gt; unit<br />
member Modulus : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Modulus : 'T * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Multiply : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Multiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Multiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Multiply : 'T * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Multiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Multiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Negate : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Negate : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member NormalizeColumns : int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member NormalizeRows : int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member PermuteColumns : MathNet.Numerics.Permutation -&gt; unit<br />
member PermuteRows : MathNet.Numerics.Permutation -&gt; unit<br />
member PointwiseDivide : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member PointwiseDivide : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member PointwiseMultiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member PointwiseMultiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Rank : unit -&gt; int<br />
member Row : int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Row : int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Row : int * int * int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Row : int * int * int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member RowCount : int with get, set<br />
member RowEnumerator : unit -&gt; System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;&gt;&gt;<br />
member RowEnumerator : int * int -&gt; System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;&gt;&gt;<br />
member SetColumn : int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member SetColumn : int * 'T [] -&gt; unit<br />
member SetDiagonal : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member SetDiagonal : 'T [] -&gt; unit<br />
member SetRow : int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member SetRow : int * 'T [] -&gt; unit<br />
member SetSubMatrix : int * int * int * int * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Stack : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Stack : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member Storage : MathNet.Numerics.LinearAlgebra.Storage.MatrixStorage&lt;'T&gt; with get, set<br />
member StrictlyLowerTriangle : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member StrictlyLowerTriangle : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member StrictlyUpperTriangle : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member StrictlyUpperTriangle : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member SubMatrix : int * int * int * int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Subtract : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Subtract : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member ToArray : unit -&gt; 'T [,]<br />
member ToColumnWiseArray : unit -&gt; 'T []<br />
member ToMatrixString : int * int * System.IFormatProvider -&gt; string<br />
member ToMatrixString : int * int * int * string * System.IFormatProvider -&gt; string<br />
member ToRowWiseArray : unit -&gt; 'T []<br />
member ToString : unit -&gt; string<br />
member ToString : string * System.IFormatProvider -&gt; string<br />
member ToString : int * int * System.IFormatProvider -&gt; string<br />
member ToTypeString : unit -&gt; string<br />
member Trace : unit -&gt; 'T<br />
member Transpose : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member TransposeAndMultiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member TransposeAndMultiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member TransposeThisAndMultiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member TransposeThisAndMultiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member TransposeThisAndMultiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member TransposeThisAndMultiply : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
member UpperTriangle : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member UpperTriangle : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; unit<br />
static member CreateFromColumns : System.Collections.Generic.IList&lt;MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
static member CreateFromRows : System.Collections.Generic.IList&lt;MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; -&gt; System.Exception<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * string -&gt; System.Exception<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * string -&gt; System.Exception<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * string -&gt; System.Exception<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * string -&gt; System.Exception<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * string -&gt; System.Exception<br />
static member DimensionsDontMatch&lt;'TException&gt; : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * string -&gt; System.Exception<br />
endFull name: MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;_&gt;</p>
<p>type: Matrix&lt;'T&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;'T&gt;&gt;<br />
implements: System.ICloneable</p>
<p>--------------------</p>
<p>type Matrix =<br />
class<br />
inherit MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt;<br />
member ConjugateTranspose : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt;<br />
member FrobeniusNorm : unit -&gt; float<br />
member InfinityNorm : unit -&gt; float<br />
member L1Norm : unit -&gt; float<br />
member Trace : unit -&gt; float<br />
end</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.Matrix</p>
<p>type: Matrix<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable<br />
inherits: Matrix&lt;float&gt;</p>
</div>
<div class="tip" id="fstips4">
<p>val aprev : Matrix&lt;float&gt;</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips5">
<p>val u : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips6">
<p>Multiple items</p>
<p>val float : 'T -&gt; float (requires member op_Explicit)Full name: Microsoft.FSharp.Core.Operators.float</p>
<p>--------------------</p>
<p>type float&lt;'Measure&gt; = float</p>
<p>Full name: Microsoft.FSharp.Core.float&lt;_&gt;</p>
<p>type: float&lt;'Measure&gt;<br />
implements: System.IComparable<br />
implements: System.IConvertible<br />
implements: System.IFormattable<br />
implements: System.IComparable&lt;float&lt;'Measure&gt;&gt;<br />
implements: System.IEquatable&lt;float&lt;'Measure&gt;&gt;<br />
inherits: System.ValueType</p>
<p>--------------------</p>
<p>type float = System.Double</p>
<p>Full name: Microsoft.FSharp.Core.float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips7">
<p>Multiple items</p>
<p>type Vector&lt;'T (requires default constructor and value type and 'T :&gt; System.ValueType and 'T :&gt; System.IEquatable&lt;'T&gt; and 'T :&gt; System.IFormattable)&gt; =<br />
class<br />
member AbsoluteMaximum : unit -&gt; 'T<br />
member AbsoluteMaximumIndex : unit -&gt; int<br />
member AbsoluteMinimum : unit -&gt; 'T<br />
member AbsoluteMinimumIndex : unit -&gt; int<br />
member Add : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Add : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Add : 'T * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Add : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member At : int -&gt; 'T<br />
member At : int * 'T -&gt; unit<br />
member Clear : unit -&gt; unit<br />
member ClearSubVector : int * int -&gt; unit<br />
member Clone : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Conjugate : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Conjugate : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member CopySubVectorTo : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * int * int * int -&gt; unit<br />
member CopyTo : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member CopyTo : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * int * int * int -&gt; unit<br />
member Count : int with get, set<br />
member CreateMatrix : int * int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member CreateVector : int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Divide : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Divide : 'T * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member DotProduct : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; 'T<br />
member Equals : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; bool<br />
member Equals : obj -&gt; bool<br />
member GetEnumerator : unit -&gt; System.Collections.Generic.IEnumerator&lt;'T&gt;<br />
member GetHashCode : unit -&gt; int<br />
member GetIndexedEnumerator : unit -&gt; System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,'T&gt;&gt;<br />
member Item : int -&gt; 'T with get, set<br />
member MapIndexedInplace : System.Func&lt;int,'T,'T&gt; * bool -&gt; unit<br />
member MapInplace : System.Func&lt;'T,'T&gt; * bool -&gt; unit<br />
member Maximum : unit -&gt; 'T<br />
member MaximumIndex : unit -&gt; int<br />
member Minimum : unit -&gt; 'T<br />
member MinimumIndex : unit -&gt; int<br />
member Modulus : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Modulus : 'T * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Multiply : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Multiply : 'T * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Negate : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Negate : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Norm : float -&gt; 'T<br />
member Normalize : float -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member OuterProduct : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member Plus : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member PointwiseDivide : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member PointwiseDivide : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member PointwiseMultiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member PointwiseMultiply : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member SetSubVector : int * int * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member SetValues : 'T [] -&gt; unit<br />
member Storage : MathNet.Numerics.LinearAlgebra.Storage.VectorStorage&lt;'T&gt; with get, set<br />
member SubVector : int * int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Subtract : 'T -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Subtract : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt;<br />
member Subtract : 'T * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Subtract : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; unit<br />
member Sum : unit -&gt; 'T<br />
member SumMagnitudes : unit -&gt; 'T<br />
member ToArray : unit -&gt; 'T []<br />
member ToColumnMatrix : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member ToRowMatrix : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
member ToString : unit -&gt; string<br />
member ToString : System.IFormatProvider -&gt; string<br />
member ToString : string * System.IFormatProvider -&gt; string<br />
member ToString : int * int * System.IFormatProvider -&gt; string<br />
member ToTypeString : unit -&gt; string<br />
member ToVectorString : int * int * System.IFormatProvider -&gt; string<br />
member ToVectorString : int * int * int * string * System.IFormatProvider -&gt; string<br />
static member OuterProduct : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; * MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;'T&gt; -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;'T&gt;<br />
endFull name: MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;_&gt;</p>
<p>type: Vector&lt;'T&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;'T&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;'T&gt;<br />
implements: System.Collections.Generic.ICollection&lt;'T&gt;<br />
implements: seq&lt;'T&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
<p>--------------------</p>
<p>type Vector =<br />
class<br />
inherit MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;float&gt;<br />
member AbsoluteMaximum : unit -&gt; float<br />
member AbsoluteMaximumIndex : unit -&gt; int<br />
member AbsoluteMinimum : unit -&gt; float<br />
member AbsoluteMinimumIndex : unit -&gt; int<br />
member MaximumIndex : unit -&gt; int<br />
member MinimumIndex : unit -&gt; int<br />
member Norm : float -&gt; float<br />
member Normalize : float -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;float&gt;<br />
member Sum : unit -&gt; float<br />
member SumMagnitudes : unit -&gt; float<br />
end</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.Vector</p>
<p>type: Vector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips8">
<p>val l : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips9">
<p>val iter : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips10">Matrix.Inverse() : Matrix&lt;float&gt;</div>
<div class="tip" id="fstips11">
<p>val ul : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips12">
<p>val lrow : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips13">
<p>Multiple overloads</p>
<p>Matrix.Row(index: int) : Vector&lt;float&gt;</p>
<p>Matrix.Row(index: int, result: Vector&lt;float&gt;) : unit</p>
<p>Matrix.Row(rowIndex: int, columnIndex: int, length: int) : Vector&lt;float&gt;</p>
<p>Matrix.Row(rowIndex: int, columnIndex: int, length: int, result: Vector&lt;float&gt;) : unit</p>
</div>
<div class="tip" id="fstips14">
<p>val mapRows : (int -&gt; Vector&lt;float&gt; -&gt; Vector&lt;float&gt;) -&gt; #Matrix&lt;float&gt; -&gt; Matrix&lt;float&gt;</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.Matrix.mapRows</p>
</div>
<div class="tip" id="fstips15">
<p>val i : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips16">
<p>val row : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips17">
<p>#r "MathNet.Numerics.dll"<br />
#r "MathNet.Numerics.FSharp.dll"</p>
<p>open MathNet.Numerics.LinearAlgebra.Double<br />
open MathNet.Numerics.LinearAlgebra.Generic</p>
</div>
<div class="tip" id="fstips18">
<p>type SimplexResult =<br />
| Success of Vector&lt;float&gt;<br />
| Error of string</p>
<p>Full name: Simplex2.SimplexResult</p>
<p>type: SimplexResult<br />
implements: System.IEquatable&lt;SimplexResult&gt;<br />
implements: System.Collections.IStructuralEquatable</p>
</div>
<div class="tip" id="fstips19">union case SimplexResult.Success: Vector&lt;float&gt; -&gt; SimplexResult</div>
<div class="tip" id="fstips20">union case SimplexResult.Error: string -&gt; SimplexResult</div>
<div class="tip" id="fstips21">
<p>Multiple items</p>
<p>val string : 'T -&gt; stringFull name: Microsoft.FSharp.Core.Operators.string</p>
<p>--------------------</p>
<p>type string = System.String</p>
<p>Full name: Microsoft.FSharp.Core.string</p>
<p>type: string<br />
implements: System.IComparable<br />
implements: System.ICloneable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;string&gt;<br />
implements: seq&lt;char&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.IEquatable&lt;string&gt;</p>
</div>
<div class="tip" id="fstips22">
<p>val simplexImpl : Matrix&lt;float&gt; -&gt; 'a -&gt; Vector&lt;float&gt; -&gt; Vector&lt;float&gt; * int [] * int [] -&gt; SimplexResult</p>
<p>Full name: Simplex2.simplexImpl</p>
<p><em>Simplex method implementation</em></p>
</div>
<div class="tip" id="fstips23">
<p>Multiple items</p>
<p>val A : Matrix&lt;float&gt; type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
<p>--------------------</p>
<p>val A : Matrix&lt;float&gt;</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips24">val b : 'a</div>
<div class="tip" id="fstips25">
<p>val c : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips26">
<p>val x : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips27">
<p>val Ib : int []</p>
<p>type: int []<br />
implements: System.ICloneable<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.IStructuralComparable<br />
implements: System.Collections.IStructuralEquatable<br />
implements: System.Collections.Generic.IList&lt;int&gt;<br />
implements: System.Collections.Generic.ICollection&lt;int&gt;<br />
implements: seq&lt;int&gt;<br />
implements: System.Collections.IEnumerable<br />
inherits: System.Array</p>
</div>
<div class="tip" id="fstips28">
<p>val In : int []</p>
<p>type: int []<br />
implements: System.ICloneable<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.IStructuralComparable<br />
implements: System.Collections.IStructuralEquatable<br />
implements: System.Collections.Generic.IList&lt;int&gt;<br />
implements: System.Collections.Generic.ICollection&lt;int&gt;<br />
implements: seq&lt;int&gt;<br />
implements: System.Collections.IEnumerable<br />
inherits: System.Array</p>
</div>
<div class="tip" id="fstips29">let cb = Seq.map c.At Ib |&gt; DenseVector.ofSeq<br />
let m, n = A.RowCount, A.ColumnCount</div>
<div class="tip" id="fstips30">
<p>val B : DenseMatrix</p>
<p>type: DenseMatrix<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable<br />
inherits: Matrix<br />
inherits: Matrix&lt;float&gt;</p>
</div>
<div class="tip" id="fstips31">
<p>module Seq</p>
<p>from Microsoft.FSharp.Collections</p>
</div>
<div class="tip" id="fstips32">
<p>val map : ('T -&gt; 'U) -&gt; seq&lt;'T&gt; -&gt; seq&lt;'U&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.map</p>
</div>
<div class="tip" id="fstips33">
<p>val A : Matrix&lt;float&gt;</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips34">
<p>Multiple overloads</p>
<p>Matrix.Column(index: int) : Vector&lt;float&gt;</p>
<p>Matrix.Column(index: int, result: Vector&lt;float&gt;) : unit</p>
<p>Matrix.Column(columnIndex: int, rowIndex: int, length: int) : Vector&lt;float&gt;</p>
<p>Matrix.Column(columnIndex: int, rowIndex: int, length: int, result: Vector&lt;float&gt;) : unit</p>
</div>
<div class="tip" id="fstips35">
<p>type DenseMatrix =<br />
class<br />
inherit MathNet.Numerics.LinearAlgebra.Double.Matrix<br />
new : MathNet.Numerics.LinearAlgebra.Storage.DenseColumnMajorMatrixStorage&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
new : int -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
new : int * int -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
new : int * int * float [] -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
new : int * int * float -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
new : float [,] -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
new : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
member CreateMatrix : int * int * bool -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt;<br />
member CreateVector : int * bool -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;float&gt;<br />
member Data : float []<br />
member FrobeniusNorm : unit -&gt; float<br />
member InfinityNorm : unit -&gt; float<br />
member L1Norm : unit -&gt; float<br />
member Trace : unit -&gt; float<br />
member Transpose : unit -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt;<br />
member Values : float []<br />
static member Create : int * int * System.Func&lt;int,int,float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member CreateRandom : int * int * MathNet.Numerics.Distributions.IContinuousDistribution -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member Identity : int -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfArray : float [,] -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfColumnMajor : int * int * System.Collections.Generic.IEnumerable&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfColumns : int * int * System.Collections.Generic.IEnumerable&lt;System.Collections.Generic.IEnumerable&lt;float&gt;&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfColumnsCovariant&lt;'TColumn&gt; : int * int * System.Collections.Generic.IEnumerable&lt;'TColumn&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfIndexed : int * int * System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,int,float&gt;&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfMatrix : MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfRows : int * int * System.Collections.Generic.IEnumerable&lt;System.Collections.Generic.IEnumerable&lt;float&gt;&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member OfRowsCovariant&lt;'TRow&gt; : int * int * System.Collections.Generic.IEnumerable&lt;'TRow&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
end</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.DenseMatrix</p>
<p>type: DenseMatrix<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable<br />
inherits: Matrix<br />
inherits: Matrix&lt;float&gt;</p>
</div>
<div class="tip" id="fstips36">
<p>val ofColumns : int -&gt; int -&gt; #seq&lt;'b&gt; -&gt; DenseMatrix (requires 'b :&gt; seq&lt;float&gt;)</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.DenseMatrix.ofColumns</p>
</div>
<div class="tip" id="fstips37">
<p>val m : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips38">val calc : (Matrix&lt;float&gt; -&gt; int -&gt; SimplexResult)</div>
<div class="tip" id="fstips39">
<p>val Binv : Matrix&lt;float&gt;</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips40">
<p>val p : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips41">
<p>val cb : DenseVector</p>
<p>type: DenseVector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips42">Vector.MapIndexedInplace(f: System.Func&lt;int,float,float&gt;, forceMapZeros: bool) : unit</div>
<div class="tip" id="fstips43">
<p>val ci : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips44">
<p>val tryFindIndex : ('T -&gt; bool) -&gt; seq&lt;'T&gt; -&gt; int option</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.tryFindIndex</p>
</div>
<div class="tip" id="fstips45">union case Option.Some: 'T -&gt; Option&lt;'T&gt;</div>
<div class="tip" id="fstips46">
<p>val jind : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips47">
<p>val j : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips48">
<p>val forall : ('T -&gt; bool) -&gt; seq&lt;'T&gt; -&gt; bool</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.forall</p>
</div>
<div class="tip" id="fstips49">
<p>val ui : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips50">
<p>val theta : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips51">
<p>val mapi : (int -&gt; 'T -&gt; 'U) -&gt; seq&lt;'T&gt; -&gt; seq&lt;'U&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.mapi</p>
</div>
<div class="tip" id="fstips52">
<p>val filter : ('T -&gt; bool) -&gt; seq&lt;'T&gt; -&gt; seq&lt;'T&gt;</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.filter</p>
</div>
<div class="tip" id="fstips53">
<p>val di : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips54">
<p>val minBy : ('T -&gt; 'U) -&gt; seq&lt;'T&gt; -&gt; 'T (requires comparison)</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.minBy</p>
</div>
<div class="tip" id="fstips55">
<p>val snd : ('T1 * 'T2) -&gt; 'T2</p>
<p>Full name: Microsoft.FSharp.Core.Operators.snd</p>
</div>
<div class="tip" id="fstips56">
<p>val xi : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips57">
<p>Multiple overloads</p>
<p>Matrix.SetColumn(columnIndex: int, column: float []) : unit</p>
<p>Matrix.SetColumn(columnIndex: int, column: Vector&lt;float&gt;) : unit</p>
</div>
<div class="tip" id="fstips58">inv B (Binv, u, l) iter</div>
<div class="tip" id="fstips59">
<p>val res : DenseVector</p>
<p>type: DenseVector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips60">
<p>type DenseVector =<br />
class<br />
inherit MathNet.Numerics.LinearAlgebra.Double.Vector<br />
new : MathNet.Numerics.LinearAlgebra.Storage.DenseVectorStorage&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
new : int -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
new : float [] -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
new : int * float -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
new : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
new : System.Collections.Generic.IEnumerable&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
member AbsoluteMaximum : unit -&gt; float<br />
member AbsoluteMaximumIndex : unit -&gt; int<br />
member AbsoluteMinimum : unit -&gt; float<br />
member AbsoluteMinimumIndex : unit -&gt; int<br />
member CreateMatrix : int * int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt;<br />
member CreateVector : int -&gt; MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;float&gt;<br />
member MaximumIndex : unit -&gt; int<br />
member MinimumIndex : unit -&gt; int<br />
member Norm : float -&gt; float<br />
member OuterProduct : MathNet.Numerics.LinearAlgebra.Double.DenseVector -&gt; MathNet.Numerics.LinearAlgebra.Generic.Matrix&lt;float&gt;<br />
member Sum : unit -&gt; float<br />
member SumMagnitudes : unit -&gt; float<br />
member Values : float []<br />
static member Create : int * System.Func&lt;int,float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member CreateRandom : int * MathNet.Numerics.Distributions.IContinuousDistribution -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member OfEnumerable : System.Collections.Generic.IEnumerable&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member OfIndexedEnumerable : int * System.Collections.Generic.IEnumerable&lt;System.Tuple&lt;int,float&gt;&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member OfVector : MathNet.Numerics.LinearAlgebra.Generic.Vector&lt;float&gt; -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member OuterProduct : MathNet.Numerics.LinearAlgebra.Double.DenseVector * MathNet.Numerics.LinearAlgebra.Double.DenseVector -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseMatrix<br />
static member Parse : string -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member Parse : string * System.IFormatProvider -&gt; MathNet.Numerics.LinearAlgebra.Double.DenseVector<br />
static member TryParse : string * MathNet.Numerics.LinearAlgebra.Double.DenseVector -&gt; bool<br />
static member TryParse : string * System.IFormatProvider * MathNet.Numerics.LinearAlgebra.Double.DenseVector -&gt; bool<br />
end</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.DenseVector</p>
<p>type: DenseVector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips61">
<p>val zeroCreate : int -&gt; DenseVector</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.DenseVector.zeroCreate</p>
</div>
<div class="tip" id="fstips62">
<p>val n : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips63">
<p>val iteri : (int -&gt; 'T -&gt; unit) -&gt; seq&lt;'T&gt; -&gt; unit</p>
<p>Full name: Microsoft.FSharp.Collections.Seq.iteri</p>
</div>
<div class="tip" id="fstips64">
<p>val ib : int</p>
<p>type: int<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;int&gt;<br />
implements: System.IEquatable&lt;int&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips65">
<p>val initSimplex : Matrix&lt;float&gt; -&gt; Vector&lt;float&gt; -&gt; Vector&lt;float&gt; -&gt; DenseMatrix * DenseVector * DenseVector * DenseVector * int [] * int []</p>
<p>Full name: Simplex2.initSimplex</p>
<p><em>naive initialization function - simply set basic x to b</em></p>
</div>
<div class="tip" id="fstips66">
<p>val A0 : Matrix&lt;float&gt;</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips67">
<p>val b0 : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips68">
<p>val c0 : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips69">let m, n = A0.RowCount, A0.ColumnCount<br />
let n' = m + nif Seq.exists (fun bi -&gt; bi &lt; 0.) b0 then failwith "b should be &gt; 0"// A0*x &lt;= b ---&gt; A*x' = b<br />
let A = DenseMatrix.init m n' (fun i j -&gt;<br />
if j &lt; n then A0.[i, j]<br />
elif i = j - n then 1.<br />
else 0.)// copy b<br />
let b = DenseVector.OfVector b0<br />
// extend c<br />
let c = DenseVector.init n' (fun i -&gt; if i &lt; n then c0.[i] else 0.)<br />
// default solution (basis xs)<br />
let x = DenseVector.OfVector b0<br />
let In, Ib = [| 0..n-1 |], [| n..n'-1 |]A, b, c, x, Ib, In</div>
<div class="tip" id="fstips70">
<p>val simplex : Matrix&lt;float&gt; -&gt; Vector&lt;float&gt; -&gt; Vector&lt;float&gt; -&gt; (Vector&lt;float&gt; * float) option</p>
<p>Full name: Simplex2.simplex</p>
<p><em>Revised Simplex implementation: min cx, Ax &lt;= b, x &gt;= 0, b &gt;= 0</em></p>
</div>
<div class="tip" id="fstips71">
<p>val A : DenseMatrix</p>
<p>type: DenseMatrix<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable<br />
inherits: Matrix<br />
inherits: Matrix&lt;float&gt;</p>
</div>
<div class="tip" id="fstips72">
<p>val b : DenseVector</p>
<p>type: DenseVector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips73">
<p>val c : DenseVector</p>
<p>type: DenseVector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips74">
<p>val x : DenseVector</p>
<p>type: DenseVector<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable<br />
inherits: Vector<br />
inherits: Vector&lt;float&gt;</p>
</div>
<div class="tip" id="fstips75">
<p>val xs : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips76">
<p>val x0 : Vector&lt;float&gt;</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips77">property Vector.Count: int</div>
<div class="tip" id="fstips78">
<p>val cx : float</p>
<p>type: float<br />
implements: System.IComparable<br />
implements: System.IFormattable<br />
implements: System.IConvertible<br />
implements: System.IComparable&lt;float&gt;<br />
implements: System.IEquatable&lt;float&gt;<br />
inherits: System.ValueType</p>
</div>
<div class="tip" id="fstips79">union case Option.None: Option&lt;'T&gt;</div>
<div class="tip" id="fstips80">(*<br />
min x1 + 5*x2 - 2*x3<br />
subject to<br />
x1 + x2 + x3 &lt;= 4<br />
x1 &lt;= 2<br />
x3 &lt;= 3<br />
3*x2 + x3 &lt;= 6<br />
x1, x2, x3 &gt;= 0 *)</div>
<div class="tip" id="fstips81">
<p>val A : Matrix&lt;float&gt;</p>
<p>Full name: Simplex2.A</p>
<p>type: Matrix&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Matrix&lt;float&gt;&gt;<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips82">
<p>val matrix : float list list -&gt; Matrix&lt;float&gt;</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.Utility.matrix</p>
</div>
<div class="tip" id="fstips83">
<p>val c : Vector&lt;float&gt;</p>
<p>Full name: Simplex2.c</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips84">
<p>val vector : float list -&gt; Vector&lt;float&gt;</p>
<p>Full name: MathNet.Numerics.LinearAlgebra.Double.Utility.vector</p>
</div>
<div class="tip" id="fstips85">
<p>val b : Vector&lt;float&gt;</p>
<p>Full name: Simplex2.b</p>
<p>type: Vector&lt;float&gt;<br />
implements: System.IFormattable<br />
implements: System.IEquatable&lt;Vector&lt;float&gt;&gt;<br />
implements: System.Collections.IList<br />
implements: System.Collections.ICollection<br />
implements: System.Collections.Generic.IList&lt;float&gt;<br />
implements: System.Collections.Generic.ICollection&lt;float&gt;<br />
implements: seq&lt;float&gt;<br />
implements: System.Collections.IEnumerable<br />
implements: System.ICloneable</p>
</div>
<div class="tip" id="fstips86">
<p>val printfn : Printf.TextWriterFormat&lt;'T&gt; -&gt; 'T</p>
<p>Full name: Microsoft.FSharp.Core.ExtraTopLevelOperators.printfn</p>
</div>
