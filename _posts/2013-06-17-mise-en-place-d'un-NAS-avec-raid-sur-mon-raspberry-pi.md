---
layout: post
category : tutoriel
tagline: ""
tags : [raid 1, NAS, raspberry pi]
---
{% include JB/setup %}

 Hello !

J'ai décidé de rentabiliser mon raspberry pi en y montant un NAS (serveur de stockage réseau), pour pouvoir sauvegarder mes données importantes. Comme le but est de ne rien perdre, j'ai décidé de mettre en place un raid 1 (deux disques dur en réplication). C'est la solution raid logiciel que je vais présenter dans cet article car elle est bien moins coûteuse  raspberry.

Le but de cette article est de voir comment :
- Mettre en place ce système
- Automatiser les sauvegardes
- Tester la récupération en cas de panne

Pour cela pas question de tout faire en live sur mon raspberry ! J'ai préféré faire mes test dans une VM smile.


*****


Le but est de mettre cette solution en place sur mon raspberry pi. Le débit des ports USB n'étant
pas excellent (car partagé entre eux + la carte ethernet), ce NAS ne sera pas avec un débit de
folie en lecture écriture mais il présentera l'avantage d'être de bon marché (voir coût en annexe)
et de ne pas consommer beaucoup d'énergie (le raspberry ne consomme pas grand chose, et les clef usb seront en veille la plupart du temps). Au pire on pourra toujours
[alimenter notre framboise avec l'énergie du soleil](http://reviews.cnet.co.uk/desktops/how-to-make-a-raspberry-pi-solar-powered-ftp-server-50009923/).

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

Pour ma part, j'opte pour un logiciel sur mon client windows : (cobian backup)[http://www.clubic.com/telecharger-fiche11081-cobian-backup.html].

# Maintenance, test de tolérance aux pannes

Ces tests ont été faits sur une VM avec un debian et une configuration identique à celle proposé ci-dessus.

## Panne matérielle

Un des disque est en erreur

    root@debian:/media# mdadm --detail /dev/md0
    /dev/md0:
    		Version : 1.2
      Creation Time : Fri Apr 19 19:49:46 2013
    	 Raid Level : raid1
    	 Array Size : 4087475 (3.90 GiB 4.19 GB)
      Used Dev Size : 4087475 (3.90 GiB 4.19 GB)
       Raid Devices : 2
      Total Devices : 2
    	Persistence : Superblock is persistent
     
    	Update Time : Fri Apr 19 20:18:20 2013
    		  State : clean, degraded
     Active Devices : 1
    Working Devices : 1
     Failed Devices : 1
      Spare Devices : 0
     
    		   Name : debian:0  (local to host debian)
    		   UUID : e24cccb5:6ad3c0be:94487917:fd011ebf
    		 Events : 42
     
    	Number   Major   Minor   RaidDevice State
    	   0       8       17        0      active sync   /dev/sdb1
    	   1       0        0        1      removed
     
    	   1       8       33        -      faulty spare   /dev/sdc1

On peut recroiser avec le fichier /etc/mdadm/mdadm.conf pour être sûr de notre analyse.


Cette commande nous a permis d'identifier le disque en erreur. Une autre solution aurait été :

    root@debian:/media# cat /proc/mdstat
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10]
    md0 : active raid1 sdb1[0] sdc1[1](F)
    	  4087475 blocks super 1.2 [2/1] [U_]

La surveillance que nous avons mis en place précédemment nous remonte des erreurs. L'un des disques est mort. On peut :
- Toujours accéder aux données (réplication)
- Remplacer le disque par un disque identique
- Remplacer le disque par un disque de capacité supérieur
Dans tous les cas, je vous conseille de remplacer le disque sinon vous perdrez toutes vos données en cas de panne du second disque !


### Vérifier que nous avons toujours un accès aux données

Pour cela, c'est très simple. Il nous suffit d'aller dans /media/raid et de vérifier que l'on a encore les données présentes.

    xavier@debian:~$ ls /media/raid/
    lost+found  toto.txt

Nous avons toujours accès à nos données !


### Solution 1 : remplacer le disque dur par un disque identique

On retire le disque précédent d'un point de vue logiciel :

    root@debian:/home/xavier# mdadm --manage /dev/md0 --remove /dev/sdc1
    mdadm: hot removed /dev/sdc1 from /dev/md0
    
Ensuite seulement, j'éteins ma machine et je monte un disque de capacité identique au précédent.

