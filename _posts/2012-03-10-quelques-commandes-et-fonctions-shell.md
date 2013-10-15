---
layout: post
category : astuce
tagline: ""
tags : [shell, fonctions]
---
{% include JB/setup %}

Comme il m'arrive fréquemment d'écrire quelques scripts shell, je créé cette page pour y rassembler les fonctions que j'utilise souvent et pour pouvoir les retrouver facilement.

*****

# Messages

## Neutre

    # @params neutral message
    show_neutral_message(){ 
    	echo -e "\033[0;36;40m$@\033[0m"
    }

## Information

    # @params information message
    show_information_message(){
    	echo -e "\033[7;32;40m$@\033[0m"
    }
	
## Erreur

    # @params error message
    show_error_message(){
    	echo -e "\033[7;31;47m$@\033[0m"
    }

# Manipulation de date

## Obtenir la date du jour sous forme de chaîne

    TODAY=$(date +%Y%m%d)	# La date du jour, au format YYYYMMDD
	
## Voyager dans le temps

    FILE_DATE=$(date --date="1 day ago" +%Y%m%d)

# Des splits

## Couper une chaîne

    file=$(echo $line | cut -d";" -f1)
    target=$(echo $line | cut -d";" -f2)

# Informations sur l'utilisateur

## Vérifier que l'utilisateur en cours est root
	
    if [ "$USER" != "root" ]
    then
    	echo "Vous devez lancer cette commande en root"
    	exit 0
    fi
	
# Manipulation de fichiers

## Synchronisation

    function sync()
    {
        echo "Copie de $1 vers $2"
        rsync -r --update -t -l $1 /media/xavier_160/save/$2
    }
     
     
    sync        /var/www/                           www/
    sync        /media/Data/dossier_personnel/      dossier_personnel/
    sync        /media/Data/docs/                   docs/

## Backup de site web

    alias xmichel_save='lftp ftp://user:pass@domaine.info -e "mirror -e / /var/www/microdev/nx/xavier/ ; quit"'

# Calculs

    total_sec=$((hour*60*60 + mins*60 + secs))
    # ou
    finalheight=$(expr $finalwidth \* $height / $width)


    function addnums {
      TOTAL=0
      while read val; do
        TOTAL=$(($TOTAL+$val))
      done
      echo $TOTAL
    }
     
    # exemple d'utilisation
    ll | awk '{if ($5=="") print 0 ; else print $5}' | addnums
	
# Android

    sudo /home/xavier/bin/android/platform-tools/adb kill-server && sudo /home/xavier/bin/android/platform-tools/adb devices
	
# Système

## Changement d'adresse mac

    sudo ifconfig eth0 hw ether 00:00:00:00:00:00
	


	