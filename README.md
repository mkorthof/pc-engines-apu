
# Voyage Linux (updated kernels)

* Updated kernels for Voyage Linux (http://linux.voyage.hk)

The main reason to do this was to get a patched kernel for CVE-2016-5195 (Dirty COW).
I do not run Voyage Linux myself on my APU.1D4 but standard Debian instead.
I do however use the Voyage kernel and modules since was the easiest and quickest way to get get WLAN working (ATH9).

* The simplest and fastest way to update Voyage kernels seems to be to:

 - setup a VM with Debian (e.g. VirtualBox)
 - download the voyage kernel source: http://www.voyage.hk/dists/0.x/linux/ (e.g. http://www.voyage.hk/dists/0.10/linux/linux-source-3.16.7-ckt9-voyage_16.0-1_all.deb)
 - get patches from https://anonscm.debian.org/cgit/kernel/linux.git/ (e.g. git clone -b jessie-security --single-branch https://anonscm.debian.org/git/kernel/linux.git),
   or alternatively, get the patches from debian kernel source package
 - apply patch(es), e.g. patch -p1 < linux/debian/patches/bugfix/all/mm-remove-gup_flags-FOLL_WRITE-games-from-__get_user.patch
 - compile: make-kpkg clean && time fakeroot make-kpkg --initrd --revision="16.0-2" --append-to-version="-voyage" kernel_image

* More info / links:

http://wiki.voyage.hk/voyage_kernel.txt
http://linux.voyage.hk/developA
https://anonscm.debian.org/cgit/kernel/linux.git/log/?h=jessie-security
https://kernel-handbook.alioth.debian.org/ch-common-tasks.html#s-common-building
https://www.debian.org/releases/jessie/i386/ch08s06.html.en
https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs

