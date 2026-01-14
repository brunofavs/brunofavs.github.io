---
layout: post
title: Will Our Tactile Sensors Shape the Direction of My Research?
date: 2026-01-09 11:12:00-0400
description: A collection of thoughts lingering on my mind about the possible scope of my research.
images:
  compare: true
  slider: true
thumbnail: assets/img/touch-reshape/digit.png
---

I was hopelessly looking through articles for human-robot manipulation challenges to justify the use of the proposed  technologies and somewhat narrow my research field. Truth is
that I am finding difficult to find challenges that most tasks are not solely reliant on tactile info. I may be looking too much into HRI instead of Physical HRI, I may go back and
narrow the search just to PHRI. I have seen articles talking about collaborative lifting, collaborative hammering, but I am not finding those tasks particularly intriguing. It may
be because in my vision those kind of tasks require contact but are not *contact-rich*. This lead me to try to find more about the different types of tactile sensors and got me
thinking that I actually  was not so sure what kind we had in our lab and what were exactly they were capable of.

From [Progress and Prospects of Multimodal Fusion Methods in Physical Human–Robot Interaction: A Review](https://ieeexplore.ieee.org/document/9094690) I got that there are
different types of direct contact sensing modalities:

There are sensors that measure external forces. These are able to measure forces applied on end effectors for example. Afonso's work on handover relied on these types of sensors.
LAR's ur10E has 6 force/torque sensors on each joint and one 6 axis fancier force/torque sensor of the end-effector. While these types of sensors provide intricate data, they are
not designed for haptic feedbackç they are designed for gross feedback. I still strongly consider Afonso's work on handover a contact-rich scenario, however I'm thinking that there
were many manipulation tasks that are somewhat reliant on haptic feedback.

2017 seemed to be the year when vision-tactile sensors boomed, with the publication of [GelSight: High-Resolution Robot Tactile Sensors for Estimating Geometry and
Force](https://www.mdpi.com/1424-8220/17/12/2762), an article sitting at around 1400 citations according to  *Google Scholar*.  

Vision-based tactile sensors work by **watching how a soft surface deforms when it touches an object**. A small camera looks at the inside of a transparent or translucent elastic
skin that is coated with a reflective or textured layer. When the sensor makes contact, the skin deforms. The camera captures this deformation, and computer vision algorithms
convert the images into information about:
- contact shape and location
- surface texture
- pressure and shear
- slip and motion They are conceptually designed for fine control.

Later, in 2020, the same company (*Gelsight*), in collaboration with *Meta*, developed *Digit*, a compact and cheaper successor to the prior Gelsight sensor aimed at robotic use.
An article was also publishing featuring  *Digit*, [DIGIT: A Novel Design for a Low-Cost Compact High-Resolution Tactile Sensor With Application to In-Hand
Manipulation](https://ieeexplore.ieee.org/abstract/document/9018215), currently hoarding around 700  citations. The quoted price per sensor is 350$, according to [*Gelsight*'s
website](https://pages.gelsight.com/digit-tactile-sensor). 

{% include figure.liquid 
    loading="eager"
    path="assets/img/touch-reshape/digit_exploded.png"
    class="img-fluid rounded z-depth-1 text-center"
    zoomable=true
    width="800"
    caption="Figure from aforementioned article about the Digit sensor"
%}

I am imagining that 2 *Digits* attached to the ends of the Robotiq 2F-140 we have at our lab. It would essentially unlock so many intriguing manipulation tasks requiring fine
control. The *Digit* sensors were designed to be natively incorporated into Allegro anthropomorphic hands

{% include figure.liquid 
    loading="eager"
    path="assets/img/touch-reshape/digit.png"
    class="img-fluid rounded z-depth-1 text-center"
    zoomable=true
    width="800"
%}

While browsing through Reddit, I found a [post](https://www.reddit.com/r/MachineLearning/comments/16rg533/r_leap_hand_lowcost_2kusd_anthropomorphic/) about a self-made
anthropomorphic hand with an example that inspired me to think about a interesting task:

**Task idea 1**: Human holds cube with numbered faces in hand, asks for a numbered or colored or even textured-face to be facing up. Our gripper having only 2 "fingers" (for a lack of
a better word), can't manipulate the cube while resting on its palm, so it would have to make use of a human hand to support and stabilize the cube, (and possibly give feedback).

**Task idea 2**: Instead of asking for a number, the human could ask for a incremental rotation. Using Afonso's work, the machine would detect a rotation input from the human, and the
rotate the cube accordingly.

*I think the main disadvantage of the current force/torque sensors we have is that there will not be a way to detect slippage. A smooth, long and narrow parallelepiped slipping
longitudinally through the grippers will not cause a change in perceived force. The force perceived will be $m\cdot g$ until the parallelepiped slips out of the gripper completely
and suddenly there is no perceived force, ignoring eventual lingering hysteresis.*

A side note about the image below also features a model trained in Isaac, and transferred to real life, which is cool.

{% include figure.liquid 
    loading="eager"
    path="assets/img/touch-reshape/cube_idea.png"
    class="img-fluid rounded z-depth-1 text-center"
    zoomable=true
    width="800"
%}

 These are however, isolated tasks. To think further ahead, I would need to find tasks with primitive connections that a  Meta-RL algorithm would be able to pick up, but than can
 be a problem for another day for now.

