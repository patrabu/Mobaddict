Title: Wake a lan server with NetworkManager
Slug: wakeonlan-with-networkmanager
Lang: en
Date: 2015-02-23 11:05:20
Author: Patrick
Category: Informatique
Tags: Linux

## References 

[Launch scripts based on network location](http://sysadminsjourney.com/content/2008/12/18/use-networkmanager-launch-scripts-based-network-location/)

[Arch documentation on NetworkManager](https://wiki.archlinux.org/index.php/NetworkManager)


## Goal

I want to start my NAS when my desktops are started so I could do some backup and use the global storage (Music, Videos, Pictures...).


## How-to

The NAS is ready to accept WOL signal (in the BIOS settings). 

To accomplish this task, I am using _etherwake_ on each of my linux boxes. 

    :::console
    etherwake -i interface mac_address


This command is encapsulated in a script shell called by the NetworkManager dispatcher. The location is :

    :::console
    /etc/NetworkManager/dispatcher.d/


The script receive two parameters, the interface (_eth0_ for example) and the status of this interface (_up_ or _down_).

The command _/sbin/ip addr show $IF to $NETMASK_ return the settings of the network interface _$IF_ if it is within the subnet described with _$NETMASK_.

Here is the script that I use : 

    ::: bash
    #!/bin/sh

    # Send a magic packet when an interface comes up, to allow it to start
    # the remote server with the MAC address.

    set -e

    PATH=/sbin:/bin:/usr/sbin

    IF=$1
    STATUS=$2
    MAC=00:11:22:33:44:55

    if [ "$IF" = "eth0" ] && [ "$STATUS" = "up" ]; then
      #LAN Subnet at home
      NETMASK="10.0.0.0/8"
      if [ -n "`/sbin/ip addr show $IF to $NETMASK`" ]; then
        if [ -e /usr/sbin/etherwake ]; then
          /usr/sbin/etherwake -i $IF $MAC
          exit $?
        fi
      fi
    fi

