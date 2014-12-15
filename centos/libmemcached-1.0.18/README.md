# libMemcached 1.0.18 for CentOS / RHEL 6
This package contains the RPM packages needed to install libmemcached 1.0.18
installed on CloudLinux / CentOS / RHEL systems.

## What This Package Does
This is an RPM distribution of
[libmemcached 1.0.18](https://launchpad.net/libmemcached/1.0/1.0.18), built
using [the Atomic Repo's SRPM for libmemcached 1.0.16](http://www4.atomicorp.com/channels/source/libmemcached/).

## Background
As of the date of this writing, Atomic only offers 1.0.16, which has a nasty
[double-free / memory leak issue](https://bugs.launchpad.net/bugs/1126601) that
can cause spurious segmentation faults on sites using Memcache or Couchbase,
with or without SASL.

## Disclaimer
These RPMs have been packaged and tested only with Plesk 12 and CloudLinux
Server release 6.6 (Leonid Kizim). It may work with other versions but it has
not been tested with them.

## Installation of libMemcached 1.0.18
### Binary RPM Method
We've already rolled 64-bit RPMs for libMemcached 1.0.18.

You can find our packages under the RPMS folder in the folder with this README.

To install the replacement RPMs, follow these steps:

1. Copy the RedBottle libmemcached RPMs into a folder on your server.

2. As `root` or a user with `sudo` access, open a terminal and change into the
   folder you created in step #1.

3. Run the following command to have Yum replace the libmemcached packages you
   already have installed on your system with the corresponding updated
   packages:

        rpm -qa | grep -e "^libmemcached" | grep 1.0.16 | \
          sed 's/-1.0.16-1.el6.art.x86_64/-1.0.18-1.el6.redbottle.x86_64.rpm/' | \
          sudo xargs yum install

4. Yum should report no errors and ask for your confirmation.
   - If the list of packages to be installed is empty, you don't have
     libmemcached 1.0.16 installed, but might have an older version installed
     instead. You can install libmemcached 1.0.18 now using our RPMs just by
     running `yum install libmemcached-1.0.18-1.el6.redbottle.x86_64.rpm`.
     
   - If Yum reports any dependency errors, follow its instructions to determine
     what to do. (Since dependency errors are often system-dependant, we can't
     really provide appropriate instructions here.)
     
   - If the list of packages looks correct, confirm it to proceed with
     installing the updated packages.

5. Restart any services (like Apache, Nginx, or PHP-FPM) that are using
   libmemcache.
  
Congratulations! You should now have libmemcached 1.0.18, replete with memory
safety.
    
### Source RPM (aka "Roll Your Own RPM") Method
This option is more hands-on but better if you want more control over what gets
compiled in to the RPMS, or in case you want to roll a different version of
libmemcached.

For reference, we've included a spec file under the "SPECS" folder that is
in the same folder as this README. You may want to reference it while you
complete this section.

Here are the steps to build your own RPMs from ART sources:

1.  From a command-line logged-in as a user who has sudo access
    (**NOT `root`**), install the development packages needed to compile
    libmemcached:
   
        sudo yum install python-sphinx systemtap-sdt-devel libevent-devel

2.  Find out the URL to the source RPM (SRPM) for your version of libmemcached
    from the Atomic Repo. For example:
   
        http://www4.atomicorp.com/channels/source/libmemcached/libmemcached-1.0.16-1.art.src.rpm
     
3.  Pass the URL from step #2 to `rpm -ivh` on the terminal. For example:

        rpm -ivh http://www4.atomicorp.com/channels/source/libmemcached/libmemcached-1.0.16-1.art.src.rpm

4.  You should find an `rpmbuild` folder in the home folder for the user you are
    logged-in as. Change into the `SOURCES` folder of that folder:

        cd ~/rpmbuild/SOURCES
   
5.  Download the desired version of libmemcached using wget. For example:

        wget http://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz

6.  (Optional) Download the "ex-Hsieh hash" patcher to remove the Hsieh hash
    function from the libmemcached distribution, since it's under a non-free
    license:
    
        wget https://raw.githubusercontent.com/remicollet/remirepo/master/libmemcached-last/strip-hsieh.sh

7.  (Optional) Make the shell script you downloaded in step #6 executable:

        chmod u+x ./strip-hsieh.sh

8.  (Optional) Run the script you make executable in step #7.

        ./strip-hsieh.sh

9.  (Optional) The script in step #8 will produce a file called
    "libmemcached-%{version}-exhsieh.tar.gz" in the current folder (where
    "%{version}" is the version number, like "1.0.18"). Copy this file into
    your `~/rpmbuild/SOURCES` folder:

        cp ./libmemcached-1.0.18-exhsieh.tar.gz ~/rpmbuild/SOURCES

10. If you **did not** complete steps #6-9, copy the file you downloaded in
    step #5 into your `~/rpmbuild/SOURCES` folder, and then modify the
    `Source0` line of `libmemcached-art.spec` to match its filename (remove the
    `-exhsieh` portion from the filename).

11. Change into the "SPECS" folder of the `rpmbuild` folder:

        cd ~/rpmbuild/SPECS
   
12. Open the `libmemcached-art.spec` file for editing:

        nano libmemcached-art.spec

13. Find the `Version` line in the file and modify it to match the version
    you're using. For example, for libmemcached 1.0.18, it's
    `Version:   1.0.18`.
   
14. Find the `Release:` line that applies to the release of the
    package version you're using (for example, `1%{?dist}.art`) and then
    modify it as appropriate for your organization's conventions (for example,
    we change it to be `Release:   1%{?dist}.redbottle`).
    
    For a smooth installation, the version number or release number must be
    higher than the version you have installed. If you're packaging 1.0.18
    and you have 1.0.16 or 1.0.17 installed, you'll be fine.

15. (Optional) Find the `%changelog` line and add an appropriate note along
    with your name and e-mail address to indicate your version includes a
    new version of libmemcached. This will aid with maintenance down the road.
    
16. Change into the rpmbuild folder:

        cd ~/rpmbuild
        
17. Build the RPMS:

        rpmbuild -ba SPECS/libmemcached-art.spec
        
18. Once the build completes, with luck you should find your complete RPMS in
    the ~/rpmbuild/RPMS folder. Change into the RPMs folder:
    
        cd ~/rpmbuild/RPMS
        
19. Pick-up at step #2 in the "Binary RPM Method" section above to install
    your new packages. For step #3, be sure to correct the second part of the
    pattern passed to the `sed` command to match the release pattern you
    specified in step #14.
