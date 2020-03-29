---
title: "🎮 Bossy Code: Cleaner Code"
date: 2020-03-29T16:11:05+08:00
categories: [bossy]
tags: [bossy, general-purpose-controller]
draft: False
---

> View bossy's working code: 🎮 [here](https://github.com/mithi/bossy)

Using namespaces and separating concerns!

I started implementing namespaces as I noticed that some of names
the constants I defined are a bit too generic.

Here's a good quick article by Lucky Resistor about implementing namespaces in Arduino
[2014-Oct-31 | Lucky Resistor: How and Why to use Namespaces](https://luckyresistor.me/2014/10/31/how-and-why-to-use-namespaces/)

I also noticed that I'm using some structs I defined for helping toggle the pixels of the LED Matrix, only to iterate over
the input ids of a specific type of input (button, potentiometer, switches), so decided make arrays containing
just the input ids so we can do this without being tied to functionalities we don't use.

I'm sure there are a lot of other ways to make my code more elegant, but maybe for another time.

# 2.0.0
`Commit: e564213cda594a035c5e5d61526ab3e7bd9bf392`
[Bossy Full-Featured Code](https://github.com/mithi/bossy/commit/e564213cda594a035c5e5d61526ab3e7bd9bf392)

# 1.0.0
`Commit: c50dcaf8250b7a0da85f7a23653407b162559442`
[Bossy Full-Featured Code](https://github.com/mithi/bossy/tree/c50dcaf8250b7a0da85f7a23653407b162559442)

I need to start using Tags and Releases with Git as well. But maybe for another time.

Some readings that might help me get started quickly:
- [2018-Jun-11 | Nesha Zoric: A Tutorial for Tagging Releases in Git ](https://dev.to/neshaz/a-tutorial-for-tagging-releases-in-git-147e)
- [2018-Sep-12 | Maciek Głowacki: How to Become a Master of Git Tags](https://blog.daftcode.pl/how-to-become-a-master-of-git-tags-b70fbd9609d9)
