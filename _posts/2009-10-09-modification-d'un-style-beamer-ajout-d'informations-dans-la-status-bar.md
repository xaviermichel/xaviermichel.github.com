---
layout: post
category : astuce
tagline: ""
tags : [latex, beamer]
---
{% include JB/setup %}

Le "problème" avec beamer, c'est que les styles sont prédéfinis et que l'on souhaiterait parfois ajouter une touche perso, par exemple le numéro de diapositive sur le thème warsaw.

C'est l'objet de cette astuce !

Où allons nous placer notre ajout ? C'est bien la première question à se poser ! Voyons déjà le thème par défaut ...

![Thème avant](/assets/posts/beamer_before.png)

Pour ma part je pense qu'il est judicieux de placer notre barre d'information au pied de la diapo ce qui pourrait donner :

![Thème après](/assets/posts/beamer_after.png)

_PS : ne faites pas attention aux fautes d'orthographe sur le slide :p_

Bien entendu, cette barre est très chargée (surtout sur la page de garde), même trop selon moi mais comme qui peut le plus peut le moins, je vais construire une barre complète et nous verrons comment l'adapter à souhait.

Notre barre de base est donc composée de quatre éléments, nous allons commencer par définir leur couleur d'arrière plan.

Par exemple pour le premier élément cela donne quelque chose comme :

    \setbeamercolor*{author in head/foot}{parent=palette tertiary}

Rien de très compliqué donc, on définit notre élément et on utilise la palette pré-définie. La difficulté vient ensuite, pour placer notre élément :

    \defbeamertemplate*{footline}{infolines theme}
    {
    	\leavevmode
    	\hbox{
    		\begin{beamercolorbox}[wd=.25\paperwidth,ht=2.25ex,dp=1ex,center]{author in head/foot}
    			\usebeamerfont{author in head/foot}\insertshortauthor
    		\end{beamercolorbox}
    	}
    }

Détaillons maintenant :
1. On définit notre barre selon le template footline
2. On créé notre "boite"
3. On place les éléments dans cette boite

Si vous testez le code vous constaterez que vous avez un début de barre.

Détaillons la ligne de création de l'élément :
- wd=.25paperwidth : largeur de la barre, ici 25% de la feuille, pour que la barre occupe tout le bas de l'écran il faudra donc que le somme de tous les élément fasse... 1 !
- ht=2.25ex : hauteur de la barre, pas grand chose à ajouter...
- dp=1ex : espace entre le bas du texte et le bord du slide (en bas)
- center : centrer le texte !

Le reste est totalement compréhensible.

A partir de là toute la barre est entièrement personnalisable !

