
# Voyage Linux (updated kernels)

* Updated kernels for Voyage Linux (http://linux.voyage.hk)

In case the steps below are too much trouble for you (and you trust me... ;) just execute the following oneliner to install the updated kernel:

`wget https://x && sha512sum x | grep x && dpkg -i x`

* Changelog:

 - changed version to voyage_16.0-**2**
 - applied Dirty COW patch (CVE-2016-5195), https://anonscm.debian.org/cgit/kernel/linux.git/commit/?h=jessie-security&id=46f7cac7d0e62a88925ed4bb442c9f33e8aae427

And that's it, *NOTHING* else was changed.

The main reason to do this was to get a patched kernel for CVE-2016-5195 (Dirty COW).<br>
I do not run Voyage Linux myself on my APU.1D4 but use standard Debian instead.<br>
I do however use the Voyage kernel and modules since was the easiest and quickest way to get get WLAN working (9).<BR>

* The simplest and fastest way to update Voyage kernels seems to be to:

 - setup a VM with Debian (e.g. VirtualBox)
 - install the voyage kernel source (or download from http://www.voyage.hk/dists/0.x/linux/, e.g. /0.10/linux/linux-source-3.16.7-ckt9-voyage_16.0-1_all.deb)
 - and config from linux-image (/boot or same url)
 - get patches from https://anonscm.debian.org/cgit/kernel/linux.git/<br>
   (e.g. `git clone -b jessie-security --single-branch https://anonscm.debian.org/git/kernel/linux.git`),<br>
   or alternatively from debian kernel source package

### apply patch(es):<br>
`patch -p1 < /path/to/linux/debian/patches/bugfix/all/mm-remove-gup_flags-FOLL_WRITE-games-from-__get_user.patch`

### compile:<br>
`make-kpkg clean && time fakeroot make-kpkg --initrd --revision="16.0-2" --append-to-version="-voyage" kernel_image`

* Tests

1. cowroot.c: not vulnerable (https://gist.github.com/rverton/e9d4ff65d703a9084e85fa9df083c679)<br>
   same result as 3.16.36-1+deb8u2
2. WLAN still works;)

* More info / links:

http://wiki.voyage.hk/voyage_kernel.txt<br>
http://linux.voyage.hk/develop<br>
https://anonscm.debian.org/cgit/kernel/linux.git/log/?h=jessie-security<br>
https://kernel-handbook.alioth.debian.org/ch-common-tasks.html#s-common-building<br>
https://www.debian.org/releases/jessie/i386/ch08s06.html.en<br>
https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs<br>

