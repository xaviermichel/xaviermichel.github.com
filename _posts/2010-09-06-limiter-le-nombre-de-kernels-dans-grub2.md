---
layout: post
category : astuce
tagline: ""
tags : [grub2, kernels]
---
{% include JB/setup %}

Si comme moi vous vous sentez envahi par d'innombrables entrées dans votre grub, il faut faire un peu de vide... 

Comment ? Et ben on va voir ça de suite !

*****

Pour limiter le nombre de noyaux apparaissant comme entré dans grub 2, éditons */etc/grub.d/10_linux*.

Déclarons une variable au début du script, avec le nombre d'entrées souhaitées.

    # Nombre max de kernel à afficher
    NBMAXKERNEL=$((2))
	
Cherchons le fameux while qui parcourt la liste des kernels, ajoutons lui la condition sur le nombre de kernels :

    while [ "x$list" != "x" -a $NBMAXKERNEL -gt 0 ] ; do
	
Et juste après être entré dans ce fameux while, décrémentons notre variable :

    NBMAXKERNEL=$(($NBMAXKERNEL-1))
	
Ensuite on sauvegarde, on lance la commande update-grub2 et on est heureux :)

