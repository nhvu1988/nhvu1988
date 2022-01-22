---
title: Ext.NET - What to get it WRONG? - PART 2 - Layout
author: nhvu1998
date: 2017-06-04
category: Coding
tags: [ext.net]
---

## Layout

Those are the questions I usually ask or got from my co-workers

* Why this component doesn't resize to fit browser's size?
* Why this field doesn't fit to its parent?
* Why GridPanel doesn't fill with parent's width?
* In Column Layout, how to fill children with parent's height?

To answer them, we need to understand how the `layout` works in `Ext.NET` first.

## Viewport

`Viewport` oftens be used as a top parent component of the application. It could be re-sized automatically to fill the browser viewport, and be used to control the whole application's layout.

> Viewport is the top most level of layout component
