# Log
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

## 2019-03-15

* Decided to install OpenCV 4.0.1 from source and dealt with some challenges.
* The dev board has <1G of RAM for compilng stuff, so this was a real issue.
* Added a swapfile to avoid "Out of memory" errors.
```bash
sudo apt-get install dphys-swapfile
```
* Gives us a 2GB swap file by default.
* Immediately ran out of disk space.
