# google-coral
Community gathering point for Google Coral dev board and dongle help.

# Journal
## 2019-02-14
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

# Dev Board
## OS notes
* Given the observations, below, the OS appears to be based on Debian Stretch.

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Mendel
Description:	Mendel GNU/Linux Beaker
Release:	mendel-beaker
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

[1]: https://coral.withgoogle.com/tutorials/devboard/
