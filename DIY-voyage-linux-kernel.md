# The simplest and fastest way to update Voyage kernels seems to be to:

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

