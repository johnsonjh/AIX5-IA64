# AIX/IA64 Software Repository

[Project Monterey runs again!](https://virtuallyfun.com/2022/09/24/ibm-aix-for-ia64-itanium-aka-project-monterey-runs-again/)

## Introduction

This repository contains a modest collection of ready-to-run precompiled software packages for **IBM AIX 5.1L for Itanium** (IA-64), previously known as [*Project Monterey*](https://en.wikipedia.org/wiki/Project_Monterey).

## Overview

* **These are Itanium (IA-64) packages!**  They **will** ***not*** **work** on standard IBM POWER systems!
* All packages extract to '`./opt/freeware`' and may be unpacked from the root directory ('`/`') for installation.
* All packages are built statically to avoid complex package interdependencies, unless otherwise indicated.

## Notes

All users of these packages should read the following notes:

* Care has been taken to retain interoperability with the installed system tools; users who prefer a GNU-style shell environment may safely add '`/opt/freeware/bin`' to the start of their `PATH`.
  * Some binaries have been renamed to avoid conflicts (*e.g.* `gmake`) when the packaged tool is incompatible with the system tool.

[]()
* Installation of the `ncurses` package is always recommended; `ncurses` applications (*e.g.* `aclock`, `elvis`, `g`, *etc.*) will load terminal definitions at runtime.

[]()
* Shells intended for interactive login use (*e.g.* `mksh`, `bash`, *etc.*) should be added to **both** '`/etc/shells`' and '`/etc/security/login.cfg`'.

[]()
* `gcc-3.1.1-1.tar.Z` depends on the GNU assembler (`gas`) included in the `binutils` package.  The *bootstrap* compiler is an experimental standalone cross-compiled variant useful for compiler development but not required for regular usage.

[]()
* The `elvis` editor is compiled with basic X11 support.  The required X11 system libraries are included in the default AIX installation.

## OpenSSH

`openssh-9.1p1-1.tar.Z` is now available as an **experimental** (but completely functional) package!  *Hooray*!

[]()
  1. **Only pubkey-based authentication is supported by the sshd daemon.**
     * All other authentication methods are currently unsupported.
  2. **utmp/wtmp/btmp is not updated by the sshd daemon.**  Use `ps` to view `sshd` logins as workaround:
     * `ps auxw | grep 'sshd: ' | grep -Ev '(^root|grep.*sshd: )'`
       * The following shell aliases, while optional, may be useful:
         * `alias w="w;ps auxw | grep 'sshd: ' | grep -Ev '(^root|grep.*sshd: )'"`
         * `alias who="who;ps auxw | grep 'sshd: ' | grep -Ev '(^root|grep.*sshd: )'"`
  3. **The `sshd` daemon requires an `sshd` user and group to be configured.**
     * The `sshd` group must be the primary group of the `sshd` user.
     * The `sshd` user account must be locked, but with logins allowed.
     * The `sshd` user should be configured to use `/usr/bin/false` as the login shell.
       * `/usr/bin/false` **must** be configured as a valid login shell (in `/etc/shells` and `/etc/security/login.cfg`)
     * The home directory of the `sshd` user should be `/var/empty`, with 755 permissions.
  4. **AIX/IA64 5.1L provides no CSPRNG.**  Only system state is available.  As a workaround:
     * The `prngd` daemon (*not yet packaged*) is supported as an entropy source.
  5. **The security of the `sshd` daemon (and AIX/IA64 5.1L in general) is not guaranteed.**
     * The AIX/IA64 5.1L system should **not** be directly exposed to the Internet.
     * Use of a properly secured firewall and/or bastion/jump host is strongly recommended.
  6. **The performance of the `sshd` daemon is lackluster.**  As a workaround:
     * Avoid the `rsa` and `ecdsa` key algorithms;  use `ed25519` instead.
     * Avoid the `3des` cipher; use `aes` or `chacha20` instead.
     * Avoid the SSH compression feature.
  7. **Specify a log file path when starting the `sshd` daemon.**  For example:
     * As `root`: `/opt/freeware/sbin/sshd -E /var/tmp/ssh.log`.
  8. **No SRC service configuration is currently provided for the `sshd` daemon.**
     * SRC setup (documented elsewhere) is left as an excercise for the reader, *or*, 
     * `sshd` may be started from from the standard `/etc/rc.local` script.
  
## Errata

While **no guarantees** are made, all packages are thoroughly tested, and the following issues have been identified:

[]()
* `mksh-r59_20220928-1` has a bug affecting signal handling and delivery; a solution is being investigated.

[]()
* `elm-ME+2.5.alpha60-1.tar.Z` requires the `elm` binary to be setgid `mail` for proper operation:
  * As `root`: `chown root:mail /opt/freeware/bin/elm && chmod g+s /opt/freeware/bin/elm`

[]()
* `coreutils-9.1-1.tar.Z` provides GNU `sort`, which has a bug affecting `configure` scripts (https://github.com/johnsonjh/AIX5-IA64/issues/6).  As a workaround:
  * Rename `sort` to `gsort`.  As root: `mv /opt/freeware/bin/sort /opt/freeware/bin/gsort`

[]()
* `libressl-3.6.0-2.tar.Z` has the following two (`2`) known issues:
  1. The `vsyslog_r` function is missing. An empty stub may be used as a workaround:
     * `void vsyslog_r(int pri, struct syslog_data *data, const char *fmt, va_list ap) {}`  
  2. LibreSSL headers normally include `stdint.h`, which is not available on stock AIX/IA64.  As a workaround:
     * Include `unistd.h` and/or `sys/types.h` in your application ahead of the LibreSSL headers.   

[]()
* And last, but not least, ***our compiler has many bugs.***
  * There is no currently **no** support for languages other than **C**.
  * All compiled executables **must** be comprehensively tested to ensure proper function.
    * Successful compilation does **not** imply a working binary.
    * We **strongly** recommended that only programs with high-coverage test suites be used.
