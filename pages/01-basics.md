---
layout: topic
title: Basic Tasks
minutes: 30
---
> ## Learning Objectives {.objectives}
> 
> * show the basic `Makefile` syntax
> * show how to run `make` from the commandline
> * explain how rules and targets determine commands to be executed
> * show the default target and the default Makefile

To illustrate how `make` works, here's the dependency tree for the paper that the robot is working on.
`paper.pdf` depends on `paper.wdp` (the raw word processor file),
and on `figure-1.svg` and `figure-2.svg`.
`figure-1.svg` depends on `summary-1.dat`,
which in turn depends on `data-1-1.dat`, `data-1-2.dat`, and so on,
while `figure-2.svg` depends on files with similar names.

In order to create `paper.pdf`, we have to run some command (maybe `latexmk`).
For the purpose of this lecture, it doesn't matter what `latexmk` actually does
and we will not use this command further.
All we need to know is that if `paper.tex` or either of the figure SVG's change, we need to
re-build `paper.pdf`.

To create `figure-1.svg`, we run `python create_figure.py figure-1.svg summary-1.dat`.
Again, it doesn't matter for now what `create_figure.py` command actually is.
What matters is that we need to run it whenever `figure-1.svg` is out of date,
i.e., whenever it is older than the `summary-1.dat` file it depends on.
Finally, in order to update `summary-1.dat`, we need to run our own little script, `stats.py`,
with all the files named `data-1-something.dat` as input.
We don't know in advance how many of these there will be: we could conceivably have dozens or hundreds of raw data files to summarize.

That little program `stats.py` adds one more wrinkle to our example problem.
We're constantly updating it as we think of new ways to process our raw data files.
We're also finding and fixing bugs more often than we'd like.
Each time it changes, we should probably update `summary-1.dat`,
just in case a new feature or bug fix changes the summary values.
We should therefore include `stats.py` in the list of things `summary-1.dat` depends on,
so that changes to `stats.py` will trigger recalculation of `summary-1.dat`.

This is all a bit much to digest at once, so let's look at the simplest piece.
How can we get `make` to re-create `figure-1.svg` automatically whenever `summary-1.dat` changes?

Let's start by going into the directory containing the files we're using in the paper,
and use the `ls` command to get a listing of what's there.
The `-t` flag to `ls` tells it to list ordered by age,
with the youngest file first and the oldest last:

~~~ {.make}
$ ls -t *.dat *.svg
summary-1.dat    figure-1.svg
~~~

This listing tells us that our data file `summary-1.dat` is newer than the SVG file that depends on it,
so the SVG file needs to be re-created.
Using our favorite editor, let's create a file called `hello.mk` and put these three lines in it.

~~~ {.make}
# hello.mk
figure-1.svg : summary-1.dat
    python create_figure.py figure-1.svg summary-1.dat
~~~

A configuration file for `make` like this one is called a **Makefile**.
The first line, starting with `#`, is a comment.
(Our comments should be more meaningful than just the name of the file.)
The second and third lines are a **rule** that tell `make` what we want to do.

The filename on the left of the colon in the first line is the **target** of the rule.
The rule tells `make` how to update or re-create this file.
The target's **prerequisites** --- the things it depends on --- are listed to the right of the colon.
In our case, `figure-1.svg` only has one prerequisite, `summary-1.dat`.

The second line of the rule is its **action**.
This tells `make` what shell command or commands to run
to bring the target up to date if it is older than any of its prerequisites.
This rule only has one command, but a rule can contain any number.

One thing to note is that the actions in rules *must* be indented with a single tab character.
`make` will not accept spaces, or mixes of spaces and tabs.
(As we said in the introduction, it was written by a summer intern in 1975, and sometimes that shows.)

Now that we've created our `Makefile`, we can tell `make` to execute its
instructions by running it from the command line:

~~~ {.make}
$ gmake -f hello.mk
python create_figure.py figure-1.svg summary-1.dat
~~~

> ## Flavors of Make {.callout}
>
> Nowadays, `make` is almost synonymous with the most popular implementation, GNU Make.
> But on some systems GNU Make may be installed as `gmake`, and the command
> `make` be either unavailable, or point to a different Make implementation.
> So if the former doesn't work for you, try the latter name as well.

The arguments `-f hello.mk` tell `make` that we want it to use the commands in the file `hello.mk`.
If we don't tell it what file to look in,
it looks for a file called `Makefile` in the current directory and uses that if it exists.

