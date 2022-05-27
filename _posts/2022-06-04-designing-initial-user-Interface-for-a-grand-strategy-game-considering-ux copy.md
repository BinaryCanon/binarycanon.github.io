---
title: Designing Initial User-Interface for a Grand Strategy Game, Considering UX.
date: 2022-06-04 00:00:00 +0900
categories: [Art, User Interface]
tags: [ui]     # TAG names should always be lowercase
pin: true
image:
  path: /assets/img/posts/20220604/base-ui.png
---

# Terminology

There's a debate about precise definitions in this field since it's relatively new (20-30 years). Here's my attempt to define some terms in the context of the software world:

<ul>
<li>
<b><u>User Interface (UI)</u></b> - A user interface (UI, e.g. a command line) or a graphical user interface (GUI) where the user interacts with the software.
</li>
<li>
<b><u>User Interaction</u></b> - Includes UI and concerns about HOW users interact with our software and WHY they interact with it in these particular ways. Designing User Interaction is often based on user research and NOT personal beliefs and preferences. Human-computer interaction (HCI) is one of the most studied topics and includes fields such as graphic, speech, and touch interactions.
</li>
<li>
<b><u>User Experience (UX)</u></b> - Includes User Interaction and has a broader scope to understand HOW users experience our software from three core views: behavior, form, and content. In the context of games, our research is usually in the form of play-testing sessions.
</li>
</ul>

![User Interaction is part of User Experience](/assets/img/posts/20220604/user-experience.jpg)
_User Interaction is part of User Experience_

> I will mainly talk about User Interaction (visual interaction), and based on my findings, I will design my first sketch for a User Interface. I have no previous experience designing user interfaces, and I appreciate any feedback.
{: .prompt-warning } 

When it comes to User Experience (UX), it's all over the place in games: animations, sound design, content, and User Interface (UI/GUI).
In the graph above, while we can see fields such as sound design as part of the User Experience (UX), it has nothing to do with User Interface (UI) and User Interaction (Behavior bubble). Why? Since it won't impact the behavior of the users with our interface (unless we put some harsh noise and users will avoid clicking buttons). However, we can add sound effects as additional assets to the user interface (Content bubble) to increase immersion.

Let's focus on the User Interaction definition. Answering the WHY question is not easy. It requires research and qualitative tools such as interviews to crack the logic of WHY users behave in specific ways with our software. Without this feedback, we often attempt to solve problems based on our beliefs and preferences instead of evidence. After gathering evidence, we build our hypothesis, test it, and reiterate it repeatedly. Since there is no final state in UX - we keep learning and improving.

User Interface emerged from <a href="https://en.wikipedia.org/wiki/History_of_graphic_design#Web_Design" target="_blank">Classical Graphic Design</a> and used to be associated with a final product to deliver. However, since websites and apps constantly change today, the design is never final. With games, it's tricky since we deliver a final product. So there must be an end state. Games are a slightly different medium; they're somewhere between those two approaches:
<ol>
<li>
Traditional Classical Design with final delivery (print, architecture).
</li>
<li>
User Interaction with an iterative mindset (websites, apps).
</li>
</ol>
That's why games have the room for artistic finishes to help design the mood of the entire experience and contribute to the overall immersion.


> Still confused? try <a href="https://youtu.be/8QQKSeR4YhI" target="_blank">this youtube video </a> by NNg.
{: .prompt-tip } 
---
# Low-fidelity Prototype
<h2>Choosing the Layout</h2>
Before searching for any inspiration, I made a basic layout in <a href="https://draw.io" target="_blank">draw.io</a> to understand what I needed in the UI.
This specification of elements will help me create the design around the user's needs. This approach also known as <a href="https://en.wikipedia.org/wiki/User-centered_design" target="_blank">user-centered design</a>.
Jamal Nichols says in his courses (linked below): <i>"The best designers stay in the lowest possible fidelity, for as long as possible"</i>. I like this statement - it can easily be translated to Game Design and gives me the freedom to make rapid changes in no time.

![The best designers stay in the lowest possible fidelity, for as long as possible.](/assets/img/posts/20220604/wireframe.png)
_The best designers stay in the lowest possible fidelity, for as long as possible._

<h2>Inspiration</h2>
After a simple specification in mind, I explored some designs in <a href="https://www.gameuidatabase.com/" target="_blank">Game UI Database</a>. Crusader Kings 3 isn't there at the time of writing, but I was inspired by its UI, combined with Anno 1800. Both UIs are clean and modern yet have an artistic vision and are not entirely flat.

![CK3 UI](/assets/img/posts/20220604/ck3-ui.jpg)
_CK3 UI_

![Anno 1800 UI](/assets/img/posts/20220604/anno-1800-ui.jpg)
_Anno 1800 UI_

<h2>Choosing Base Colors</h2>
Since I like the base color of Godot's UI, I chose it, changed it a bit, and expanded my palette using <a href="https://mycolor.space/" target="_blank">mycolor.space</a>.
Google's <a href="https://material.io/design/color/the-color-system.html#color-theme-creation" target="_blank">material design</a> helped me understand HOW to use colors in the UI; contrast, primary and secondary colors, and accessible colors.


---
# Medium-fidelity Prototype
<h2>From draw.io to Krita</h2>
I couldn't help it, and I wanted to jump into Krita and create a medium-fidelity prototype before the entire wireframe was ready. I also think it's fine to do so; it's a game with many UIs and elements. Also, It already took me several weeks of iterations in the Game Design Document (along with other game mechanics).
At this point, I knew I wanted to make the UI as concise as possible - I didn't want the user to look for different corners on the screen to forward the game's clock. I want everything to be in front of him. Therefore, the top panel will have all the necessary information and controls, and the side panel will contain the game mechanics. There's also a plan for bottom menus, but these are still in their low-fidelity stage. 

<h2>Small Details</h2>
According to NNg, taking elements from the real world helps build experiences that feel intuitive (linked below).
So here is the time to take elements from the real world and convert the wireframe into a medium-fidelity prototype. First, since the left panel will contain various options and details about specific countries, a leather book feels intuitive and classic to me. I've used textures from <a href="https://polyhaven.com/" target="_blank">PolyHaven</a> to create the feeling of a leather book.
The top panel is more artistic, but it follows the repetition of the left panel, and it contains the same texture and colors. The game controls will be placed just below it in the flag's section. I slightly bent the top menu to break its square shape and added some depth and fine lines.

![Medium-fidelity Prototype](/assets/img/posts/20220604/base-ui.png)
_Medium-fidelity Prototype_

---
# High-fidelity Prototype
<h2>From Krita to Godot</h2>
I implemented a very basic version of it in the game and stopped after a while since I realized any change would take a while if the layout will change every now and then. I'm still in the low-fidelity prototype, and once I have the basic layout, I will implement it in the Godot.

---
# And.. back to low-fidelity
Iteration is the key. I just created a simple layout to try and feel the game. Now the real low-fidelity work begins.

---
> <u>Resources:</u> <a href="https://www.gameuidatabase.com/" target="_blank">Game UI Database</a>, NNg's <a href="https://www.nngroup.com/articles/ten-usability-heuristics/" target="_blank">blog</a>, Jamal Nichols's <a href="https://www.creativelive.com/instructor/jamal-nichols" target="_blank">courses</a> 
{: .prompt-info } 

Brb.


