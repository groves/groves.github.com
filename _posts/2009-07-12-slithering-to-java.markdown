---
layout: post
title: Slithering Into Java's Heart
---

Jython has a pretty good relationship with Java. Python code seamlessly calls any Java code and
Python classes can subclass Java classes and implement Java interfaces. However the relationship in
the other direction is a little more awkward. To call Python code from Java, one either uses jythonc
to compile the Python source to Java source, or exposes Python classes through [Java code that
exists solely to instantiate it](http://wiki.python.org/jython/JythonMonthly/Articles/October2006/3).

Since jythonc is completely gone in Jython 2.5, and the wrapping is painful at best, one of the goals for Jython 2.5.1 is to make the Java to Python relationship as cordial as the Python to Java relationship. I administered the first congeniality injection to Jython's trunk earlier this weekend in the form of the ability to name Jython's proxy classes and to write them out to disk.

To explain why this makes things more pleasant from Java requires a little background on how Jython allows Python classes to subclass Java classes. Every time a Python class subclasses a Java class, Jython dynamically generates a proxy class to that Java class. The proxy class overrides all of the methods of Java classes and implements every method coming from Java interfaces. In its implementations of the Java methods, it dispatches from Java into an instance of the Python class it represents by looking up that method name on the instance and calling whatever it finds with the arguments the method received.

Up until this weekend, this was an entirely dynamic process. The proxy class would be generated at the time the Python class was created, and the Java class name of the proxy was created by a combination of the Python module and class and a counter that was incremented with every created proxy. This made things difficult for Java code in a couple ways. First, since the proxy classes were always generated dynamically, Java source couldn't import or call them directly as there weren't classes around at compile time for Java to work with. Second, since the names in part depended on the order in which the proxy classes were created, even if you were able to get your Java code to wait to resolve the classes till runtime, you couldn't depend on the generated class names for things like Class.forName and other Java reflection facilities.

This weekend's commits addresses both of these issues. The proxy classes can now be written to disk
by setting sys.javaproxy_dir from Python or `Py.getSystemState().javaproxy_dir` from Java. Setting
that field causes all proxy classes generated after that point to be written to the given directory
when they're created. The non-deterministic proxy class name problem is addressed by setting
`__javaname__` in a Python class. If that's set, the proxy class is given the name from
`__javaname__`.

This opens up some nice possibilities. If you generate the proxies for your code with a fixed name,
you can add them to your Java classpath and import and use them normally. You can even pass the
classes to something like Guice to let it inject Python classes into your Java code.
[clamp](http://github.com/groves/clamp) uses
`__javaname__` to automatically set the names of its generated proxies to the fully-qualified name of the Python class, so you don't need to specify those by hand. Combined with its method and constructor generation code, clamp will let you write Python classes that can be used from Java without scaffolding Java interfaces and classes to Python to extend.

There's still a fair amount of work to do before this code makes it into a real release: now that
Jython proxies can persist beyond the lifetime of a JVM, they'll need to grow a version number so we
can safely change them in later releases; clamp still has several items on its [0.1 TODO
list](http://github.com/groves/clamp/blob/b7b8b03a3b65cf2d236fc49bec47afaa61ce9588/TODO); Jython needs tools to generate static proxies for its standard library and to allow users to generate static proxies for their own libraries, so Jython can be used in environments that don't allow classes to be created dynamically as jythonc once allowed. However all of that work is still much less than what went into 2.5.0, so it shouldn't be too long before 2.5.1 starts to bridge the Java to Python gap.

