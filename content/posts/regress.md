---
title: "regress - Regression testing"
date: 2017-10-04T00:00:00-04:00
subtitle: "What's the big idea?"
tags:
- Regress
- Regression
- Regression testing
- Testing
- Testing framework
---
#### Where did the idea come from?
Regress arose from my simple every day need of testing newly
written programs and scripts with a bunch of different inputs
against the expected outputs.

#### What do I mean you ask? Here's an example:
Imagine you are developing a new ```wc``` that is faster than the
one installed by default on your operating system. Below is your
working directory:
```
$ ls
in1  out1 wc_own
```

While:
```
$ cat out1
       1      73     503
$ cat in1
Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Aenean
commodo ligula eget dolor. Aenean massa. Cum sociis natoque
enatibus et magnis dis parturient montes, nascetur ridiculus mus.
Donec quam felis, ultricies nec, pellentesque eu, pretium quis,
sem. Nulla consequat massa quis enim. Donec pede justo, fringilla
vel, aliquet nec, vulputate eget, arcu. In enim justo, rhoncus ut,
imperdiet a, venenatis vitae, justo. Nullam dictum felis eu pede
mollis pretium. Integer tincidunt. Cras dapibus.
```

Regress simplifies the process of running these tests in the
future. Just put each one of your test inputs into a file starting
with the input prefix and put its expected output into a file
with the same name, where the input prefix is replaced by the
output prefix.

In the given simple example this is how you would run 10s, or 100s
of tests:
```
regress ./wc_own
```

Regress will collect all the input files, run them through the
given command and compare the output against the expected ones,
reporting the differences.

Regress was designed to be modular, so all the prefixes, the input
and output files' location can be defined by flags.

If this sounds like something you could use don't hesitate to
download it and give it a go. We could also use some help from
the community, so if you are looking for a good first time
open-source project, fork away! If you have some feature
requests, or ideas open up a Issue and we will discuss it there.

[Check out regress on GitHub](https://github.com/keller00/regress)
