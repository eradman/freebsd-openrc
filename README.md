FreeBSD + OpenRC
================

Daemons from base:

* ntpd
* sshd
* wlan

System mangement from base:

* zfs

Daemons from ports:

* cupsd
* dbus
* dhcpcd

Installation
------------

Install build tools

    pkg install -y meson ninja

Fetch sources

    fetch https://github.com/OpenRC/openrc/archive/refs/tags/0.64.1.tar.gz -o openrc-0.64.1.tar.gz
    fetch https://github.com/eradman/freebsd-openrc/archive/refs/heads/master.tar.gz -o freebsd-openrc.tar.gz
    tar -zxf openrc-0.64.1.tar.gz
    tar -zxf freebsd-openrc.tar.gz

Build/install OpenRC

    cd ~/openrc-0.64.1
    meson setup build
    cd build
    meson compile
    meson install --destdir /

Install FreeBSD services

    cd ~/freebsd-openrc-master
    for svc in `ls init.d`; do
      install -v -m 755 init.d/$svc /etc/init.d/
    done

    rc-update add zfs default
    rc-update add sshd default

    vi /etc/conf.d/hostname
    reboot

Networking
----------

Optionally, install dhcpcd to facilitate automatic IPv4 and IPv6 network configuration

    pkg install -y dhcpcd

Add networking services

    rc-update add dhcpcd default
    rc-update add ntpd default
    openrc default

Adjustments
-----------

To reduce chatter on the console edit `/etc/syslog.conf` and remove `*.err,`

    kern.warning;auth.notice;mail.crit    /dev/console
