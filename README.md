Community (read: "unofficial") gathering point for Google Coral dev board and dongle knowledge.

# Start Here

| What's up?    | Where to go |
|:--------------|:------------|
| Got a Coral dev board. What do I do? | There's an official [Getting Started Guide][1]. |
| Can't get something working. | The [issue tracker][0] is where we talk through problems. |
| Found something wrong in Google's docs. | Stick it in the [issue tracker][0] as a public service. |

# Overview

Officially, Google Coral is two devices:
```
Dev Board
A single-board computer with a removable system-on-module (SOM) featuring the Edge TPU.
*    Supported OS: Mendel Linux (derivative of Debian)
*    Supported Framework: TensorFlow Lite
*    Languages: Python (C++ coming soon)
```
and
```
USB Accelerator
A USB accessory featuring the Edge TPU that brings ML inferencing to existing systems.
*    Supported OS: Debian Linux
*    Compatible with Raspberry Pi boards
*    Supported Framework: TensorFlow Lite
```
Source: https://coral.withgoogle.com/

These devices are brand-spanking new, and as of right now there is very little supporting documentation. This repo exists to help interested devs avoid the twisted hellscape that is Google Product Forums.

# Dev Board
## OS notes
* Given the observations below (primarily the `libc` version) the OS appears to be a stock version of Debian 9.0 (Stretch). This knowledge allows us to access Debian sources without much concern.

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Mendel
Description:	Mendel GNU/Linux Beaker
Release:	mendel-beaker
```

```bash
$ cat /etc/debian_version
buster/sid
```

```bash
$ cat /etc/apt/sources/list
deb [arch=arm64] https://packages.cloud.google.com/apt mendel-bsp-enterprise-beaker main
deb-src https://packages.cloud.google.com/apt mendel-bsp-enterprise-beaker main
deb [arch=arm64] https://packages.cloud.google.com/apt mendel-beaker main
deb-src https://packages.cloud.google.com/apt mendel-beaker main
```

```bash
$ /lib/aarch64-linux-gnu/libc.so.6 
GNU C Library (Debian GLIBC 2.24-11+deb9u4) stable release version 2.24, by Roland McGrath et al.
Copyright (C) 2016 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.
Compiled by GNU CC version 6.3.0 20170516.
Available extensions:
	crypt add-on version 2.1 by Michael Glad and others
	GNU Libidn by Simon Josefsson
	Native POSIX Threads Library by Ulrich Drepper et al
	BIND-8.2.3-T5B
libc ABIs: UNIQUE
For bug reporting instructions, please see:
<http://www.debian.org/Bugs/>.
```

```bash
$ gcc --version
gcc (Debian 6.3.0-18+deb9u1) 6.3.0 20170516
Copyright (C) 2016 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

```bash
$ cat /proc/version 
Linux version 4.9.51-imx (pbuilder@49cfd4856c32) (gcc version 6.3.0 20170516 (Debian 6.3.0-18) ) #1 SMP PREEMPT Thu Jan 31 01:58:26 UTC 2019
```

[0]: https://github.com/f0cal/google-coral/issues
[1]: https://coral.withgoogle.com/tutorials/devboard/
[2]: https://github.com/saltstack/salt-bootstrap
[3]: https://www.hiroom2.com/2017/02/21/linux-change-reference-file-of-lsb-release-from-etc-lsb-release/
