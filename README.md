Community (read: "unofficial") gathering point for Google Coral dev board and dongle knowledge.

# Start Here

| What's up?    | Where to go |
|:--------------|:------------|
| Got a Coral dev board. What do I do? | There's an official [Getting Started Guide][1]. |
| Can't get something working. | The [issue][0] tracker is where we talk through problems. |

# Overview

Officially, Google Coral is both:
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

# Journal
## 2019-03-14
* DevBoard showed up today; wanted to start playing with it immediately.
* Followed the [Getting Started Guide][1] verbatim but hit some immediate roadblocks.

```bash
$ dmesg | grep ttyUSB
[11567.500409] usb 1-2: cp210x converter now attached to ttyUSB0
[11567.503236] usb 1-2: cp210x converter now attached to ttyUSB1
[11569.062428] cp210x ttyUSB1: failed set req 0x1e size 4 status: -32
[11569.062443] cp210x ttyUSB1: failed to set baud rate to 300
```

* Unclear that the above `failed`s were actually a problem but then....
```bash
$ screen /dev/ttyUSB0 115200
[screen is terminating]
```
* The Guide tells you to unplug, replug, and retry but this didn't resolve anything for me.
* I think at the end of the day this was due to `udev` persmissions because ```sudo screen /dev/ttyUSB0 115200``` got it working immediately.
* The rest went fairly smoothly.
* Note that if you update the hostname in `/etc/hostname` you should do the same in `/etc/hosts` otherwise you'll see a bunch of complaints about `sudo` not being able to resolve the hostname.
* The first thing I tried to do that was "out of bounds" was to install a `salt-minion` via the standard [bootstrap script][2].
* I quickly hit a problem there in that `lsb_release` reports "Mendel" even though the board is basically running Debian Stretch. More on that below in the "OS Notes" section.
* I forced `lsb_release` to report what I wanted it to by using [this hack][3].
* Then I added the Debian Stetch sources (see next) so the bootstrap could install what it needed to.

```bash
$ cat /etc/apt/sources.list.d/debian-stretch.list
deb http://deb.debian.org/debian/ stable main contrib non-free
deb-src http://deb.debian.org/debian/ stable main contrib non-free

deb http://deb.debian.org/debian/ stable-updates main contrib non-free
deb-src http://deb.debian.org/debian/ stable-updates main contrib non-free

deb http://deb.debian.org/debian-security stable/updates main
deb-src http://deb.debian.org/debian-security stable/updates main

deb http://ftp.debian.org/debian stretch-backports main
deb-src http://ftp.debian.org/debian stretch-backports main
```
* `salt-minion` services installed and started successfully after that.

# Dev Board
## OS notes
* Given the observations below (primarily the `libc` version) the OS appears to be an upgraded-to-testing version of Debian 9.0 (Stretch). This knowledge allows us to access Debian sources without much concern.

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
