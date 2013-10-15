---
layout: post
category : tutoriel
tagline: ""
tags : [linux, wifi, point d'accès, hotspot]
---
{% include JB/setup %}

Cet article traite de la mise en place d'un point d'accès wifi sous linux. Il s'agit d'un réseau avec un serveur (notre ordinateur) qui va accepter différentes connexions de clients en particulier celle des téléphones android qui ne supportent pas les réseaux de type ad-hoc. 

On mettra aussi en place un filtrage sur l'adresse mac des clients. L'idée est un peu de reproduire un réseau de type live-box ou similaire. 

Faire cette manipulation m'a pris un peu de temps et je juge utile de résumer les étapes à suivre pour la mise en place de ce réseau.

*****

Lors de mes recherches, j'ai trouvé un logiciel parfaitement adapté à ce que nous voulons faire : hostapd. Je me suis tourné vers google pour obtenir plus de détails :
- [Léa-linux](http://www.lea-linux.org/documentations/index.php/Cr%C3%A9er_un_point_d'acc%C3%A8s_s%C3%A9curis%C3%A9_avec_hostAPd) : un article assez complet que j'ai suivi dans un premier temps
- [La documentation d'ubuntu](http://doc.ubuntu-fr.org/hostapd) : plus complète que le lien précédent, nous allons reprendre les différentes étapes une à une.

La documentation d'ubuntu est très bien faite mais je me suis perdu dedans à un moment donc je résume le tout.

# Installation et configuration de hostapd

## Installation

Pour l'installation, on a de la chance car hostapd est dans les dépôts.

    sudo apt-get install hostapd
	
## Fichier de configuration

Pour la configuration, j'ai fait un mixe entre la documentation d'ubuntu et le blog cité précédemment, ce qui nous donne (/etc/hostapd/hostapd.conf) :

    # interface wlan du Wi-Fi
    interface=wlan0
     
    # nl80211 avec tous les drivers Linux mac80211
    driver=nl80211
     
    # Nom du spot Wi-Fi
    ssid=tx52
     
    # mode Wi-Fi (a = IEEE 802.11a, b = IEEE 802.11b, g = IEEE 802.11g)
    hw_mode=g
     
    # canal de fréquence Wi-Fi (1-14)
    channel=6
     
    # Wi-Fi : authentification
    auth_algs=1
     
    wpa=1
    wpa_passphrase=grelagaa
    wpa_key_mgmt=WPA-PSK
    wpa_pairwise=TKIP
     
     
    # Beacon interval in kus (1.024 ms)
    beacon_int=100
     
    # DTIM (delivery trafic information message)
    dtim_period=2
     
    # Maximum number of stations allowed in station table
    max_num_sta=255
     
    # RTS/CTS threshold; 2347 = disabled (default)
    rts_threshold=2347
     
    # Fragmentation threshold; 2346 = disabled (default)
    fragm_threshold=2346
     
    # Station MAC address -based authentication (driver=hostap or driver=nl80211)
    # 0 = accept unless in deny list
    # 1 = deny unless in accept list
    # 2 = use external RADIUS server (accept/deny lists are searched first)
    macaddr_acl=1
     
    # Accept/deny lists are read from separate files
    accept_mac_file=/etc/hostapd/hostapd.accept
    deny_mac_file=/etc/hostapd/hostapd.deny

## Filtrage des adresses mac

Toujours en suivant la doc d'ubuntu, on créé deux fichiers pour le filtrage des adresses mac :

    b4:24:63:73:f8:14
    00:07:a5:f9:a7:80
	
et */etc/hostapd/hostapd.deny* qui est vide car inutile pour l'instant, voir macaddr_acl dans le fichier de configuration.

Jusqu'ici la doc d'ubuntu nous a bien guidé, mais c'est par le suite que cela devient plus obscure.

# Mise en place d'un serveur DHCP

## Installation

Comme pour l'installation précédent, pas de difficulté :

    xavier@xavier-desktop:~% sudo apt-get install dhcp3-server
	
Configuration

On va regarde le contenu du fichier de conf (/etc/dhcp3/dhcpd.conf) par défaut et l'adapter à notre besoin, on ajoute donc :

    option domain-name-servers 192.168.0.1;
    subnet 192.168.0.0 netmask 255.255.255.0 {
    	option subnet-mask 255.255.255.0;
    	option broadcast-address 192.168.0.0;
    	range dynamic-bootp 192.168.0.15 192.168.0.100;
    }
	

Rien de bien compliqué à comprendre si on a quelques notions de réseau. Notons que notre machine serveur sera 192.168.0.1/24.

## Lancement du serveur

Il y a différentes étapes à suivre. Notons qu'un script sera donné plus tard et il fera tout tout seul (mais il faudra lire cet article jusqu'à la fin pour avoir le lien).

## Lancement de hostapd

    xavier@xavier-desktop:~% sudo hostapd /etc/hostapd/hostapd.conf
    Configuration file: /etc/hostapd/hostapd.conf
    Using interface wlan0 with hwaddr 00:26:5a:7f:65:a5 and ssid 'tx52'
    Malformed netlink message: len=432 left=256 plen=416
    256 extra bytes in the end of netlink message

C'est un bon début ! Je ne lance pas cela en arrière plan pour avoir les logs qui défilent sous les yeux et suivre ce qui se passe.

## Configuration de la carte réseau du serveur

    xavier@xavier-desktop:~% sudo ifconfig wlan0 192.168.0.1 netmask 255.255.255.0
	
## Lancement du serveur DHCP

    xavier@xavier-desktop:~% sudo dhcpd3 -d -f -pf /var/run/dhcp3-server/dhcpd.pid -cf /etc/dhcp3/dhcpd.conf wlan0
	
Pareillement qu'avant, je ne la lance pas en arrière plan pour avoir un retour immédiat comme nous sommes en train de tout configurer.

## Connexion des clients

Et maintenant ça rox, on peut connecter des appareils à notre ordinateur sans problème, y compris mon petit téléphone.

# Et après ?

On n'oublie pas de consulter régulièrement les logs pour vérifier que personne ne tente de s'introduire dans notre réseau.

Ce n'était pas le but de l'exercice mais l'on peut imaginer le partage de la connexion internet de l'ordinateur serveur. Pour cela il faut retoucher un peu notre configuration du DNS :

*TOUT ce qui suit est expliqué sur la [documentation d'ubuntu](http://doc.ubuntu-fr.org/hostapd) !* Je ne le met là que pour résumer ce que j'ai fait (pour moi quoi).

Fermer le serveur actuel, on le relancera plus tard !

    option domain-name-servers 192.168.0.1;
    subnet 192.168.0.0 netmask 255.255.255.0 {
    	option routers 192.168.0.1;
    	option subnet-mask 255.255.255.0;
    	option broadcast-address 192.168.0.0;
    	option domain-name-servers 192.168.0.1;
    	range dynamic-bootp 192.168.0.15 192.168.0.100;
    }
	
Installer puis configurer un serveur de cache DNS :

    xavier@xavier-desktop:~% sudo apt-get install dnsmasq
	
Et adapter /etc/dnsmasq.conf

    bogus-priv
    filterwin2k
    # no-resolv
    interface=wlan0
    no-dhcp-interface=wlan0


    xavier@xavier-desktop:~% sudo dnsmasq -x /var/run/dnsmasq.pid -C /etc/dnsmasq.conf 

Enfin, on va utiliser [le script magique](http://doc.ubuntu-fr.org/hostapd#exemple_de_script), on a tout bien configurer à priori. Quelques retouches mineurs sont à faire dans ce script mais rien de bien sorcier. Il est tout simplement terrible, c'est LE script qui va bien.

Voilà on a terminé de configurer notre point d'accès wifi, et tout fonctionne bien donc tout va pour le mieux dans le meilleur des mondes.