`make`'s output shows us that it has run the command we wanted it to.
It did this because at least one prerequisite for `figure-1.svg` was newer than `figure-1.svg` itself.
By default, `make` uses the time a file was last modified as its age.
(Opening a file in an editor to view it doesn't change this timestamp, but any change to its contents will.)
Since `summary-1.dat`'s timestamp was younger than `figure-1.svg`'s,
`make` ran the shell command we gave it and created a new version of `figure-1.svg`.

Let's run `make` again:

~~~ {.make}
$ gmake -f hello.mk
~~~

This time, it doesn't execute any commands.
This happened --- or didn't --- because the target is newer than its prerequisites.
Since there's nothing to bring up to date, `make` doesn't change anything.

If we were only allowed one rule per file,
`make` wouldn't be any simpler than typing commands by hand or putting them in little shell scripts.
Luckily, `make` allows us to put any number of rules in a single configuration file.
Here is another Makefile called `double.mk` with rules to re-create
both `figure-1.svg` and `figure-2.svg`.
These rules are identical except for the 1's and 2's in the filenames; we'll see later how to combine these rules into one.

~~~ {.make}
# double.mk
figure-1.svg : summary-1.dat
    python create_figure.py figure-1.svg summary-1.dat

figure-2.svg : summary-2.dat
    python create_figure.py figure-2.svg summary-2.dat
~~~

Let's pretend we've just updated our data files by running `touch *.dat`.
(The Unix `touch` command doesn't change the contents of files,
but updates their timestamps as if they had been modified.)
Now, when we run `make`, it re-creates `figure-1.svg` again --- and then stops:

~~~ {.input}
$ touch *.dat
$ gmake -f double.mk
~~~
~~~ {.output}
python create_figure.py figure-1.svg summary-1.dat
~~~

Why wasn't `figure-2.svg` re-created?
The answer is that `make` uses the first rule in the Makefile as its **default rule**.
Unless it's told otherwise, it only executes this rule.
If we want `make` to rebuild `figure-2.svg`, we have to tell it so explicitly.
We use `-f double.mk` to tell `make` what Makefile to use,
and then give it the name of the target we want it to handle:

~~~ {.input}
$ gmake -f double.mk figure-2.svg
~~~
~~~ {.output}
python create_figure.py figure-2.svg summary-2.dat
~~~

Again,
building things one at a time like this is slightly better than typing individual commands,
but only slightly.
To get `make` to build everything at once, we have to introduce a **phony target**.
This is just a target name that doesn't correspond to any actual file.
Since it doesn't actually exist,
it can't ever be up to date,
but other things can still depend on it.
Here's our third Makefile, `phony.mk`:

~~~ {.make}
# phony.mk

all : figure-1.svg figure-2.svg

figure-1.svg : summary-1.dat
    python create_figure.py figure-1.svg summary-1.dat

figure-2.svg : summary-2.dat
    python create_figure.py figure-2.svg summary-2.dat
~~~

We've introduced a phony target called `all`, which depends on `figure-1.svg` and `figure-2.svg`.
Since there's no file called `all` in the current directory,
if we type `make all`,
`make` will decide that the `all` target is out of date.
And since `all` depends on `figure-1.svg` and `figure-2.svg`,
`make` will go and update them both, which is exactly what we want.

Let's `touch` our data files again, and run `make -f phony.mk all`.
Sure enough, `make` runs the `sgr` command twice to re-create both figures:

~~~ {.input}
$ touch *.dat
$ gmake -f phony.mk
~~~
~~~ {.output}
python create_figure.py figure-1.svg summary-1.dat
python create_figure.py figure-2.svg summary-2.dat
~~~

> ## Order of Execution {.callout}
>
> One thing to note is that the order in which commands are executed is arbitrary.
> `make` could decide to update `figure-2.svg` first, rather than `figure-1.svg`,
> because there's no dependency to respect between the two.
> `make` could also update them in parallel if it had more than one processor to use --- we'll return to this idea later.

> ## Multiple Roles {.callout}
>
> A single thing can be a target in one rule, and a prerequisite in others.
> The dependencies between the files mentioned in the Makefile make up a directed graph.
> In order for `make` to run, this graph must not contain any cycles.
> For example, if X depends on Y, Y depends on Z, and Z depends on X,
> everything depends on something else, so there is nothing `make` can build first.
> If it detects a cycle in a Makefile, `make` will print an error message and stop.
> Unfortunately, whether or not a cycle exists depends on which files exist,
> and `make`'s error message is usually not particularly informative.

> ## Making Another Figure {.challenge}
>
> You want to make another figure called `figure-2-copy.svg` that is a
> copy of `figure-2.svg`.  Create a make file that has
> `figure-2-copy.svg` depend on `figure-2.svg` and updates
> `figure-2-copy.svg` appropriately.
