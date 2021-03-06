# abtime

[![Build Status](https://travis-ci.org/thejerf/abtime.png?branch=master)](https://travis-ci.org/thejerf/abtime)

    go get github.com/thejerf/abtime

A library for abstracting away from the literal Go time library, for testing and time control.

In any code that seriously uses time, such as billing or scheduling code,
best software engineering practices are that you should not directly
access the operating system time. This module provides you with code to
implement that principle in Go.

See some discussions:

* [blog.plover.com's Moonpig discussion](http://blog.plover.com/prog/Moonpig.html#testing-sucks)
* [Jon Skeet's discussion on Stack Overflow](http://stackoverflow.com/questions/5622194/time-dependent-unit-tests/5622222#5622222)
* [Jim McBeath's post](http://jim-mcbeath.blogspot.com/2009/02/unit-testing-with-dates-and-times.html)

This module is fully covered with
[godoc](http://godoc.org/github.com/thejerf/abtime), including examples,
usage, and everything else you might expect from a README.md on GitHub.
(DRY.)

# Changelog

* 1.0.3:
  * Fix locking for Unregister and UnregisterAll.
* 1.0.2
  * Adds support for unregistering triggers, so the ids can be reused with
    the same abtime object.

    As the godoc says, this is a sign of some sort of flaw, but it is not
    yet clear how to handle it. I still haven't found a good option for an
    API for this stuff. My original goal with abtime was to be as close to
    the original `time` API as possible, I'm considering abandoning
    that. Though I still don't know what exactly that would look like.

    (Plus, this need some sort of context support now.)
* 1.0.1
  * [Issue 3](https://github.com/thejerf/abtime/issues/3) reports a
    reversal in the sense of the timer.Reset return value, which is
    fixed. While fixing this, a race condition in setting the underlying
    value was also fixed.
* 1.0.0
  * Initial Release.

# On the Abtime API

Perusing the abtime API will quickly reveal that unlike other clock-mocking
libraries in Go, the abtime API introduces additional `ID` parameters to
the calls you make, which are ignored when using real time.

This is because as nice as the API may be, it is a mistake for a
clock-mocking library to bake in the assumption that your code can
assume the existence of a monotonic time, because in multi-threaded code,
you can not count on that. Just because you have alarm A firing in
1000ms in one thread and alarm B firing in a different thread in 1001ms,
that does not mean that you can assume A will fire before B. That's an
error in multithreading code, and in my opinion, any mocking library needs
to be able to help you test that even if B fires first, everything will
still work as desired. Writing into the API that the only way to trigger
alarms and such is by monotonically advancing the clock precludes this
possibility from the beginning, however convenient it may be.

Abtime uses additional parameters to get around that.

You can still get into situations where testing is difficult or
inconventient. However I have not been able to work out an API that is any
better or safer, or I'd make a 2.0. Generally what's been provided by this
library has been enough.

In my personal experince, the only thing I use the clock advancing for
is test stability for code that needs to add time stamps for things.
When testing triggers and alarms, it's often perfectly sufficient to
just trigger them and not advance the clock at all. It's generally the
logic you're testing, and the various paths code can execute through, not
actual time handling itself. So in practice I don't generally miss having
the nice "just advance the clock" API as much as you would think.

# Stability

As I have been using this code for a while now and it has stopped changing,
this is now at version 1.0.0.

# Commit Signing

Starting with the commit after 3003eee879c, I will be signing this repository
with the ["jerf" keybase account](https://keybase.io/jerf). If you are viewing
this repository through GitHub, you should see the commits as showing as
"verified" in the commit view.

(Bear in mind that due to the nature of how git commit signing works, there
may be runs of unverified commits; what matters is that the top one is
signed.)

