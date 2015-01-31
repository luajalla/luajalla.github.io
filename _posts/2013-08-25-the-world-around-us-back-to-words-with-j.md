---
layout: post
title: 'The World Around Us: back to words with J'
author:
  display_name: luajalla
  login: luajalla
  email: lu-a-jalla@ya.ru
  url: ''
author_login: luajalla
author_email: lu-a-jalla@ya.ru
wordpress_id: 1872
wordpress_url: http://luajalla.azurewebsites.net/?p=1872
date: '2013-08-25 22:08:15 +0200'
date_gmt: '2013-08-25 22:08:15 +0200'
image:
  feature: header4.jpg
  credit: Stockholm, Djurgården
tags:
- J
- discoveries
comments: []
---

> The most exciting phrase to hear in science, the one that heralds new discoveries, is not "Eureka!" (I've found it!), but "That's funny..."  
(Isaac Asimov)

*[Originally posted [here](http://luajalla.azurewebsites.net/the-world-around-us-back-to-words-with-j/).]*  

Sometimes everybody wants to get off the beaten track and see the world. See how many exciting discoveries are waiting for you there.  

![J Logo]({{ site.url }}/images/jwlogo.png)
{: .image-right}  

I want to bring your attention to a language, which changed the vision of some things for me. This language is J.  

J is an array programming language; it is also functional and even object-oriented. And *extremely concise*. If you ever called F# 'cryptic' because of its operators or started to hate a Scala dev writing ````/:```` instead of ````fold```` – stop right here, because this language is definitely not for you.  

If you have a suspicion that J is for aliens, relax – it’s just *different*. In terms of grammar J is literally closer to English than C or Java. Who said ‘token’? It’s time to remember the good old ‘word’. Functions are verbs, objects – nouns, statements - sentences etc.  

Now let’s experiment!  

- J download is <a title="J Download" href="http://www.jsoftware.com/stable.htm" target="_blank">here</a>;  
- Getting started <a title="Getting Started with J" href="http://www.jsoftware.com/jwiki/Guides/Getting%20Started" target="_blank">materials</a>;  
- Why J <a title="Why J" href="http://www.jsoftware.com/jwiki/Guides/Getting%20Started" target="_blank">page</a>.  

It’s really funny, that *whys* start with:  

> J is a very rich language. You could study and use it for years, and still consider yourself a beginner.  

You’ll get it later =).  

But what trapped me for a weekend? I suspect it's all about unusual code and the new ways of thinking. Maybe something is wrong with me, but J is really fun to write and read! (though the latter requires more effort...)  

### Some operators   

````*:```` - square  
````/```` - ‘insert’, ````f/ x y z = x f y f z````  
````% ```` - division  
````_```` - unary minus  
````>:```` - increment  
````#```` - ‘tally’, the number of elements  
````@```` - ‘atop’, composition  
````@.```` – ‘agenda’/switch conjunction  
````=:```` - assignment  
```` ` ```` - ‘tie’ conjunction  
````|.```` – reverse the order  
````|:```` - transpose matrix  
````+/ .*```` - matrix multiplication  

### Conjunctions  

First of all, note, that the rightmost function is applied first, so ````3*2+1 = 3*(2+1) = 9````. How do we calculate, say, the sum of squares?  

Square numbers (command output is green):  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">*: </span><span class="i">1 2 3</span>
<span class="c">    1 4 9</span></pre>  

J has a special operator ````/````, called ‘insert’, I prefer to think about it as ‘reduce’:  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">+/ </span><span class="i">1 2 3</span>
<span class="c">    6</span></pre>  

The equivalent is ````1 + 2 + 3````.  
So the sum of squares can be written as  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">+/ *: </span><span class="i">1 2 3</span>
<span class="c">    14</span></pre>
<p>Conjunction <code>&amp;</code> allows to ‘fix’ the left or right argument:</p>
<pre class="fssnip"><span class="l"> 1: </span><span class="i">(</span><span class="o">^&amp;</span><span class="i">2) 1 2 3</span>
<span class="c">    1 4 9</span>
<span class="l"> 2: </span><span class="i">(2</span><span class="o">&amp;^</span><span class="i">) 1 2 3</span>
<span class="c">    2 4 8</span></pre>  

### Monads and Dyads  

Those who like the forbidden M-word may be confused. But APL-style definition is actually older: *monad* is a function taking a single argument on the right (like ````*:```` above). *Dyad* takes two arguments, on the left and on the right (````40 + 2````).  

### Hooks and Forks  

When two verbs come together, we call it a **hook**:  
````(f g) y = y f (g y)````  

Let’s normalize the list of elements so that their sum is equal to 1 (note, that division is denoted with ````%````). See the naïve variants and a hook:  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">1 2 5</span><span class="o"> % </span><span class="i">(1<span class="o"> + </span>2<span class="o"> + </span>5)</span>
<span class="l"> 2: </span><span class="i">1 2 5</span><span class="o"> % +/ </span><span class="i">1 2 5</span>
<span class="l"> 3: </span><span class="i">(</span><span class="o">% +/</span><span class="i">) 1 2 5</span>
<span class="c">    0.125 0.25 0.625</span></pre>  

Three verbs form a **fork**:  
````(f g h) y = (f y) g (h y)````  

My favorite fork example is mean:  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">(</span><span class="o">+/ % #</span><span class="i">) 1 2 3 4</span>
<span class="c">    2.5</span></pre>  

The same as  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">(</span><span class="o">+/</span><span class="i"> 1 2 3 4) </span><span class="o">%</span><span class="i"> (</span><span class="o">#</span><span class="i"> 1 2 3 4)</span>
<span class="l"> 2: </span><span class="o">+/</span><span class="i"> 1 2 3 4</span><span class="o"> % # </span><span class="i">1 2 3 4</span></pre>  

### Gerund  

The things are getting even more interesting: meet a *gerund* (the result of ```` ` ````)! The sense is similar to English: derived from a verb, but functions as a noun.  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">+ ` -</span>
<span class="c">    ┌─┬─┐</span>
<span class="c">    │+│-│</span>
<span class="c">    └─┴─┘</span></pre>  

We can choose one of the ‘boxes’ with switch conjunction ````@.````:  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">+ ` - @. </span><span class="i">0</span>
<span class="c">    +</span>
<span class="l"> 2: </span><span class="o">+ ` - @. </span><span class="i">1</span>
<span class="c">    -</span></pre>  

Look at the abs implementation:  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">abs </span><span class="o">=: + ` - @. </span><span class="i">(</span><span class="o">&lt; &amp;</span><span class="i"> 0)</span>
<span class="l"> 2: </span><span class="i">abs 42</span>
<span class="c">    42</span>
<span class="l"> 3: </span><span class="i">abs _42</span>
<span class="c">    42</span></pre>  

Gerund can be used together with ‘insert’, e.g. the following <del>statement</del> sentence is equivalent to ````(50 * 2 % 100)````:  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">* ` % / </span><span class="i">50 2 100</span>
<span class="c">    1</span></pre>  

### More Verbs!  

````#.```` – dyad ‘base’ or monad ‘from base 2’  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">#. </span><span class="i">1 0 0 1</span>   
<span class="c">    9</span>  
<span class="l"> 2: </span><span class="i">3 </span><span class="o">#. </span><span class="i">2 1</span>   
<span class="c">    7</span></pre>  

````[```` and ````]```` – ‘same’, id or argument  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">[</span><span class="i"> 2</span>   
<span class="c">    2</span>
<span class="l"> 2: </span><span class="i">1 </span><span class="o">[</span><span class="i"> 2</span>   
<span class="c">    1</span>  
<span class="l"> 3: </span><span class="o">]</span><span class="i"> 2</span>   
<span class="c">    2</span>  
<span class="l"> 4: </span><span class="i">1 </span><span class="o">[</span><span class="i"> 2</span>   
<span class="c">    2</span></pre>  

````+/\````  – running sum (the same works for * etc)  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">+/\</span><span class="i"> 2 3 4</span>   
<span class="c">    2 5 9</span>
<span class="l"> 2: </span><span class="i">acc </span><span class="o">=: /\</span>
<span class="l"> 3: </span><span class="o">+ </span><span class="i">acc 2 3 4</span>
<span class="c">    2 5 9</span>
<span class="l"> 4: </span><span class="o">* </span><span class="i">acc 2 3 4</span>
<span class="c">    2 6 24</span></pre>  

````{.```` - take (````n {. L````)  
	
<pre class="fssnip"><span class="l"> 1: </span><span class="i">2 </span><span class="o">{.</span><span class="i"> 3 4 5</span>   
<span class="c">    3 4</span></pre>
<p><code>}.</code> - drop (<code>n }. L</code>)</p>
<pre class="fssnip"><span class="l"> 1: </span><span class="i">2 </span><span class="o">}.</span><span class="i"> 3 4 5</span>   
<span class="c">    5</span></pre>
<p><code>{</code> - fetch</p>
<pre class="fssnip"><span class="l"> 1: </span><span class="i">1 </span><span class="o">{</span><span class="i"> 3 4 5 6</span>   
<span class="c">    4</span>
<span class="l"> 2: </span><span class="i">_1 </span><span class="o">{</span><span class="i"> 3 4 5 6</span>   
<span class="c">    6</span></pre>  

````/:```` - sorting  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">/:</span><span class="i"> 10 2 5 3</span>   
<span class="c">    1 3 2 10</span></pre>  

Remember fork, id and fetch?  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">(</span><span class="o">/: { ]</span><span class="i">) 10 2 5 3</span>   
<span class="c">    2 3 5 10</span></pre>  

````q:```` - prime factors  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">q:</span><span class="i"> 20</span>   
<span class="c">    2 2 5</span></pre>  

````p:```` - generate nth prime  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">p:</span><span class="i"> 3</span>   
<span class="c">    7</span></pre>  

sum 100 primes:  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">+/ p: i.</span><span class="i"> 100</span>   
<span class="c">    24133</span></pre>  

### Modelling Transitions  

Having several states and markov-process like transitions probabilities, what is the probability of one of these states at some time? Credit rating migrations can be taken as an example of such transitions.  

So we define matrix and initial state probabilities:  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">T </span><span class="o">=:</span><span class="i"> 3 3 </span><span class="o">$</span><span class="i"> 0.6 0.3 0.1 0.1 0.5 0.4 0 0.1 0.9</span>
<span class="l"> 2: </span><span class="i">x </span><span class="o">=:</span><span class="i"> 0.3 0.2 0.5</span></pre>  

At time ````y```` the result is ````T^y```` (````x u^:n y```` is equivalent to ````x u x u …(n times) y````). What is the probability of state 3?  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">T3 </span><span class="o">=:</span><span class="i"> T </span><span class="o">+/ .*^:</span><span class="i">2 T</span>
<span class="l"> 2: </span><span class="o">+/</span><span class="i"> x </span><span class="o">*</span><span class="i"> (2 </span><span class="o">{|:</span><span class="i"> T3)</span>
<span class="c">    0.6668</span>
<span class="l"> 3: </span><span class="o">+/</span><span class="i"> x </span><span class="o">* [</span><span class="i"> 2 </span><span class="o">{|:</span><span class="i"> T3</span>
<span class="c">    0.6668</span>
<span class="l"> 4: </span><span class="i"> 2 </span><span class="o">{</span><span class="i"> x </span><span class="o">+/ .*</span><span class="i"> T3</span>
<span class="c">    0.6668</span></pre>  

The first two options transpose matrix, select the column with index 2 and calculate dot-product with x. The last one uses matrix multiplication verb.  

### Present Value  

Given the stream of cashflows and rate (in %), we want to calculate PV.  
Cashflows: ````100 50````  
Rate: ````5%````  
````PV = 100 + 50 / (1 + 5%) = 147.61905````  

Splitting by steps:  
- transform %  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">pv </span><span class="o">=: &gt;:@</span><span class="i">(</span><span class="o">] %<span class="i"> 100"_)</span>
<span class="l"> 2: </span><span class="i">pv 5</span>
<span class="c">    1.05</span>
</span></pre>  

- compute discount factor  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">pv </span><span class="o">=: %@&gt;:@</span><span class="i">(</span><span class="o">] %<span class="i"> 100"_)</span>
<span class="l"> 2: </span><span class="i">pv 5</span>
<span class="c">    0.952381</span>
<span class="l"> 3: </span><span class="i">1 % 1.05</span>
<span class="c">    0.952381</span>
</span></pre>  

- reverse cashflows list and sum the discounted values ````(50*df^1 + 100*df^0)````:  

<pre class="fssnip"><span class="l"> 1: </span><span class="o">|. </span><span class="i">100 50</span>
<span class="c">    50 100</span>
<span class="l"> 2: </span><span class="i">0.952381 <span class="o">#. |. </span><span class="i">100 50</span>
<span class="c">    147.619</span>
</span></pre>  

- combine everything  

<pre class="fssnip"><span class="l"> 1: </span><span class="i">pv </span><span class="o">=: %@&gt;:@</span><span class="i">(</span><span class="o">] %<span class="i"> 100"_)</span><span class="o"> #. |.@[</span>
<span class="l"> 2: </span><span class="i">100 50 pv 5</span>
<span class="c">    147.619</span>
</span></pre>  

### Bonus  

If you’re still able to follow this post, here's a couple of references:  
- <a title="J Reference Card" href="http://www.jsoftware.com/jwiki/HenryRich?action=AttachFile&amp;do=view&amp;target=J602_RefCard_color_letter_current.pdf" target="_blank">J Reference Card</a> (2 pages to your rescue);  
- financial <a title="J Financial Phrases" href="http://www.jsoftware.com/jwiki/JPhrases/Finance" target="_blank">phrases;</a>  
- <a title="J Piano Tuning Essay" href="http://www.jsoftware.com/jwiki/Essays/PianoTuning" target="_blank">essay</a> on piano tuning;  
- linear recurrences and <a title="J Matrix Powers" href="http://www.jsoftware.com/jwiki/Essays/Linear%20Recurrences" target="_blank">matrix powers</a>;  
- <a title="Simplex Method" href="http://www.jsoftware.com/jwiki/Stories/RichardBrown." target="_blank">simplex method</a> in J.  

Happy crazy coding!  

<p>&nbsp;</p>
<p>&nbsp;</p>
