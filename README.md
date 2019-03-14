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

[1]: https://coral.withgoogle.com/tutorials/devboard/
