---
layout: post
category : projet
tagline: ""
tags : [log4j, colorier, bash]
---
{% include JB/setup %}

Aujourd'hui je me propose de répondre à une problématique courante. 

On a une application java qui produit des logs à la pelle et on aimerait un peu filtrer et colorier tout cela. 

De plus, on souhaite que l'on puisse faire ce filtrage/coloriage facilement et utiliser le script directement sur un serveur (pour un site en JEE par exemple). Comme la plupart des serveurs sont des linux, je me propose de répondre à cette problématique par un script bash.

*****

# Cahier des charges

Notre script devra répondre aux fonctionnalités suivantes :
- Possibilité de masquer des lignes si elles contiennent un tag donné
- Possibilité de colorier les lignes si elles contiennent un tag donné
- Facilité la configuration via un fichier de configuration
- Enfin, les tags peuvent être soit fixes, soit définis par des expressions régulières

Comme nous pouvons le voir, tout va tourner autours d'un système de tag associé à une couleur.

Pour répondre au point masquage de lignes, nous allons décider que si la couleur est vide la ligne doit être masquée.

# Configuration

Voici un exemple de fichier de configuration que nous allons utiliser. Les lignes commençant par # sont bien évidemment des commentaires.

    # fichier de configuration pour la coloration de logs
     
    # TAG=color
    # TAG correspond au motif recerche
    #
    # Pour visulaliser les couleurs, faire ./color_my_log.sh PALETTE
    #
    # Si color est vide, la ligne qui correspond est ignoree
    # Des qu'une correspondance est trouvee, le style est applique (priorite)
     
     
    # logs a ignorer
     
    ignore=
    un tag a ignorer=
    ignore moi=
     
    # notez que cela fonctionne aussi avec des regexp
    ^$=
    ^[0-9]*$=
     
    # mettre en moins important
     
    un tag avec des espaces=0;37;40
     
    # mettre en important
     
    un tag important=0;30;47
    XMI=0;30;47
     
    # par defaut colorier les niveaux de logs
     
    DEBUG=0;37;40
    INFO=0;36;40
    WARN=0;33;40
    ERROR=0;31;40
    FATAL=0;37;41

# Programme principal

Nous allons ensuite écrire notre script, lecture du fichier de configuration, parsing du fichier... Rien de très compliqué du coup je vous le donne en version un peu brut de forme sans le découper :

Notons également que l'argument PALETTE permet d'afficher la palette de couleur. Sinon cet argument est considéré comme le nom du fichier à suivre.

    #!/bin/sh
     
    CONF_FILE=color_my_log.config
     
     
    if [ $# -eq 0 ]
    then
            echo -e "   \033[0;30;47m-> Lecture de l'entree standard\033[0m"
            MODE=STDIN
    else
            echo -e "   \033[0;30;47m-> Lecture du fichier donne en argument\033[0m"
            MODE=ARG
            FILE=$1
    fi
     
     
    if [ "$1" == "PALETTE" ]
    then
     
            ############################################################
            # Nico Golde <nico(at)ngolde.de> Homepage: http://www.ngolde.de
            # Last change: Mon Feb 16 16:24:41 CET 2004
            ############################################################
     
            for attr in 0 1 4 5 7 ; do
                    echo "----------------------------------------------------------------"
                    printf "\t\t- ESC[%s;Foreground;Background -\n" $attr
                    for fore in 30 31 32 33 34 35 36 37; do
                            for back in 40 41 42 43 44 45 46 47; do
                                    printf '\033[%s;%s;%sm s;s  ' $attr $fore $back $fore $back
                            done
                            printf '\033[0m\n'
                    done
            done
     
            ############################################################
     
            exit 0
    fi
     
     
    ############################################################
     
     
    # lit le fichier de configuration
    read_configuration_file() {
            i=0
            while read line
            do
                    # on ignore les lignes de commentaire
                    if [[ "$line" == \#* ]]; then
                            continue
                    fi
                    # on ignore les lignes vides...
                    if [ -z "$line" ]; then
                            continue
                    fi
     
                    key=$(echo $line | cut -d"=" -f1);
                    value=$(echo $line | cut -d"=" -f2);
     
                    # pas de tableau associatif avant bash 4 <img src="http://www.xaviermichel.info/data/smileys/hmm.gif" width="19" height="19" alt="hmmm" style="border:0;" />
                    color_keys[i]=$key
                    color_values[i]=$value
                    let i++
     
                    if [ -z "$value" ]; then
                            echo -e "Les lignes contenant la clef '\033[0;35;40m$key\033[0m' seront ignorees"
                            continue
                    fi
     
                    echo -e "\033[${value}m${key}\033[0m"
     
            done < "$CONF_FILE"
    }
     
     
    # Effectue la coloration de la ligne donnee en argument
    do_coloration() {
            line=$@
     
            for key in "${!color_keys[@]}"; do
     
                    count=$(echo "$line" | grep -c "${color_keys[$key]}")
     
                    if [ ! $count -eq 0 ]; then
                            if [ -z "${color_values[$key]}" ]; then
                                    return
                            fi
     
                            echo -e "\033[${color_values[$key]}m${line}\033[0m"
                            return
                    fi
     
            done
     
            echo "$line"
    }
     
     
    ############################################################
    # main
    ############################################################
     
    read_configuration_file
     
    echo -e "\033[0;30;47m====================\033[0m"
     
    if [ $MODE == "STDIN" ]
    then
            while read line
            do
                    do_coloration "$line"
            done
    else
            tail -f "$FILE" | while read line
            do
                    do_coloration "$line"
            done
    fi

	
# Exemple d'utilisation

Il y a diverse façon d'utiliser ce script.

Affichage de la palette de couleur :

    $ ./color_my_log.sh PALETTE
	
Suivre un fichier :

    $ ./color_my_log.sh log.txt 	# effectue un tail -f sur log.txt
    # ou
    $ tail -f log.txt | color_my_log.sh
    # ou encore
    $ zcat logs/archives/stdout.log.120920.gz | grep -A 50 -B 50 'pattern' | ./color_my_log.sh

Essayez le ! :)


