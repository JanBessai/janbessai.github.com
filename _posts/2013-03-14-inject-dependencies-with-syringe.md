---
layout: post
title: "Inject dependencies with Syringe"
description: ""
category: "Lambda calculus"
tags: [lambda calculus, Dependency Injection, Java, Spring]
---
{% include JB/setup %}
![Syringe](/pics/Syringe.png)
One of my latest projects was for a seminar on 
[AI-Planning](http://ls14-www.cs.tu-dortmund.de/index.php/Seminar-AI-Planning-12-13).
The result was a tool called Syringe. You can read all about it
in the [paper](/papers/applicative.pdf) or get an overview
using the [slides](/slides/codegeneration.pdf). Here is the
abstract:
> This work analyzes how to generate code from applicative terms,
> especially in the context of Dependency Injection.  Relevant basic
> concepts are introduced and a threefold correspondence between
> Dependency Injection style object creation, applicative terms and
> Hilbert-style proofs is explored. The implementation of Syringe, a
> tool which can translate λ-expressions to XML fragments for
> the Spring Framework, is presented together with a demo application to
> compose GUI components.

Syringe is available on [Github](https://github.com/JanBessai/Syringe).
