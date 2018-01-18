
# Voyage Linux Kernels
Updated kernels for Voyage Linux (http://linux.voyage.hk)

## Debian 9 stretch update

"Plain" Debian 9 (dist upgrade) seems to run just fine on the PC Engines APU. WLAN works (WLE200NX/Qualcomm Atheros AR5008 using included ath9k driver). The Vanilla kernel 4.9.0 is ofcourse missing stuff like LED driver module (/sys/class/leds). ~~I'm currently testing DKMS to get the driver installed now and in future kernels.~~ I've managed to built the LED kernel module using [DKMS](https://github.com/dell/dkms). Unless there are huge ABI changes between kernel versions these should keep working for now.

### PC Engines APU LEDs

Module:       leds_apu, leds-apu.ko  
Description:  PCEngines apu LED driver  
Author:       Christian Herzog  

#### leds-apu-dkms_0.1_amd64.deb:

Installs leds-apu module and adds it to /etc/modules so it gets loads at boot time. It also sets these triggers for disk and cpu activity in /etc/rc.local. 
```
echo disk-activity > /sys/class/leds/apu\:2/trigger
echo cpu0 > /sys/class/leds/apu\:3/trigger
```
You can change them to whatever you like bu editting /etc/rc.local. 

DKMS will try to recompile the module if the kernel version changes. If you don't know what to download use you probably want this package.
To install:
<pre>
$ wget https://github.com/mkorthof/voyage-linux/raw/master/leds-apu-dkms_0.1_amd64.deb && sha512sum leds-apu-dkms_0.1_amd64.deb | \
grep aec8d1cd0b7967ac9146fe11811c6ca0a67692e94a61f395901504e88c311b2cec0cd8f3d52432a01c6da35207944d0df1dca35a0e8c1eb26bc65b223739cd14 && \
sudo dpkg -i leds-apu-dkms_0.1_amd64.deb
</pre>
<sub>(copy/paste as one line)</sub>
<br><br>

#### leds-apu-modules-4.9.0-5-amd64_0.1_amd64.deb (bmdeb):

Installs just the binary module into /lib/modules on 4.9.0-5 kernels, nothing else. This package has no dependencies.
After installing it (`dpkg -i`) you have to manually load the module (`modprobe leds-apu`) and set trigger(s).

#### leds-apu-dkms_0.1{.dsc,_source.changes,.tar.gz} (source):

Tarball leds-apu-dkms_0.1.tar.gz contains module src, dkms.conf and the post install/remove scripts that modify /etc/modules and /etc/rc.local.

### Triggers

After the module is loaded /sys/class/leds/apu:[1-3] will be available (apu1 is the powerled).
Examples:
```
echo 1 > /sys/class/leds/apu\:2/brightness
echo disk-activity > /sys/class/leds/apu\:2/trigger`
````
List all options:
`cat /sys/class/leds/apu\:1/trigger`


## Debian 8 jessie / voyage-linux-0.10.0

The main reason to do this was to get a patched kernel for CVE-2016-5195 (aka Dirty COW: https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs).<br>
I do not run Voyage Linux myself but use standard Debian instead - on a PC Engines APU.1D4.<br>
I do however use the Voyage kernel and modules since that was the easiest and quickest way to get WLAN working (ath9).<BR>

#### In case the steps below are too much trouble for you (and you trust me... ;) just execute the oneliner below to install the updated kernel package.

Kernel 3.16.7-ckt9-voyage_16.0-2 (linux-image-3.16.7-ckt9-voyage_16.0-2_amd64.deb):
<pre>
$ wget https://github.com/mkorthof/voyage-linux/raw/master/linux-image-3.16.7-ckt9-voyage_16.0-2_amd64.deb && sha512sum linux-image-3.16.7-ckt9-voyage_16.0-2_amd64.deb | \
grep 48dc119f47814ad53245b9d76fac8f38d8c7e26142d72f533a1773e0b019a507131230519af9873b06090100a92443ec44a4114b0d6578333ec9daa9f19d9b52 && \
sudo bash -c "mv /lib/modules/3.16.7-ckt9-voyage /lib/modules/3.16.7-ckt9-voyage.bak && dpkg -i linux-image-3.16.7-ckt9-voyage_16.0-2_amd64.deb"
</pre>
<sub>(copy/paste as one line)</sub>
<br><br>

#### Changelog 3.16.7-ckt9-voyage:

* changed version to voyage_16.0-**2**
* applied Dirty COW patch CVE-2016-5195 ([https://anonscm.debian.org/cgit/kernel/linux.git](https://anonscm.debian.org/cgit/kernel/linux.git/commit/?h=jessie-security&id=46f7cac7d0e62a88925ed4bb442c9f33e8aae427))

And that's it, *NOTHING* else was changed.
<br>

#### Tests:

1. cowroot.c: not vulnerable ([https://gist.github.com/rverton](https://gist.github.com/rverton/e9d4ff65d703a9084e85fa9df083c679)), this is the same result as 3.16.36-1+deb8u2
2. WLAN still works;)
<br><br>
=======
#### Changelog 3.16.7-ckt9-voyage:

* changed version to voyage_16.0-**2**
* applied Dirty COW patch CVE-2016-5195 ([https://anonscm.debian.org/cgit/kernel/linux.git](https://anonscm.debian.org/cgit/kernel/linux.git/commit/?h=jessie-security&id=46f7cac7d0e62a88925ed4bb442c9f33e8aae427))

And that's it, *NOTHING* else was changed.
<br>

#### Tests:

1. cowroot.c: not vulnerable ([https://gist.github.com/rverton](https://gist.github.com/rverton/e9d4ff65d703a9084e85fa9df083c679)), this is the same result as 3.16.36-1+deb8u2
2. WLAN still works;)
<br><br>

#### Rolling it yourself:

The simplest and fastest way to update Voyage kernels seems to be to:

1. Setup a VM with Debian (e.g. VirtualBox)
2. Install the voyage kernel source<br>
   Or download from http://www.voyage.hk/dists/0.x/linux/<br>
   ( e.g. [/dists/0.10/linux/linux-source-3.16.7-ckt9-voyage_16.0-1_all.deb](http://www.voyage.hk/dists/0.10/linux/linux-source-3.16.7-ckt9-voyage_16.0-1_all.deb) )
3. Copy config from linux-image (/boot or get it from linux.voyage.hk)
4. Get patch(es) from https://anonscm.debian.org/cgit/kernel/linux.git/<br>
   e.g. `git clone -b jessie-security --single-branch https://anonscm.debian.org/git/kernel/linux.git`<br>
   or alternatively use debian kernel source package
5. Apply patches: `patch -p1 < /path/to/linux/debian/patches/bugfix/all/mm-remove-gup_flags-FOLL_WRITE-games-from-__get_user.patch`
6. Compile: `make-kpkg clean && time fakeroot make-kpkg --initrd --revision="16.0-2" --append-to-version="-voyage" kernel_image`

#### Rolling it yourself:

The simplest and fastest way to update Voyage kernels seems to be to:

1. Setup a VM with Debian (e.g. VirtualBox)
2. Install the voyage kernel source<br>
   Or download from http://www.voyage.hk/dists/0.x/linux/<br>
   ( e.g. [/dists/0.10/linux/linux-source-3.16.7-ckt9-voyage_16.0-1_all.deb](http://www.voyage.hk/dists/0.10/linux/linux-source-3.16.7-ckt9-voyage_16.0-1_all.deb) )
3. Copy config from linux-image (/boot or get it from linux.voyage.hk)
4. Get patch(es) from https://anonscm.debian.org/cgit/kernel/linux.git/<br>
   e.g. `git clone -b jessie-security --single-branch https://anonscm.debian.org/git/kernel/linux.git`<br>
   or alternatively use debian kernel source package
5. Apply patches: `patch -p1 < /path/to/linux/debian/patches/bugfix/all/mm-remove-gup_flags-FOLL_WRITE-games-from-__get_user.patch`
6. Compile: `make-kpkg clean && time fakeroot make-kpkg --initrd --revision="16.0-2" --append-to-version="-voyage" kernel_image`

* More info / links:
  * http://wiki.voyage.hk/voyage_kernel.txt
  * http://linux.voyage.hk/develop
  * https://anonscm.debian.org/cgit/kernel/linux.git/log/?h=jessie-security
  * https://kernel-handbook.alioth.debian.org/ch-common-tasks.html#s-common-building
  * https://www.debian.org/releases/jessie/i386/ch08s06.html.en

## Serial Console:

Might be a good idea set this up first, before messing with kernel.

In /etc/default/grub change:
<pre>
GRUB_CMDLINE_LINUX="video=off elevator=deadline console=tty0 console=ttyS0,115200"
GRUB_TERMINAL=serial
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=115200 --stop=1"
</pre>
In /etc/inittab add at the bottom:<br>
`0:2345:respawn:/sbin/agetty -8 ttyS0 115200 vt100`