La commande fdisk permet de confirmer que le disque dur remplacé est bien présent :

    root@debian:/home/xavier# fdisk -l
    [...]
    Disk /dev/sdc doesn't contain a valid partition table
     
       Device Boot      Start         End      Blocks   Id  System
    /dev/sdb1               1        1044     8385898+  fd  Linux raid autodetect
    [...]
    Disk /dev/md0 doesn't contain a valid partition table

On reformate maintenant le nouveau disque, voir préparation des disques dur et Formatage et montage des disques (en ne faisant bien évidemment que l'étape de montage !, pas le mkfs !).

    root@debian:/home/xavier# fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0x36db354c.
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
     
    Disk /dev/sdc: 8589 MB, 8589934592 bytes
    255 heads, 63 sectors/track, 1044 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x36db354c

Pour information, actuellement notre raid continu sur un seul disque (donc ne sert à rien).

    root@debian:/home/xavier# cat /proc/mdstat
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
    md0 : active raid1 sdb1[0]
    	  8384862 blocks super 1.2 [2/1] [U_]
     
    unused devices: <none>
    
Ensuite, il faut relancer l'utilisation du disque nouvellement formaté :

    root@debian:/home/xavier# mdadm --manage /dev/md0 --add /dev/sdc1
    mdadm: added /dev/sdc1
    root@debian:/home/xavier# cat /proc/mdstat
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
    md0 : active raid1 sdc1[2] sdb1[0]
    	  8384862 blocks super 1.2 [2/1] [U_]
    	  [======>..............]  recovery = 32.2% (2706560/8384862) finish=1.6min speed=58138K/sec
     
    unused devices: <none>
    root@debian:/home/xavier# 


Jusqu'au point où :

    root@debian:/home/xavier# cat /proc/mdstat
    Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
    md0 : active raid1 sdb1[2] sdc1[1]
    	  8384862 blocks super 1.2 [2/2] [UU]
     
    unused devices: <none>

Notre raid est de nouveau fonctionnel !

### Solution 2 : remplacer le disque par un disque de plus grande capacité

Cette solution est a peu prêt équivalente à celle que nous avons testé juste avant, je veux juste m'assurer qu'il est possible d'utiliser un disque plus gros dans le futur.

Voici les partitions que j'ai avant de commencer mes tests :

    xavier@rebian:~$df -h
    Sys. de fichiers	Taille	Uti.		Disp.	Uti%		Monté sur
    /dev/md0			3.9G		72M		3.8G		2%		/media/raid

Je retire maintenant l'un des disques dur. Le raid continue à fonctionner en utilisant un seul disque. Si je regarde le statut via mdadm, l'état du disque est à "removed".

Je créé un disque de 12 Go pour remplacer celui qui ne fonctionne pas. Je le branche et redémarre ma debian. Un coup de cat /proc/mdstat m'indique que mon raid ne tourne que sur sdc1. Je valide que c'est bien sdb qui doit être préparé avec un coup de fdisk -l.

Je prépare ensuite mon disque a changer (fdisk /dev/sdb).

Enfin, je réinsère le disque fraîchement préparé dans l'array :

    root@debian:/home/xavier# mdadm --manage /dev/md0 --add /dev/sdb1
    mdadm: added /dev/sdb1

Et on a plus qu'à attendre la fin de la reconstruction de l'array (voir cat /proc/mdstat).

Un fois l'array reconstruit, j'ai bien à nouveau un raid 1 en réplication sur mes deux disques, avec un espace possible de 3.9G.

Par curiosité, je retire le plus petit (et vieux) et deux disques et le remplace par un disque de 16Go. Je prépare ce dernier et l'intègre au raid. La taille de mon raid reste à 3.9G malgé les 12G de disponible.

Nos disques ont été formatés en utilisant tout l'espace libre, nous devons donc seulement augmenter la taille de l'array puis au niveau système. Pour cela, j'ai suivi [les instructions trouvées ici](http://webapp5.rrz.uni-hamburg.de/SuSe-Dokumentation/manual/sles-manuals_en/manual/raidresize.html).

Cela donne :

    root@debian:/home/xavier# df -h
    Sys. de fichiers    Taille  Uti. Disp. Uti% Monté sur
    /dev/md0              3,9G   72M  3,8G   2% /media/raid
    root@debian:/home/xavier# mdadm -D /dev/md0
    /dev/md0:
    		Version : 1.2
      Creation Time : Fri Apr 19 19:49:46 2013
    	 Raid Level : raid1
    	 Array Size : 4087475 (3.90 GiB 4.19 GB)
      Used Dev Size : 4087475 (3.90 GiB 4.19 GB)
       Raid Devices : 2
      Total Devices : 2
    	Persistence : Superblock is persistent
     
    	Number   Major   Minor   RaidDevice State
    	   3       8       17        0      active sync   /dev/sdb1
    	   2       8       33        1      active sync   /dev/sdc1
    root@debian:/home/xavier# mdadm --grow /dev/md0 -z max
    mdadm: component size of /dev/md0 has been set to 12280637K
    root@debian:/home/xavier# mdadm -D /dev/md0
    /dev/md0:
    		Version : 1.2
      Creation Time : Fri Apr 19 19:49:46 2013
    	 Raid Level : raid1
    	 Array Size : 12280637 (11.71 GiB 12.58 GB)
      Used Dev Size : 12280637 (11.71 GiB 12.58 GB)
       Raid Devices : 2
      Total Devices : 2
    	Persistence : Superblock is persistent
     
    	Number   Major   Minor   RaidDevice State
    	   3       8       17        0      active sync   /dev/sdb1
    	   2       8       33        1      active sync   /dev/sdc1
    root@debian:/home/xavier# resize2fs /dev/md0
    resize2fs 1.41.12 (17-May-2010)
    Le système de fichiers de /dev/md0 est monté sur /media/raid ; le changement de taille doit être effectué en ligne
    old desc_blocks = 1, new_desc_blocks = 1
    En train d'effectuer un changement de taille en ligne de /dev/md0 vers 3070159 (4k) blocs.
    Le système de fichiers /dev/md0 a maintenant une taille de 3070159 blocs.
     
    root@debian:/home/xavier# df -h
    Sys. de fichiers    Taille  Uti. Disp. Uti% Monté sur
    /dev/md0               12G   74M   12G   1% /media/raid

Et voilà, nous avons doublé la taille de notre raid sans perdre une donnée !

## Panne logicielle

Le système est tombé, mais nos disques sont probablement intacts.

Notre système de sauvegarde est donc mort, on souhaite remonter un système pour remettre en place notre NAS. Mais dans l'attente, il est possible que nous souhaitions récupérer nos données.

### Récupérer nos données à l'aide d'un autre système et d'un seul des disque

J'ai réinstallé une debian, sur un nouveau système et branché un des disques de notre ex RAID. Je peux repérer la partition en question avec un fdisk.

En fait, cette étape est superflue dans mon cas car lors de l'installation de mdadm, celui-ci détecte l'array existant et le remonte.

    root@debian2:/home/xavier# fdisk -l
     
    Disk /dev/sda: 8388 MB, 8388108288 bytes
    255 heads, 63 sectors/track, 1019 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x000ed18d
     
       Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *           1         972     7804928   83  Linux
    Partition 1 does not end on cylinder boundary.
    /dev/sda2             972        1020      384001    5  Extended
    Partition 2 does not end on cylinder boundary.
    /dev/sda5             972        1020      384000   82  Linux swap / Solaris
     
    Disk /dev/sdb: 12.6 GB, 12582420480 bytes
    255 heads, 63 sectors/track, 1529 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x3961ebcf
     
       Device Boot      Start         End      Blocks   Id  System
    /dev/sdb1               1        1529    12281661   fd  Linux raid autodetect
    root@debian2:/home/xavier# mdadm
    bash: mdadm : commande introuvable
    root@debian2:/home/xavier# apt-get install mdadm
    Lecture des listes de paquets... Fait
    Construction de l'arbre des dépendances      
    Lecture des informations d'état... Fait
    Les NOUVEAUX paquets suivants seront installés :
      mdadm
    0 mis à jour, 1 nouvellement installés, 0 à enlever et 0 non mis à jour.
    Il est nécessaire de prendre 451 ko dans les archives.
    Après cette opération, 995 ko d'espace disque supplémentaires seront utilisés.
    Réception de : 1 http://ftp.fr.debian.org/debian/ squeeze/main mdadm i386 3.1.4-1+8efb9d1+squeeze1 [451 kB]
    451 ko réceptionnés en 0s (998 ko/s)
    Préconfiguration des paquets...
    Sélection du paquet mdadm précédemment désélectionné.
    (Lecture de la base de données... 117583 fichiers et répertoires déjà installés.)
    Dépaquetage de mdadm (à partir de .../mdadm_3.1.4-1+8efb9d1+squeeze1_i386.deb) ...
    Traitement des actions différées (« triggers ») pour « man-db »...
    Paramétrage de mdadm (3.1.4-1+8efb9d1+squeeze1) ...
    Generating array device nodes... done.
    Generating mdadm.conf... done.
    update-initramfs: deferring update (trigger activated)
    Starting MD monitoring service: mdadm --monitor.
    Assembling MD array md0...done (degraded [1/2]).
    Generating udev events for MD arrays...done.
    Traitement des actions différées (« triggers ») pour « initramfs-tools »...
    update-initramfs: Generating /boot/initrd.img-2.6.32-5-686
    root@debian2:/home/xavier# cat /proc/mdstat
    Personalities : [raid1]
    md0 : active raid1 sdb1[3]
    	  12280637 blocks super 1.2 [2/1] [U_]
     
    unused devices: <none>
    root@debian2:/home/xavier# mount /dev/md0 /mnt/
    root@debian2:/home/xavier# cd /mnt/
    root@debian2:/mnt# ls
    lost+found  toto.txt

Et hop, mdadm a tout remonté lui même.

Voulant tester le cas où tout ne se serait pas si bien passé, je casse l'array existant, vérifie qu'il n'existe plus et tente de le remonter.

    root@debian2:~# mdadm -S /dev/md0
    mdadm: stopped /dev/md0
    root@debian2:~# cat /proc/mdstat
    Personalities : [raid1]
    unused devices: <none>
    root@debian2:~# mdadm -A /dev/md0 /dev/sdb1
    mdadm: /dev/md0 has been started with 1 drive (out of 2).
    root@debian2:~# cat /proc/mdstat
    Personalities : [raid1]
    md0 : active raid1 sdb1[3]
    	  12280637 blocks super 1.2 [2/1] [U_]
     
    unused devices: <none>
    root@debian2:~#
    
### Remonter un système pour remettre en place notre raid

Deux solutions, on effectue l'étape précédente et l'on réintègre un second disque dans l'array (tout comme lorsque l'on remplace un disque déffectueux).

