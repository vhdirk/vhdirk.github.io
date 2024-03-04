+++
title = "Installing TexLive 2011 on Ubuntu 11.10"
date = "2012-02-09"
+++

The vanilla TexLive comes with a package management tool called _tlmgr_ which is
particularly useful when you want to install additional packages. The TexLive
version packaged in ubuntu does not have this tool since it would conflict with
apt.

Anyway, installing Tex packages the ubuntu way is a bit [annoying](https://help.ubuntu.com/community/LaTeX#manualpkginstall), so my preferred solution is to install the vanilla version, and a fake package that replaces the ubuntu packages. It might even contain some awesome new features (or bugs); ubuntu's version is already 3 years old.

So, here it goes:

- Ensure that the only Debian TeX Live packages installed are tex-common, texinfo, and perhaps lmodern
- Install vanilla TeX Live as root, system-wide <http://www.tug.org/texlive/quickinstall.html>
  During install, enter L to enable symlinks and hit 3 times "Enter" to keep default settings.
  note: you can also add the binary path of the installation to `/etc/environment`
- Tell APT about your TeX Live installation by building a dummy package using equivs:

    ```sh
    sudo aptitude install equivs
    mkdir /tmp/tl-equivs && cd /tmp/tl-equivs
    equivs-control texlive-local

    # edit texlive-local, see [example](http://pastebin.com/0wmQg9nB)

    equivs-build texlive-local
    sudo dpkg -i texlive-local_*.deb
    ```

- Voila, all done.

thanks to

- <http://superuser.com/questions/134125/vanilla-tex-live-2009-on-ubuntu>
- <http://www.tug.org/texlive/debian.html>
