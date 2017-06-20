
# Voyage Linux Kernels
Updated kernels for Voyage Linux (http://linux.voyage.hk)

The main reason to do this was to get a patched kernel for CVE-2016-5195 (aka Dirty COW: https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs).<br>
I do not run Voyage Linux myself but use standard Debian instead - on a PC Engines APU.1D4.<br>
I do however use the Voyage kernel and modules since that was the easiest and quickest way to get get WLAN working (ath9).<BR>

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
* applied Dirty COW patch CVE-2016-5195 ( https://anonscm.debian.org/cgit/kernel/linux.git/commit/?h=jessie-security&id=46f7cac7d0e62a88925ed4bb442c9f33e8aae427 )

And that's it, *NOTHING* else was changed.
<br>

#### Tests:

1. cowroot.c: not vulnerable (https://gist.github.com/rverton/e9d4ff65d703a9084e85fa9df083c679)<br>
   this is the same result as 3.16.36-1+deb8u2
2. WLAN still works;)
<br><br>

#### Rolling it yourself:

The simplest and fastest way to update Voyage kernels seems to be to:

1. Setup a VM with Debian (e.g. VirtualBox)
2. Install the voyage kernel source (or download from http://www.voyage.hk/dists/0.x/linux/, e.g. /0.10/linux/linux-source-3.     16.7-ckt9-voyage_16.0-1_all.deb)
3. Copy config from linux-image (/boot or same url)
4. Get patch(es) from https://anonscm.debian.org/cgit/kernel/linux.git/<br>
   (e.g. `git clone -b jessie-security --single-branch https://anonscm.debian.org/git/kernel/linux.git`),<br>
   or alternatively from debian kernel source package
5. Apply patches: `patch -p1 < /path/to/linux/debian/patches/bugfix/all/mm-remove-gup_flags-FOLL_WRITE-games-from-__get_user.patch`
6. Compile: `make-kpkg clean && time fakeroot make-kpkg --initrd --revision="16.0-2" --append-to-version="-voyage" kernel_image`
<br><br>

* More info / links:
  * http://wiki.voyage.hk/voyage_kernel.txt
  * http://linux.voyage.hk/develop
  * https://anonscm.debian.org/cgit/kernel/linux.git/log/?h=jessie-security
  * https://kernel-handbook.alioth.debian.org/ch-common-tasks.html#s-common-building
  * https://www.debian.org/releases/jessie/i386/ch08s06.html.en

#### Serial Console:

Might be a good idea set this up first, before messing with kernel.

In /etc/default/grub change:
<pre>
GRUB_CMDLINE_LINUX="video=off elevator=deadline console=tty0 console=ttyS0,115200"
GRUB_TERMINAL=serial
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=115200 --stop=1"
</pre>
In /etc/inittab add at the bottom:<br>
`0:2345:respawn:/sbin/agetty -8 ttyS0 115200 vt100`
