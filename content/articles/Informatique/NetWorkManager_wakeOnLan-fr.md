Title: Réveiller un serveur avec NetworkManager
Slug: wakeonlan-with-networkmanager
Lang: fr
Date: 2015-02-23 11:05:20
Author: Patrick
Category: Informatique
Tags: Linux


## Références 

[Lancement de scripts en fonction du réseau](http://sysadminsjourney.com/content/2008/12/18/use-networkmanager-launch-scripts-based-network-location/)

[Documentation de Arch Linux sur NetworkManager](https://wiki.archlinux.org/index.php/NetworkManager)


## Bût

Je veux démarrer mon NAS quand un des ordinateurs est démarré. 
De cette façon, les sauvegardes peuvent être effectuées et on peut accéder aux données partagées (musique, photos, vidéos...)


## Mise en œuvre

Le BIOS du NAS est configuré de façon à accepter le signal WOL (Wake On Lan).

Pour les ordinateurs sous Linux, j'utilise le programme _etherwake_ pour envoyer le signal WOL sur une interface réseau à destination de l'adresse MAC du NAS. 

    :::console
    etherwake -i interface mac_address

Cette commande est intégrée dans un script shell déposé dans le répertoire dispatcher.d de NetworkManager : 

    :::console
    /etc/NetworkManager/dispatcher.d/

Lorsqu'il est appelé par le dispacher de NetworManager, ce script reçoit deux paramètres : l'interface réseau (_eth0_ par exemple) et l'état de cette interface (_up_ or _down_).

En utilisant la commande _/sbin/ip addr show $IF to $NETMASK_ qui renvoie les paramètres de l'interface réseau _$IF_ si cette dernière est déclarée dans le réseau décrit par _$NETMASK_.

Voici le script utilisé : 

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

