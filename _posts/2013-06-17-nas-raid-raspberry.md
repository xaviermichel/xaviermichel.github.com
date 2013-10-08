---
layout: post
category : raspberry
tagline: ""
tags : [raid 1, NAS, raspberry pi]
---
{% include JB/setup %}

> Hello !
> 
> J'ai décidé de rentabiliser mon raspberry pi en y montant un NAS (serveur de stockage réseau), pour pouvoir sauvegarder mes données importantes. Comme le but est de ne rien perdre, j'ai décidé de mettre en place un raid 1 (deux disques dur en réplication). C'est la solution raid logiciel que je vais présenter dans cet article car elle est bien moins coûteuse  raspberry.
> 
> Le but de cette article est de voir comment :
> - Mettre en place ce système
> - Automatiser les sauvegardes
> - Tester la récupération en cas de panne
> 
> Pour cela pas question de tout faire en live sur mon raspberry ! J'ai préféré faire mes test dans une VM smile.



Le but est de mettre cette solution en place sur mon raspberry pi. Le débit des ports USB n'étant 
pas excellent (car partagé entre eux + la carte ethernet), ce NAS ne sera pas avec un débit de 
folie en lecture écriture mais il présentera l'avantage d'être de bon marché (voir coût en annexe) 
et de ne pas consommer beaucoup d'énergie (le raspberry ne consomme pas grand chose, et les clef 
usb seront en veille la plupart du temps). Au pire on pourra toujours 
[http://reviews.cnet.co.uk/desktops/how-to-make-a-raspberry-pi-solar-powered-ftp-server-50009923/](alimenter notre framboise avec l'énergie du soleil).

# Pré-requis

- Un serveur tournant sous linux (mon raspberry pour ma part)
- Deux clé usb pour faire un raid 1. J'ai choisi des clé usb car je n'ai pas besoin d'un grand espace de stockage, et de plus, les performances sont bridées avec l'USB

> Ayant fait les manipulations ci-dessous via une VM dans un premier temps, il faut remplacer "disque dur" par "clé USB" pour être dans le cas des manipulations que j'ai fait sur mon raspberry. Pour mon raspberry, cela fait deux clés de 64 Go.


# Préparation des disques dur

Nous avons nos disques dur fraîchement achetés, il convient maintenant de les préparer pour la mission qui les attend.

Nous allons maintenant repérer nos disques :

    root@debian:/home/xavier# fdisk -l
     
    Disk /dev/sda: 8589 MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x000db52d
     
       Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *           1         996     7993344   83  Linux
    Partition 1 does not end on cylinder boundary.
    /dev/sda2             996        1045      392193    5  Extended
    /dev/sda5             996        1045      392192   82  Linux swap / Solaris
     
    Disk /dev/sdb: 8589 MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000
     
    Disk /dev/sdb doesn't contain a valid partition table
     
    Disk /dev/sdc: 8589 MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x00000000
     
    Disk /dev/sdc doesn't contain a valid partition table
    root@debian:/home/xavier# 

Vous pouvez repérer vos nouveaux disques. Il s'agit ici de `/dev/sdb` et `/dev/sdc`.

Nous allons préparer ces disques pour notre besoin : avec un partition raid linux.

    root@debian:/home/xavier# fdisk /dev/sdb
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xb2cdac70.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.
     
    Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)
     
    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
             switch off the mode (command 'c') and change display units to
             sectors (command 'u').
     
    Command (m for help): n
    Command action
       e   extended
       p   primary partition (1-4)
    p
    Partition number (1-4): 1
    First cylinder (1-1044, default 1): 
    Using default value 1
    Last cylinder, +cylinders or +size{K,M,G} (1-1044, default 1044): 
    Using default value 1044
     
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    Changed system type of partition 1 to fd (Linux raid autodetect)
     
    Command (m for help): p
     
    Disk /dev/sdb: 8589 MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0xb2cdac70
     
       Device Boot      Start         End      Blocks   Id  System
    /dev/sdb1               1        1044     8385898+  fd  Linux raid autodetect
     
    Command (m for help): w
    The partition table has been altered!
     
    Calling ioctl() to re-read partition table.
    Syncing disks.
    root@debian:/home/xavier# 

> Si un partition FAT32 existe déjà sur le support, il faut bien entendu la supprimer (commande d).

Il faut refaire exactement la même chose avec le second disque du raid.

Si on joue la commande fdisk, on constate que nos disques dont prêts ! 

       Device Boot      Start         End      Blocks   Id  System
    /dev/sdb1               1        1044     8385898+  fd  Linux raid autodetect
    [...]
       Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1               1        1044     8385898+  fd  Linux raid autodetect

# Installation et configuration du logiciel de raid

J'ai choisi le populaire mdadm, l'installation en est d'autant plus simple :

    root@debian:/home/xavier# apt-get install mdadm
	
Ensuite, on lance la construction de l'array (/dev/md0) et on attend la fin...

    root@debian:/home/xavier# mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
    mdadm: Note: this array has metadata at the start and
        may not be suitable as a boot device.  If you plan to
        store '/boot' on this device please ensure that
        your boot-loader understands md/v1.x metadata, or use
        --metadata=0.90
    mdadm: size set to 8384862K
    Continue creating array? y
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md0 started.
    root@debian:/home/xavier# watch -n 1 cat /proc/mdstat
    Personalities : [raid1] 
    md0 : active raid1 sdc1[1] sdb1[0]
          8384862 blocks super 1.2 [2/2] [UU]
          [==>..................]  resync = 12.7% (1066176/8384862) finish=1.3min speed=88848K/sec
     
    unused devices: <none>
    root@debian:/home/xavier# 
	
