---
layout: post
title: "Forget Scala! A Java Backend for Idris"
description: ""
category: Compilation
tags: [Idris, Java, Compilers]
---
{% include JB/setup %}

My [pull request](https://github.com/edwinb/Idris-dev/issues/221) for
Idris was accepted. The latest version can now compile Idris to Java.

### Quick start

1. install the development version from git {% highlight sh %}
$ git clone https://github.com/edwinb/Idris-dev
$ cd Idris-dev
$ make
{% endhighlight %}
2. compile your source with Java as target {% highlight sh %}
$ idris someCode.idr -o someCode.java --target Java
{% endhighlight %}
3. update your classpath to include the idris runtime, e.g. by {% highlight sh %}
$ export CLASSPATH="~/Idris-dev/java/idris.jar:$CLASSPATH"
{% endhighlight %} Note that Java 7 is required.  
4. compile and run the generated Java source {% highlight sh %}
$ javac someCode.java
$ java someCode
{% endhighlight %}
Note that the heavy use of inner classes will create a lot
of .class files in your compilation directory.

Make sure to take a look at the test suite, that comes with idris.
It contains a lot of examples and is adapted to test the Java backend
as well.

### Behind the scenes

Idris internally compiles to a small defunctionalized applicative
language:
{% highlight haskell %}
data SExp = SV LVar
          | SApp Bool Name [LVar]
          | SLet LVar SExp SExp
          | SUpdate LVar SExp
          | SCon Int Name [LVar]
          | SCase LVar [SAlt]
          | SChkCase LVar [SAlt]
          | SProj LVar Int
          | SConst Const
          | SForeign FLang FType String [(FType, LVar)]
          | SOp PrimFn [LVar]
          | SNothing -- erased value, will never be inspected
          | SError String
  deriving Show

data SAlt = SConCase Int Int Name [Name] SExp
          | SConstCase Const SExp
          | SDefaultCase SExp
  deriving Show

data SDecl = SFun Name [Name] Int SExp
  deriving Show
{% endhighlight %}
The main challenges in compiling this mini language to anything else
are name mangling, how to represent closures and how to map types and
foreign function calls. I use the 
[language-java](http://hackage.haskell.org/package/language-java) package
to create abstract syntax trees instead of writing code by hand. This 
way a lot of errors are avoided by proper typing during compile time.

#### Name mangling
Additionally to the usual escaping of unicode characters, the Java
backend translates namespaces to (static) inner classes of the top level
class, which will be named according to the target file name. Functions
will be translated to static member functions of those classes. So the
`foo` function of the `Bar` module from `someFile.java` will
go to `someFile.Bar.foo()`.

#### Closures
Closures are represented by anonymous inner classes inheriting from
`org.idris.rts.Closure`. `Closure` is a standard Java `Callable` (and
`Runnable`) with an `Object []` array to hold references to all bound
variables.

#### Types
Primitive types from idris are mapped to their Boxed Java correspondents
(e.g. `Int` to `Integer`). The usage of boxed types in Java is necessary
to be able to get "Pointer"-like behavior. Complex idris types are mapped
to instances of `org.idris.rts.IdrisObject`. `IdrisObject` contains an
int to specify which constructor was used and an `Object []` array to
hold all members.

#### Foreign functions
Calls to foreign functions are injected "as is" into the java code.
The class `org.idris.rts.ForeignPrimitives` contains java counterparts
for all standard C-functions which idris internally is built on. These
look quite strange (e.g. `feof`), since about every design choice for
Java file handling is different from C, making it hard to emulate the
expected behavior.

#### Parallelism
Parallelism is supported using fork to create new lightweight Threads
and a primitive message passing implementation in `ForeignPrimitives`.

### Future work
All this is still quite inefficient and a bytecode version certainly
would be better. Further the interfacing part still needs some polish,
e.g. for calling idris from Java. And the IO primitives need a general
cleanup for all backends. While we cannot yet scrap Scala, I'd love
to see a real world example of Java code interacting with Idris to do
something awesome :).
