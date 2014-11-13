---
layout: page
title: Automating Tasks with Make
---
Originally invented to manage compilation of programs, `make` can be
used to automatically execute sequences of commands (programs) and
update any set of files that depend on another set of files. This
makes it a good solution for many data analysis and data management
problems, including the generation of images from data.

Programs that `make` will execute are described by a text file (almost
always called `Makefile`) containing a list of commands, called
*rules*, and the files that they create, called *targets*. The rules
describe how files depend on each other, and how to update out-of-date
files. They can be very specific, useful to generate one file, or
general, specifying a pattern for creating a certain class of files.

> ## Prerequisites
>
> Learners must be familiar with basic shell commands to create files
> and directories.

## Topics

1.  [Introduction](01-intro.html)
2.  [Basic Tasks](02-basics.html)
3.  [Automatic Variables and Wildcards](03-automatic-variables.html)
4.  [Patterns](04-patterns.html)
5.  [Variables](05-variables.html)

## Other Resources

* [Motivation](motivation.html)
* [Reference Guide](reference.html)
* [Next Steps](discussion.html)
* [Instructor's Guide](instructors.html)
