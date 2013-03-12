---
layout: post
title: "Traces with monoidal errors"
description: ""
category: "Haskell"
tags: [haskell, category theory, exceptions]
---
{% include JB/setup %}
Haskell has a lot to offer when it comes to cleaning up your code
structure. One of the gains is proper error signaling. Let us look at
common C code returning `NULL` to signal errors and setting a global
error variable:
{% highlight c %}
void *foo(...) {
  do_something();
  if (error) {
	errno = 42;
    return NULL;
  } else {
    return result;
  }
}
{% endhighlight %}
You will find this for example in the standard function `fopen`.
Additionally a global variable `errno` is set, to signal which
kind of error was encountered. While this is very straight forward
it has two problems:
1. You are not warned if you use the result without checking for an error
2. The global variable `errno` will be overwritten on every call

Now most modern languages solve this by adding Exceptions as a controlflow
escape mechanism. They are as common place as shun upon, since they
can cause great headaches in compiler construction, reasoning about code,
[multithreading](http://docs.oracle.com/javase/7/docs/api/java/lang/Thread.UncaughtExceptionHandler.html),
[maintainability](http://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)
and [cleanup](http://effbot.org/zone/python-with-statement.htm).

Now a lot has been
[done](http://www.haskell.org/hoogle/?hoogle=error) in the
Haskell world to study the [subject](http://www.haskell.org/haskellwiki/Error_vs._Exception).
One of the most common approaches in [real world](http://book.realworldhaskell.org/read/error-handling.html)
libraries is to use [MonadError](http://hackage.haskell.org/packages/archive/mtl/latest/doc/html/Control-Monad-Error.html)
with `(Error e) => Either e a`. We all know, that this is most convenient, especially
because of the do-notation to chain computations which can fail:
{% highlight haskell %}
foo :: Either String Result
foo = do
  x <- bar
  y <- baz x
  return y
{% endhighlight %}
`Either` also is the Haskell name for [coproducts](http://en.wikipedia.org/wiki/Coproduct),
![Either Coproduct](/tikz/either_coproduct.png)

which means
```python
x.show()
```





