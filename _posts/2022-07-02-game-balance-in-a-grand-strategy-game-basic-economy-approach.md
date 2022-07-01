---
title: Game Balance in a Grand Strategy Game - Basic Economy Approach
date: 2022-07-02 00:00:00 +0900
categories: [Game Design]
tags: [economy system]     # TAG names should always be lowercase
pin: true
image:
  path: /assets/img/posts/20220702/cosine_1.png
---

# Intro
Games are multidisciplinary. I had no idea to what extent.
In the last couple of weeks, I've been exploring Game Balance.

---
# Balancing Economy Variables
Balancing a game requires the balance of all the game's systems. I believe balancing a game's economy is a small scale of rules based on the research and philosophy of Adam Smith and John Maynard Keynes (however, when it comes to social sciences, we are stuck..?) Here I will talk about my approach for using variables to impact other variables; How to balance "unrest" to affect "optimism."


<h2>Some Math</h2>

> In this section, I'm using <a href="https://www.desmos.com/calculator" target="_blank">Desmos calculator</a>.
{: .prompt-info } 

Assuming "unrest" and "optimism" are normalized (0-1.0), we need a function whose outputs will correspond to a scale of a whole floating number as well.
Our component, which represents x, is the unrest variable in this case. $$ cos(component) $$ can do that with some changes: <br />
$$ \frac{1}{2}cos(component)+\frac{1}{2} $$ <br />
![This function will output values between (0-1.0)](/assets/img/posts/20220702/cosine_1.png)
_This function will output values between (0-1.0)_


We are multiplying by PI to set its equilibrium at 0.5 instead of PI, which isn't helpful since we are dealing with normalized scales (0-1.0).<br />
$$ \frac{1}{2}cos(component \cdot \pi)+\frac{1}{2} $$ <br />
![Multiplying by PI to set its equilibrium at 0.5](/assets/img/posts/20220702/cosine_2.png)
_Multiplying by PI to set its equilibrium at 0.5_
Let's say I want "unrest" to impact "optimism." First, I need unrest to deal negative and positive points so it can impact optimism with positive and negative weights. To do that, I will remove the last part of the function ($$\frac{1}{2}$$).
The range is now from -0.5 to 0.5:
![Ranges from -0.5 to 0.5](/assets/img/posts/20220702/cosine_3.png)
_Ranges from -0.5 to 0.5_

Remember the equilibrium is still the same at 0.5: <br />
$$ \frac{1}{2}cos(component \cdot \pi)=0 $$ <br />
Anything below 0.5 will output a negative number. Anything above 0.5 outputs a positive one.
That doesn't make sense because when unrest goes down, optimism should go up.
Let's negate the function:<br />
$$ -\frac{1}{2}cos(unrest \cdot \pi) $$ <br />
The range is now from -0.5 to 0.5:
![Negating the function](/assets/img/posts/20220702/cosine_4.png)
_Negating the function_

Now when unrest is below 0.5, we have a positive output, and when it's above 0.5, the output is negative.
All left to do is to determine the weight of unrest on optimism. Let's calculate the optimism with weights.<br />
Assuming we want unrest to affect optimism by a weight of 30%, we will have:<br />

$$ optimism = 0.3 \cdot unrest ... $$ <br />
Converting to the cosine formula: <br />
$$ -\frac{1}{2}cos(unrest \cdot \pi)\cdot 0.3 $$ <br />

Then we add other variables - all their weights will sum to 1.0:<br />
$$ optimism = 0.3 \cdot unrest + 0.4 \cdot variable_b + 0.3 \cdot variable_c $$ <br />
> It's also called weighted average. <a href="https://youtu.be/slFqL86q3EA" target="_blank">Watch on YouTube.</a>.
{: .prompt-info } 

If we have an aggregate values, such as resources (electricity, water, currency, etc) we can use the following:<br />
$$ value+value(weight_1 \cdot variable_1 + weight_2 \cdot variable_2) $$ <br />

We can use the weighted average to constrain values in relation to their previous value so changes will look relative:<br />
$$ Electricity_{previous} + Electricity_{previous} \cdot (weight \cdot residential + weight \cdot industries) $$ <br />

<h2>Further info</h2>
<u>Why multiply the component with PI?</u><br />
Since I'm using scales from 0-1.0, I want to ensure that if 1.0 is the input, the function will output its maximum output as well (0.5 in this case)—the same with 0, which will output -0.5.
<br />

<u>Why cosine?</u><br />
In this case, I wanted a straightforward function that maps inputs of 0-1.0 to negative and positive outputs. Exploring functions is a key to balancing a game. I'm still learning this stuff, though.


<h2>Game Balance Resources</h2>
Spreadsheets are a straightforward way to design game economy. They are frequently utilized to balance different game systems as a whole. Here are a few further resources:

<ul>
<li>
Ian Schreiber on Game Balance: <a href="https://youtu.be/tR-9oXiytsk" target="_blank">youtu.be/tR-9oXiytsk</a>
<ul>
<li>
Schreiber's book: <a href="https://www.google.com/books/edition/Game_Balance/g301EAAAQBAJ" target="_blank">google.com/books/edition/Game_Balance/g301EAAAQBAJ</a>
</li>
</ul>
</li>
<li>
Game Balance using spreadsheets: <a href="https://youtu.be/WqfZV2Wlb1g" target="_blank">youtu.be/WqfZV2Wlb1g</a>
<ul>
<li>
Readable version: <a href="http://jameystevenson.com/" target="_blank">jameystevenson.com</a>
</li>
</ul>
</li>
<li>
Function generator: <a href="https://cubic-bezier.com/" target="_blank">cubic-bezier.com</a>
</li>
</ul>


The best designers stay in the lowest possible fidelity as much as possible. -Jamal <br />
Brb.
