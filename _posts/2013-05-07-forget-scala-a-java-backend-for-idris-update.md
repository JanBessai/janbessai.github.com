---
layout: post
title: "Forget Scala! A Java Backend for Idris (Update)"
description: ""
category: Compilation
tags: [Idris, Java, Compilers]
---
{% include JB/setup %}
A lot has changed since my last post on the Java Backend for Idris.
### Quick start

1. install the development version from git {% highlight sh %}
$ git clone https://github.com/edwinb/Idris-dev
$ cd Idris-dev
{% endhighlight %}
2. enable the backend in `config.mk` {% highlight make %}
## Enable Java RTS:
CABALFLAGS :=-f Java
{% endhighlight %}
3. install Idris {% highlight sh %}
$ make
{% endhighlight %} Make sure you have [maven](http://maven.apache.org/) installed and in your path
4. compile your own source with Java as target {% highlight sh %}
$ idris someCode.idr -o someCode.java --target Java
{% endhighlight %}
5. run the generated Java source {% highlight sh %}
$ ./someCode.jar
{% endhighlight %}

### Behind the scenes

Setting `CLASSPATH` and invoking `javac` by hand is a tedious job
which can be automated.  To achive this Idris now uses maven. Since
maven is not installed on all computers and many people just want to
use the C backend, Java is disabled by default and has to be enabled
by the cabal flag `-f Java`.

#### RTS

The runtime system for java targets is located in the
subfolder `/java` of your source tree obtained via git. It will be
compiled and installed for you by cabal which is invoked by
make. Cabal itself will first adapt `/java/pom_template.xml` to
include the correct version information and then invoke `mvn install`
on the resulting `pom.xml` in order to install the runtime system in
your local maven repository. The runtime system itself is a small
`.jar` including base classes for idris objects (tagged by their
constructor id), closures (aka runnables), tail call closures
(recursive calls reduced to a loop) and a collection of primitive io
functions (file access, message passing, ..), which mimic the
interface of the C backend.

#### Compilation

Instead of just creating a `.java` file and relying
on you to compile it by hand, idris will now create a maven project
and compile + bundle the source for you. If you'd rather resort to the
old behavior just pass `-S` during compilation - this way you only
get a `.java` file (and you even can skip adding build flags to
cabal). Another option is to pass `-c`, which will extract all
compiled `.class` files and not bundle the project in a
jar. Internally idris will setup a new maven project in the temp
directory of your system.  This project is described by a pom created
from the template you find in `/java/executable_pom_template.xml` in
the source tree. The template includes a dependency for your
previously compiled runtime system and it will use
(http://maven.apache.org/plugins/maven-shade-plugin/)[shade] in order
to create a standalone executable `.jar` with all dependencies in it.
The resulting `.jar` is automatically prefixed by a header
including an `.sh` script to allow [executing it directly](https://coderwall.com/p/ssuaxa).

#### Foreign functions

The new build process has improved foreign functions a lot. As always
this is best illustrated by an example:
{% highlight haskell %}
module Main

%include "com.google.common.math.IntMath"
%lib "com.google.guava:guava:14.0"

binom : Int -> Int -> IO Int
binom n k = mkForeign (FFun "IntMath.binomial" [FInt, FInt] FInt) n k

main : IO ()
main = do print "The number of possibilities in lotto is 49 choose 6:"
          res <- binom 49 6
          print res
{% endhighlight %}
The `%include` directive will add `import
com.google.common.math.IntMath` to the generated java code. Further
the `%lib` directive will advice maven to add a dependency on the
[google guava](http://search.maven.org/#artifactdetails|com.google.guava|guava|14.0|bundle)
artifact. Coordinates are given by "groupId:artifactId:packaging:version" as
[usual](http://maven.apache.org/pom.html#Maven_Coordinates).
The rest of the code shows how to create a foreign function call to the static
function
[binomial](http://docs.guava-libraries.googlecode.com/git-history/release/javadoc/com/google/common/math/IntMath.html#binomial(int,%20int))
from `IntMath`. Right now only calls to static functions are available,
so you'd have to create wrappers for objects. I am however working on
an [improvement](https://github.com/edwinb/Idris-dev/blob/master/java/src/main/java/org/idris/rts/ForeignPrimitives.java#L252-L270) to remove the need for clumsy wrappers. When compiled
with `--target Java` the above code will cause maven to automagically download
guava and bundle it into the resulting `.jar`, so there is no need to manually
download stuff or set any `CLASSPATH` variables.

Stay tuned for more updates, even tough it may take a while, because
I have to finish my last exam ([pattern recognition](http://ls12-www.cs.tu-dortmund.de/patrec/teaching/WS11/mustererkennung/index.html)) and start my master's thesis.
