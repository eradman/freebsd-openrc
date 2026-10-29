FreeBSD + OpenRC
================

Prerequisites
-------------

    pkg install -y meson ninja pkgconf
    pkg install -y dhcpcd

Installation
------------

Fetch sources

    fetch https://github.com/OpenRC/openrc/archive/refs/tags/0.64.1.tar.gz -o openrc-0.64.1.tar.gz
    fetch https://github.com/eradman/freebsd-openrc/archive/refs/heads/master.tar.gz -o freebsd-openrc.tar.gz
    tar -zxf openrc-0.64.1.tar.gz
    tar -zxf freebsd-openrc.tar.gz

Build/install OpenRC

    cd ~/openrc-0.64.1
    meson setup builddir
    cd builddir
    meson compile
    meson install --destdir /

Install base services

    cd ~/freebsd-openrc-master
    for svc in dhcpcd ntpd sshd zfs; do
      install -m 755 init.d/$svc /etc/init.d/
      rc-update add $svc default
    done

Final steps

    vi /etc/conf.d/hostname
    sed -i -E 's/slaac private/#slaac private/g' /usr/local/etc/dhcpcd.conf
    reboot
