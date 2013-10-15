---
layout: post
category : astuce
tagline: ""
tags : [facebook, téléchargement, photo]
---
{% include JB/setup %}

Facebook ne permet pas de télécharger l'intégralité d'un album photo. Cette fonctionnalité serait pourtant bien pratique, c'est pourquoi j'ai réalisé un petit script bash pour télécharger un album.

Ce script ne nécessite pas d'entrer ses identifiants facebook ou autres demande pouvant fragiliser la sécurité de mon compte. Il va simplement scanner le contenu de la page html aperçu de l'album pour aller chercher les images.

*****

Il suffit de télécharger la page html ou l'on a l’aperçu de toutes les photos de l'album et de l'enregistrée dans un dossier. On peut répéter cette opération pour chaque album à récupérer.

Dans ce même dossier, créer un fichier exécutable avec le script suivant (puis le lancer) : 

    for fichier in *.htm
    do
    	i=0
    	mkdir -p "./images/${fichier}/"
    	for miniature_url in $(cat "${fichier}" | sed 's/</\n/g' | grep 'class="uiMediaThumbImg"' | grep -v '/images/spacer.gif' | grep -v '/s480x480' | sed 's/.*url(\(.*\)).*/\1/g')
    	do
    		i=$(($i+1))
    		image_url=$(echo $miniature_url | sed 's/_a.jpg/_n.jpg/')
    		wget --no-check-certificate $image_url -O "./images/${fichier}/$i.jpg"
    	done
    done

Et voilà, 12 lignes qui nous épargnerons de longues récupérations à la main :D
	
