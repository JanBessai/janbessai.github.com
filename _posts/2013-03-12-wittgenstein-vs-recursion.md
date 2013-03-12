---
layout: post
title: "Wittgenstein vs Recursion"
description: ""
category: Philosophy
tags: [philosophy, recursion, Wittgenstein]
---
{% include JB/setup %}
![Wittgenstein](http://upload.wikimedia.org/wikipedia/en/c/c7/Ludwig_Wittgenstein_by_Ben_Richards.jpg)


First of all: I love Wittgenstein's Tractatus! It may have been naive
to try and explain the world in such a "simple" logical framework, but
it is still one of the best and most readable approaches of the 21st century.
For computer scientists this should be of special interest:
> 3 .333    A function cannot be its own argument, because the functional sign already contains the prototype of its own argument and it cannot contain itself.
>
> If, for example, we suppose that the function F(fx) could be its own argument, then there would be a proposition "F(F(fx))", and in this the outer functions F and the inner function F must have different meanings; for the inner has the form φ(fx), the outer the form ψ(φ(fx)). Common to both functions is only the letter "F", which by itself signifies nothing.
>
> This is at once clear, if instead of   "F(F(u))", we write "(φ) : F(φu) . φu = Fu".
>
> Herewith Russell's paradox vanishes.

It seems Wittgenstein used something similar to simple typed lambda,
where ¬∃σ. ⊢ λ x . x x : σ. I wonder how his world would have
looked in a more turing complete framework.

You can find the complete text and translation [here](http://people.umass.edu/klement/tlp/).  
The picture above was taken from [Wikipedia](http://en.wikipedia.org/w/index.php?title=File:Ludwig_Wittgenstein_by_Ben_Richards.jpg&oldid=468820646).