> Sur mon rapsberry et pour deux clés de 64Go, cette opération a pris une heure (environ 17M/sec pour une clé déclarée avec une vitesse d'écriture à 20Mo/s).

Enfin, nos DD sont fin prêts !

    root@debian:/home/xavier# cat /proc/mdstat
    Personalities : [raid1] 
    md0 : active raid1 sdc1[1] sdb1[0]
          8384862 blocks super 1.2 [2/2] [UU]
     
    unused devices: <none>
    root@debian:/home/xavier# 
	
On peut également obtenir les informations via la commande :

    root@debian:/home/xavier# cat /proc/mdstat
    Personalities : [raid1] 
    md0 : active raid1 sdc1[1] sdb1[0]
          8384862 blocks super 1.2 [2/2] [UU]
     
    unused devices: <none>
    root@debian:/home/xavier# mdadm --detail /dev/md0
    /dev/md0:
            Version : 1.2
      Creation Time : Wed Apr 17 20:49:34 2013
         Raid Level : raid1
         Array Size : 8384862 (8.00 GiB 8.59 GB)
      Used Dev Size : 8384862 (8.00 GiB 8.59 GB)
       Raid Devices : 2
      Total Devices : 2
        Persistence : Superblock is persistent
     
        Update Time : Wed Apr 17 20:50:59 2013
              State : clean
     Active Devices : 2
    Working Devices : 2
     Failed Devices : 0
      Spare Devices : 0
     
               Name : debian:0  (local to host debian)
               UUID : 9c268e33:336a16eb:f1d3b427:e080e44d
             Events : 32
     
        Number   Major   Minor   RaidDevice State
           0       8       17        0      active sync   /dev/sdb1
           1       8       33        1      active sync   /dev/sdc1
    root@debian:/home/xavier# 
	
Il est maintenant très important de sauvegarder la configuration de notre array. Cela se fait simplement via la commande :

    mdadm --detail --scan --verbose > /etc/mdadm/mdadm.conf
	
N'hésitons pas à jeter un œil à /etc/mdadm/mdadm.conf pour vérifier que le contenu nous parrait correct ;) .

# Formatage et montage des disques

J'ai choisi de ne pas utiliser lvm. Bien que cela vende du rêve, je n'en ai pas réellement besoin et cela viendrait complexifier mon stockage.

Notre array (/dev/md0) étant prêt, nous pouvons formater les disques pour notre besoin. J'ai choisi l'ext4.

    mkfs.ext4 /dev/md0 -m 1
	
Nous allons désormais monter notre /dev/md0 et rendre persistant le montage via la fstab. Notre politique de sécurité sera que notre raid sera accessible aux utilisateurs du groupe "raid" uniquement. 

    # on créé le groupe
    addgroup raid
    # on tour à tour chaque utilisateur qui aura le droit d'accès au raid
    adduser xavier raid
    # on créé le point de montage
    mkdir /media/raid
    # on monte notre array pour voir si cela fonctionne
    mount /dev/md0 /media/raid
    # on applique les droits au point de montage
    chown root:raid /media/raid
    chmod 770 /media/raid

Pour rendre le montage permanant, il est nécessaire de l'ajouter dans /etc/fstab (ajouter la ligne suivante)

    /dev/md0   /media/raid   ext4      defaults,nofail     0   0
	
L'option nofail permet de ne pas bloquer le démarrage du système en cas d'erreur.

Enfin, faisons un petit reboot pour vérifier que tout se mets bien en place au démarrage !

# Mise en place des partages samba !

Afin de simplifier l'utilisation du NAS (et comme je suis tout seul sur mon réseau local), j'ai fait une configuration très basique pour l'ajout d'un point de partage. Par exemple :
	
    [Pi-common]
    	comment = Partage public
    	path = /media/raid/common
    	browseable = yes
    	writeable = yes
    	guest ok = yes
    	read only = no
		
Mais on peut ajuster un peu les droits. Pour cela, il faut avoir définit le mot de passe samba d'un utilisateur (commande smbpasswd), par exemple :

    smbpasswd -a xavier
	
Une fois le mot de passe défini, on enregistre le point de partage pour un utilisateur spécifique :

    [Pi-secret]
    	comment = Mon dossier secret
    	path = /media/raid/secret
    	valid users = xavier
    	browseable = no
    	guest ok = no
    	read only = no

# Mise en place de surveillance d'un disque défectueux.
		
Pour la surveillance, j'ai mis un job dans ma crontab qui m'envoi un mail en cas d'alerte :

    # Vérifier l'état du raid toutes les heures
    0 * * * * /bin/grep 'UU' /proc/mdstat || /usr/bin/mail -s "[ALERTE] Raid en erreur sur $(hostname)" toto@rome.biz < /proc/mdstat
	
	Note : pensez à vérifier que la commande mail fonctionne chez vous :D
	
	# Sauvegarde des données
	
	Pour ma part, j'opte pour un logiciel sur mon client windows : [http://www.clubic.com/telecharger-fiche11081-cobian-backup.html](cobian backup).
	
	# Maintenance, test de tolérance aux pannes
	
	Ces tests ont été faits sur une VM avec un debian et une configuration identique à celle proposé ci-dessus.
	
	
	A compléter !
	
	
	