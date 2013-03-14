---
layout: post
title: "Compiler Assisted Automatic Error Detection"
description: ""
category: Compilers
tags: [compilers, c++, AspectC++, aspect oriented, error detection]
---
{% include JB/setup %}
For the seminar on [software based fault tolerance](http://ess.cs.tu-dortmund.de/Teaching/WS2012/SFt/index.html)
I compared different methods of error detection in compilers:
> This work gives an overview of compiler-assisted methods to ensure
> fault-tolerance without hardware support and major modifications to
> source programs. The overview of existing methods is accompanied by a
> practical implementation of AN-Encoding using [AspectC++](http://www.aspectc.org),
> and a theoretical proposal for adding redundancy to functional
> programs by employing multiple beta reduction
> strategies. Experimental results from \[[15](http://doi.ieeecomputersociety.org/10.1109/MM.2007.4),
> [17](http://dx.doi.org/10.1109/DEPEND.2010.16)\] are compared and limitations of compiler-assisted approaches are discussed.

You can read the full paper [online](/papers/errordetectingcompilers.pdf) or
just get a quick overview using the [slides](/slides/errordetectingcompilers-presentation.pdf).
My aspect oriented implementation of AN-Encoding is also [available](https://github.com/JanBessai/anencoding).
