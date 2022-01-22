---
title: Ext.NET - What to get it WRONG? - PART 1 - Why we use it?
author: nhvu1998
date: 2017-06-03T00:00:00.000Z
category: Coding
tags: [ext.net]
---
![](/assets/img/posts/20170603-ext-net-what-get-wrong-part1-why-we-use-it-1.png)

## Ext.NET - You got it WRONG!

`Ext.NET` is an `ASP.NET` framework that helps to develop the UI for a web application *likes desktop application*. It's a cool framework with many advantages, comes with a bunch of cool built-in components, and also has a [beautiful sample site](http://mvc.ext.net/). But... 

> Why there are many people in my company scare it off? 😱😱😱

After a while, I also had a chance to join the team working on it. I realize that the codes have old ages and the implementation contains many mistakes, which could make any newbie misunderstand and be afraid at first time step into the `Ext.NET` world.

In this series, I will try to list out some our mistakes could be improved to make the Ext.NET more friendly and take it easy to work on.

## Why use Ext.NET?

Firstly, we can go through some advantages of `Ext.NET`

### Built for .NET developers

Yeah! Ext.NET is built for us, .NET developers 👨‍💻, from... its name? 🤷

Haha, it's just a joke but it's not wrong 🙆 We can use .NET to build the server (backend) and also the client (frontend) so our codes will be consistent in both sides. It's a big benefit when we want to stabilize the system, easy to maintain, and quickly figure out what's wrong when bug 🐞 is coming!

### HTML5 + CSS + JS

Yeah! It comes with a set of underlying HTML5, CSS and JS components and since it's built for .NET developer, we (.NET developers) don't require to have a good or deeply knowledge of those web technologies. Of course, basic skills in CSS and JS to do customization is not our problem, right? 😉

### Cross-browser compatibility

Yeah! It's cross-browser compatibility, supports all modern browsers, includes...

> [IE6 supported](https://docs.sencha.com/extjs/6.0.2/guides/supported_browsers.html) 👍👍👍

As you know, [IE is a dangerous browser for any front-end developers](https://www.quora.com/Why-do-so-many-front-end-developers-hate-Internet-Explorer). In my case, when starting a new project, I always ask my boss 

> what's the minimum IE version needs to be supported? 😨

### Responsive

Yeah! `Ext.NET` supports responsive. All components supposed to be auto resizable to fit to their defining layout and fit to their parent (the container). 

It means, when the parent resizes, all the inside components will be resized to fit the parent size and their corresponding chidren also will be resized simultaneously.

`Ext.NET` provides many types of `layout` to handle most of the popular cases.

The [border layout](http://mvc.ext.net/#/Viewport_Basic/Built_as_Builder/) is the one I love most 😍. It could be used to layout the whole webpage that have 3 basic things: header, body, and footer, in a viewport, that I didn't see any support from other frontend frameworks so far.

> But we need to understand **`how the Layout works`** correctly!

Otherwise, fixing layout in `Ext.NET` could take you a day!

### Nice document and many cool built-in components

Yeah! The last but not least advantage of Ext.NET is, it comes with [a beautiful sample site](http://mvc.ext.net/). On that site, we can see attractive samples of all built-in components and they also provide source codes for us easy to catch up and quickly find out what we need, can use in our application.

## Why not use `Ext.NET`?

The only reason that you shouldn't use `Ext.NET` is...

> You're not RICH 😐

Oh... Yes! `Ext.NET` is not free. It requires to buy a license if we want to use it on the commercial application.

[$4999](http://ext.net/store/) is not a cheap price but not expensive compares to its features? 

Luckily, my company has it, so I don't worry about it anymore 🤑