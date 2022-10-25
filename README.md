# AIX5-IA64

## Introduction

A modest collection of compiled software for **IBM AIX 5.1 for Itanium** (IA-64), previously known as [*Project Monterey*](https://en.wikipedia.org/wiki/Project_Monterey).

## Overview

* All packages extract to '`./opt/freeware`' and may be unpacked from the root directory ('`/`') for installation.

* All packages are built statically to avoid complex package interdependencies, unless otherwise indicated.

## Notes

* Care has been taken to retain interoperability with the installed system tools; users who prefer a GNU-style shell environment may safely add `/opt/freeware/bin` to the start of their `PATH`.

  * Some binaries have been renamed to avoid conflicts (*e.g.* `gmake`) when the packaged tool is incompatible with the system tool.

* Installation of the `ncurses` package is always recommended; `ncurses` applications (*e.g.* `aclock`, `elvis`, `g`, *etc.*) will load terminal definitions at runtime.

* `gcc-3.1.1-1.tar.Z` depends on the GNU assembler (`gas`) included in the `binutils` package.  The *bootstrap* compiler is an experimental standalone cross-compiled variant useful for compiler development but not required for regular usage.

* The `elvis` package is compiled with basic X11 support.  The required X11 system libraries are included in the default installation.

## Errata

* `mksh-r59_20220928-1` has a bug affecting signal handling and delivery; the cause is under investigation.
