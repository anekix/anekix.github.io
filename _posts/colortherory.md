---
layout: post
title: A Dive into Color theory 
categories:
- blog
---


I want to start this by saying that i am a not expert at color theory ts just that i am writing down the things that i 
learned whiile learning about shaders & graphics programming .it will serve as my own refrence. aslo It might be helpful to someone...someday...

Most of you would be already be familiar with the rgba color model.in this each color is formed by adding three primary colors, red, green & blue.


`HSV`
HSL stands for Hue, Saturation and Lightness. Hue is the value of the actual pure colour, such as Red, Green, Blue, Yellow, Purple and so on, as represented on a colour wheel. The value is in degrees, from 0 to 360. See image HSL Colour Wheel

Saturation is how much of that colour is present in the mix. It's a percentage scale, from 0% to 100%. 0% saturation outputs a dull grey, meaning there's 0% of your hue in the mix.

Lightness is again a percentage value, from 0% to 100%. 0% means its completely dark, in other words— pure black. 100% is, well, pure white. See image Saturation and Lightness

HSL is an intuitive colour format that mimics the real world. For example, you're probably sitting at a desk right now. Notice the colour of the desk. Let's say its a mahogany desk. It's colour values would be—

Hex: #4f2017;
RGB: rgb(79, 32, 23);
HSL: hsl(10, 55%, 20%);
Now hold your hand above it, like a couple of inches above the surface. Your hand's shadow now makes the desktop a bit darker, right? Now, it's impossible to represent this colour change in hex or rgb, without changing the colour itself. But in hsl, it's a absolute breeze— simply decrease the Lightness value, and bam! The colour remains the same, but with a bit of black mixed in— basically lessen the Lightness value.

Hex: #200d09;
RGB: rgb(32, 13, 9);
HSL: hsl(10, 55%, 8%);
As you can see, the values of Hex and RGB have completely changed, whereas HSL only one aspect changed. Because of this, it becomes intuitively easy to create colour schemes on the fly.

If I told you the button on the webpage needs to be #61904a or rgb(97, 144, 74), can you guess what the colour could be? Nope, not unless you're a computer. But the same colour in HSL: hsl(100, 32%, 43%). Now the Hue value is 100°, meaning it's close to pure Green which is 120°. So its a green of some sort. Next, its 32% saturated, meaning 32 steps from being a dull grey, which a pretty desaturated green. Next, its 43 steps from being pure white, and inversely, 57 steps from being pure black. So, on the lighter side.

Make the button lighter on Hover, and darker on Click? It's a snap, just increase and decrease the last value, Lightness. That's all. This is impossible to do with Hex and RGB without a tool or a designers help.


