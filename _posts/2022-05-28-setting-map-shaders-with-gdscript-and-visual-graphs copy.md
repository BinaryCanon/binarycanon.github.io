---
title: Setting Map Shaders with GDScript & Visual Graphs
date: 2022-05-28 00:00:00 +0900
categories: [Programming, Map]
tags: [shaders]     # TAG names should always be lowercase
pin: true
image:
  path: /assets/img/posts/20220528/country-shader-demo.gif
---

# Intro
My goal is to create a responsive shader for both mouse hovering and selection. Godot's visual shaders are neat and show a preview of each of their operation. Moreover, it outputs the shader's source code if you need to verify the calculations or manipulate a copy of it.

---
# Breakdown 
![Visual Shader Godot - Country selection](/assets/img/posts/20220528/testing-color.png)
_Each node will be explained below_
<h2>Breakdown of Each Node</h2>
<ol>
<li>
The shader starts with the texture input.
The texture is a PNG with a blank background, so it's crucial to set the shader's Depth Draw to <code>alpha_prepass</code>. Otherwise, you won't see a transparent background.
</li>
<li>
<code>ColorUniform</code> is a dynamic color I control from GDScript. It changes based on the country's color. More about <a href="https://thebookofshaders.com/03/" target="_blank">Uniforms</a>.
</li>
<li>
Node number 3 is being used instead of node number 2. I must do that to show the preview of the other nodes. Otherwise, they will be blank.
</li>
<li>
We take the input texture and the color we want to use (node number 3 for now) and utilize the Darken function. The Darken function takes the lowest value between the two inputs, so it will choose the red since its vector is <code>Color(1,0,0)</code> while the white's <code>Color(1,1,1)</code>.
</li>
<li>
To calculate the border, we can use the difference between the colored texture and its alpha (empty background). <u>Note:</u> I have a border on the sprite, and you must have one to make it work - the black border will stay after calculating the difference since it overlaps with the background's value.
</li>
<li>
I scale its color slightly to give it more emphasis (not necessary, just experimentation).
</li>
<li>
Since we only have the alpha channel, the border is white. To change it to red, I use the Darken function again, which chooses the smallest color value (same as node num. 2).
</li>
<li>
A boolean which I control from GDScript. All Uniform variables are accessible from GDScript.
</li>
<li>
The Albedo color. If <code>IsOutlined</code> is true, it will choose the nodes' path with green numbers (border). Otherwise, it will choose the nodes with red numbers above them. Either way, it colors the sprite in red.
</li>
<li>
This node controls the alpha channel (sprite's transparency). If <code>IsOutlined</code> is true, continue to read about node number 11. Otherwise, set it to 0.25 opacity for the mouse-hover effect (1*0.25).
</li>
<li>
The sprite's alpha channel is forwarded from node num 1 to this node. If it receives a true value from node number 10 (<code>IsOutlined</code> aka border to be shown), we multiply by the difference, which is node number 6. Let's think about it; The border has a value of 1 (white), and everything else is 0 (black).
We need to eliminate everything else that is not the border. When we multiply the sprite's alpha with node number 6, each white pixel is multiplied by 0 (black/transparent) and won't be shown, except the border, which is 1, and its corresponding section from node number 6, which is also 1. Connecting node 6 to 7 colors it with red.
</li>
</ol>
<br />

---

# The Final Graph
![Visual Shader Godot - Country selection, Uniform variable](/assets/img/posts/20220528/uniform-color.png)
_Using node 2 instead of node 3_

> There is only one change, instead of using node number 3 I'm using node number 2 as a Uniform variable controlled from GDScript.
{: .prompt-info } 

---

# Controlling the shader using GDScript
Controlling Uniform variables from GDScript depends on which type of shader you are working on. For this case, a Visual Shader on 3DSprite (GeometryInstance), I use:
```gdscript
$Sprite.material_override.set_shader_param("IsOutlined", true)
$Sprite.material_override.set_shader_param("ColorAlbedoUniform", Color.red)
``` 

---

# Demo
![ALT The flower](/assets/img/posts/20220528/country-shader-demo.gif)
_Optimizing GIFs for web is a headache, I tried my best :/_
<br />
