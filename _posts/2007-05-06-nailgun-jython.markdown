---
layout: post
title: Nailgun Your Way to a Quick Jython Startup
---

[Charles Nutter](http://headius.blogspot.com/) just mentioned
[Nailgun](http://www.martiansoftware.com/nailgun/index.html) on jruby-dev as a possible way to speed up jruby startup time. Turns out it works pretty well for jython too.

Regular Jython:

    groves@muhngole ~/Desktop/nailgun-0.7.1
    $ time java -jar d:/home/groves/dev/jython/trunk/dist/jython.jar -c 'print "hello"'
    hello

    real    0m2.390s
    user    0m0.015s
    sys     0m0.015s


Nailgunned Jython:

    groves@muhngole ~/Desktop/nailgun-0.7.1
    $ time ./ng.exe org.python.util.jython -c 'print "hello"'
    hello

    real    0m0.062s
    user    0m0.015s
    sys     0m0.015s

CPython:

    groves@muhngole ~/dev/jython/trunk
    $ time python -c 'print "hello"'
    hello

    real    0m0.093s
    user    0m0.030s
    sys     0m0.093s


So with Nailgun, Jython can print 'hello' more quickly than CPython itself. Unfortunately, much of the time of the regular Java invocation of Jython is spent initializing Jython so the first run of Jython from inside Nailgun is actually a little slower than a regular run. After that, all of the initialization is done inside of Nailgun's VM and things run pretty quickly as shown in the numbers above.

From within Nailgun's directory it's just a three step process to run Jython:

1. java -jar nailgun-0.7.1.jar
1. ./ng ng-cp jython.jar
1. ./ng org.python.util.jython -c 'print "hello"'

Since Nailgun is just passing its command line arguments through to Jython, you can just pass the script you want Jython to run to ng instead of the -c option I'm using as an example.

