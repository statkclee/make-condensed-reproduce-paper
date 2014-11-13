
The default target and the default Makefile
-------------------------------------------

In the previous section, a phony target `all` was added to make it
easy to remake "all" targets. It turns out that many Makefiles have a
target with this meaning and this name. If a target is not specified
on the command line, `make` will use `all` as the default:

    $ make -f phony.mk
    make: Nothing to be done for 'all'.

Does the name make it special?
No, target `all` is special simply because it specified *first*
in the Makefile. Nevertheless, it is customary to call it just that.

What happens if the Makefile name is not specified using the `-f`
switch? `make` by default will use `Makefile` as the name of the
Makefile. This means that our Makefile should usually be called just
that. Combining the use of the default target and the default Makefile
allows us to remake everything by simply saying `make` (without
further parameters).
