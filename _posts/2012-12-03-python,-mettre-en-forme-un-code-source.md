---
layout: post
category : astuce
tagline: ""
tags : [python, formatage, source]
---
{% include JB/setup %}

Comment remplacer les espaces par des tabulations en tête de ligne dans un code python afin de marquer davantage les niveaux des blocs ?

Un petit script python pour répondre à ce problème.

*****

Il s'agit d'un simple parcours de documents avec remplacement des espaces par des tabulations tant qu'aucun autre caractère n'est détecté.

    import os
     
    files=[f for f in os.listdir('.') if f.endswith('.py') and f != 'space-tab.py']
     
    for nom in files:
    	fich=open(nom,'r')
    	text=fich.read()
    	fich.close()
    	debLigne=True
    	new_text=""
    	for car in text:
    		if car==' ' and debLigne:
    			new_text+='\t'
    		if car != ' ':
    			debLigne=False
    		if not debLigne:
    			new_text+=car
    		if car == '\n':
    			debLigne=True
    	fich=open(nom,'w')
    	fich.write(new_text)
    	fich.close()
    	print nom
		
