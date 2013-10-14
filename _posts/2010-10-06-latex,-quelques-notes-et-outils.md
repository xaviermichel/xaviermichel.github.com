---
layout: post
category : astuce
tagline: ""
tags : [latex]
---
{% include JB/setup %}

Quelques astuces pour utiliser latex avec une grande simplicité.

Je compléterai cet article de temps à autres, en fonction des choses qui me sembles utiles comme l'installation d'un package, ou encore la compilation latex via un Makefile.

*****

# Installation d'un package

Ici nous allons installer picins qui n'est pas dans les paquets ubuntu. Commençons par [le télécharger](http://tug.ctan.org/tex-archive/macros/latex209/contrib/picins/).

On admet que l'on a récupéré le tut dans un dossier picins, et on va déplacer le contenu dans /usr/share/texmf/tex/latex :

    sudo cp -R picins /usr/share/texmf/tex/latex/
	
Il faut maintenant mettre à jour la base tex via la commande :

    sudo texhash
	
Nous pouvons maintenant utiliser picins !

# Le makefile qui va bien

Voici un makefile magique qui vous permettra de compiler vos .tex sans laisser de références vide. Par contre je ne gère pas de bibliographie comme je n'en fais jamais.

    PDF_NAME=index.pdf	# nom du pdf de sortie
    SOURCE_NAME=index.tex	# nom du fichier tex principal (qui inclut les autres) sans son extension !
    LOG_FILE=index.log		# nom du fichier de log
     
    all: clean $(PDF_NAME)
     
    pdf: $(PDF_NAME)
     
    $(PDF_NAME): $(SOURCE_NAME)
    	pdflatex $(SOURCE_NAME)
    	latex_count=5 ; \
    	while egrep -s 'There were undefined references.' $(LOG_FILE) && [ $$latex_count -gt 0 ] ;\
    	    do \
    	      echo "Rerunning latex...." ;\
    	      pdflatex $(SOURCE_NAME) ;\
    	      latex_count=`expr $$latex_count - 1` ;\
    	    done
     
     
    clean:
    	rm -f *.ps *.dvi *.aux *.toc *.idx *.ind *.ilg *.log *.out *.glo *.lof $(PDF_NAME)


# Les packages qu'il vous faut !

Dans cette partie je vais mettre une liste de package que je vais essayer de vous vendre  :)

## fncychap

Ce package génial vous permet de rendre vos titres plus jolies, plus brillants, plus mieux ! Je vous invite donc à lire [la documentation](http://www.ctan.org/tex-archive/help/Catalogue/entries/fncychap.html).

