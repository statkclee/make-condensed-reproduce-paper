---
layout: page
title: Automating Tasks with Make
subtitle: Reference
---
FIXME

## Glossary

action
:   The steps a **build manager** must take to bring a file or other
    object up to date.

automatic variable
:   A variable whose value is automatically redefined for each rule.
    Automatic variables include `$@`, which holds the rule's
    **target**, and `$^`, which holds its **prerequisites**.
    Automatic variables are typically used in **pattern rules**.

build file
:   A description of dependencies and rules for a **build manager**.

build manager
:   A program such as Make whose main purpose is to rebuild software,
    documentation, web sites, and other things after changes have been
    made.

default rule
:   The rule that is executed if no other rule is specified.

dependency
:   A file that some other file depends on.  If any of a file's
    dependencies are newer than the file itself, the file must be
    updated.  A file's dependencies are also called its
    **prerequisites**.

false dependency
:   A "dependency" used to trigger some other action.

Makefile
:   The default build file used by Make.  The term is also used
    for any **build file** that Make understands.

pattern rule
:   A rule that specifies a general way to manage an entire class of
    files.  For example, a pattern rule might specify how to compile
    any C file, rather than just a particular C file.  Pattern rules
    typically make use of **automatic variables**.

phony target
:   A **target** that does not correspond to a file or other object.
    Phony targets are usually just symbolic names for sequences of
    actions.

prerequisites
:   A file that some other file depends on.  If any of a file's
    prerequisites are newer than the file itself, the file must be
    updated.  A file's prerequisites are also called its
    **dependencies**.

rule
:   In a build system, a specification of a **target**'s
    **prerequisites** and what **actions** to take to bring the
    target up to date.

target
:   A thing that may be created or updated.  Targets typically have
    **prerequisites** that must be up to date before the target itself
    can be updated.

variable
:   A symbolic name for something in a **Makefile**.
