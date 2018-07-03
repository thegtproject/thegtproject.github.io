---
title: "Preview of Pixel Shaders 2"
date: 2018-06-20T09:47:47-06:00
draft: true
---

I am far from a graphics artist/designer/or anything of the nature. So ignore the quality of the effects i'm about to show you. These examples are mostly glsl code copied from shadertoy and hacked, mangled, and mashed to do something obvious as a demo.

My hope is for someone to craft something better than I can that can be used for the official examples in Pixel when shader supports gets merged. I really want to do this right for my first official contribution to an open source project!

Now let me show you how to use the shader API for Pixel.

> Remember you need the shader branch from my fork of pixel checked out in faiface's directory. Instructions if you need them are on the bottom of the first preview post [located here](../preview-of-pixel-shaders)

Let's take a look at how easy it is to use. Grab

Before and After

![](/images/before.gif)

![](/images/blur.gif)

Cool little blur effect, right? I know it looks terrible.

The great part about this new feature, however, is how EASY it is to implement that blur effect. Let's open `pixel/examples/platformer/main.go` and make the needed changes.

First things first, since we're going to use our own shader, we need to provide the glsl fragment shader code in our game. Add this to the bottom of the file:

``` c
var motionBlurFragShader = `
#version 330 core
precision mediump float;
in vec4 Color;
in vec2 TexCoords;
in float Intensity;

out vec4 color;

uniform vec4 colorMask;
uniform vec4 texBounds;
uniform sampler2D tex;
uniform sampler2D backbuffer;
uniform float u_time;
uniform float u_blur;

void main() {
	vec2 t = (TexCoords - texBounds.xy) / texBounds.zw;
	color = Intensity * Color * texture(tex, t);
	vec2 p = t / min(t.x, t.y);
	float tt = u_time * 0.7;
	p = p * p * 8.0;
	float c = u_blur;
	c += 0.01 / length(p - vec2(sin(tt * 2.), cos(tt * 1.)));
	c += 0.01 / length(p - vec2(sin(tt * 1.3), cos(tt * .8)));
	c += 0.01 / length(p - vec2(sin(tt * .9 + u_time), cos(tt * 1.7)));
	c += 0.01 / length(p - vec2(cos(tt * .47), sin(tt * 1.9 + u_time)));

	vec4 b = texture(backbuffer, t);

	vec4 ccc = c * vec4(0.5, 0.5, 0.5, 1.0) + b * 0.7;
	color = mix(color, ccc, 0.5 );
}
`
```

Since we want to do a post-processing effect to the entire scene, we need to get the underlying canvas of our window.

``` go
    winCanvas := win.GetCanvas()
```

Now let's bind two additional uniforms to our shader. `u_time` for a clock and `u_bluramt` to adjust the blur coefficient from our game. I did `u_bluramt` to help find the right blur amount in real-time. This will serve a good example of adding outside input to your shader.

To bind these two uniforms, create the variables they should be bound to, and add the uniforms by name with a pointer to those variables like so:

``` go
    var uTime, uBlurAmnt float32
    
    winCanvas := win.GetCanvas()

	last := time.Now()
	for !win.Closed() {
    ...
```