Ou, pour éviter de toute reconstruire, on repart sur un système tout propre et on installe mdadm qui va surement tout détecter.

    TODO

    
Si ce n'est pas le cas, nous pourrons utiliser le /etc/mdadm/mdadm.conf que nous avons pensé à sauvegarder, et lancer la commande :

    mdadm --assemble --scan

Sinon, on peut retrouver les disques concernés à coup de fdisk et recréer l'array :

    mdadm --assemble /dev/md0 /dev/sdb1 /dev/sdc1

Enfin, on n'oublie pas de sauvegarder sa conf pour le prochain reboot :

    mdadm --detail --scan --verbose > /etc/mdadm/mdadm.conf

# Annexe

## Vérifier l'état du raid

    root@debian:/home/xavier# mdadm --detail /dev/md0
    
## Coût de la mise en place de la solution

| Tables        | Cool  |
| ------------- | -----:|
| [Raspberry PI modèle B](http://www.raspberrypi.org/faqs) (avec boitier, câble et frais de port)     | 52,67 € |
| [Carte SD (32Go, classe 10)](http://www.amazon.fr/Transcend-TS32GUSDHC10E-Carte-m%C3%A9moire-Classe/dp/B008UR8TS0) pour l'OS du raspberry      | 24,85 € |
| [Clé USB de 64Go](http://www.darty.com/nav/achat/accessoires/stockage/cle_usb/lexar_media_jump_drive_s73_64g.html) | 2 x 54,90 € |
| *Total* | *187,32 €* |


***
>*Migration commentaires :*
>
>> soulfire82, le 11/09/2013 à 22:29
>> Bonjour,
>> 
>> Ce tuto est très intéressant!
>> Je l'ai mis en pratique pour me monter un serveur NAS avec des disques de 500Go afin de sauvegarder mes données. Cependant j'ai une question. Le fait de débrancher un ou deux disques implique obligatoirement de tout reconstruire? Le disque n'était plus détecté j'ai donc du le rajouter avec la commande mdadm --manage /dev/md0 --add /dev/sda1.
>> 
>> Avec 500Go j'en ai pour 8h a chaque fois!
>> 
>> Sinon super tuto merci!
>
> ***
>
>> Xavier, le 12/09/2013 à 13:37
>> Bonjour et merci pour ce retour smile
>> 
>> Hélas s'il y a eu des écritures entre temps, il faut reconstruire entièrement l'array  downer
>> 
>> Il faut donc mieux éviter de débrancher le disque wink . Sinon, il faut stopper le raid au niveau logiciel avant de débrancher le disque.
>> 
>> Xavier
***
