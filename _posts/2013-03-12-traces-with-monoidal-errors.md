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
void \*foo(...) {
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

A lot has been
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
{% highlight haskell %}
recover = either recover use . Left
use = either reover use . Right
{% endhighlight %}
Where either is the unique function
{% highlight haskell %}
either f _ (Left x) = f x
either _ g (Right y) = g y
{% endhighlight %}
and equality is up to Haskell's reduction and alpha renaming. If an initial 
neutral element exists, coproducts can be turned into monoids. This element
exists for the typeclass `Error` because of `noMsg : (Error e) => e`.
{% highlight haskell %}
instance (Error e) => Monoid (Either e a) where
  mempty = Left noMsg
  (Left _) `mappend` x = x
  res@(Right _) `mappend` _ = res
{% endhighlight %}
Combined with the monadic properties, this can as well be expressed
as a `MonadPlus` instance. It is well known, that this interface allows
elegant backtracking
{% highlight haskell %}
backtrack :: (Error e) => [Either e a] -> Either e a
backtrack = foldl1 mappend
{% endhighlight %}
`backtrack` returns the first computation that did not fail. However the amiable
reader might have noticed that the second problem mentioned above is not
solved:
{% highlight haskell %}
(Left err1) `mappend` (Left err2)
{% endhighlight %}
will drop `err1` and only return `err2`. Luckily there is an easy improvement
on the `Monoid` (or `MonadPlus`) instance of `Either `. What is really missing
is a way to combine multiple errors and preserve the monoid properties. The
easiest way to obtain the missing piece is to add a monoid requirement on the
error type argument:
{% highlight haskell %}
instance (Error e, Monoid e) => Monoid (Either e a) where
  mempty = Left mempty
  (Left e1) `mappend` (Left e2) = Left (e1 `mappend` e2)
  (Left e1) `mappend` res@(Right _) = res
  res@(Right _) `mappend` _ = res
{% endhighlight %}
Even though this approach is most simplistic, it just works :). Error types for
which no combination is available can easily resort to the standard behavior:
{% highlight haskell %}
instance Monoid StandardError where
  mempty = Left noMsg
  err `mappend` _ = err
{% endhighlight  %}
A full blown instance declaration (including a monad transformer to stack
the new error mechanism) can be found [here](https://github.com/JanBessai/Monoid-Error).
Let me know, if you need anything else (more instances, an upload to hackage, ...).
