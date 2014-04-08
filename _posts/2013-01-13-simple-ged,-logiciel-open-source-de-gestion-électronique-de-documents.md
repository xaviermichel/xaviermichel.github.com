---
layout: post
category : projet
tagline: ""
tags : [ged]
---
{% include JB/setup %}

Simple EDM est un logiciel de gestion électronique de document à destination des particuliers, via une gestion simplifiée.

Ce logiciel est open source et il a pour but d'évoluer pour répondre à tous les besoins des utilisateurs, à vous de me suggérer des évolutions ! 

*****

# Présentation du projet

## Présentation de la GED

La GED est amplement présentée sur [wikipédia](http://fr.wikipedia.org/wiki/Gestion_%C3%A9lectronique_des_documents).

Notre logiciel ne vise pas une utilisation professionnelle mais plutôt personnelle avec une gestion simplifié, assez personnalisable et qui devrait encore s'améliorer dans le futur. Le mot d'ordre, c'est une GED pour une utilisation en tant que particulier. Les GED d'entreprise dont très complètes, mais trop complexe pour un usage maison. Le but était donc de simplifier ! (notez cependant que la scalabilité est prise en compte par simple-edm).

On trouve notamment sur notre logiciel la possibilité d'ajouter directement un document via l'explorateur. Notons que tous les types de documents peuvent être sauvegarder (pdf, doc...). Grâce une recherche via le moteur de recherche, retrouvez votre document presque instantannément !

## Pourquoi ce projet ?

De plus en plus on vous demande la copie de tel ou tel document, où l'ais-je rangé déjà celui là ? Afin d'éviter ce genre de soucie, un stockage sur votre ordinateur de ce document de façon très simple et vous avez la possibilité de le retrouver en quelques cliques !

# Notre logiciel

## Les fonctionnalités de notre système de GED

Notre système propose donc les fonctionnalités suivantes :
- Possibilité de créer des gros groupes de documents (par exemple factures, scolaire...). Ces groupes sont appelés classeurs.
- Un moteur de recherche puissant qui va chercher la correspondance dans le document (texte, word, ...) même !
- Un moteur de plugin, qui permet de facilement ajouter des fonctionnalités dans se plonger au coeur du code source.

## Stratégie

A l'heure où le cloud computing se développe, je préfère avoir mes documents en local car ce sont des données personnelles. C'est pour cette raison que simple GED garde vos documents en local uniquement (le mode "cloud" personnel est utilisable, voir la partie scalabilty du README sur github).


# Téléchargement et installation

En résumé, on [télécharge simple EDM]() ici puis on extrait le zip téléchargé et on lance "simple-edm-windows-starter.cmd.

Si vous rencontrer une difficulté ou si vous voulez proposer une fonctionnalité, n'hésitez pas à me contacter via les commentaires.

Ce logiciel est open source, vous pouvez récupérer les sources sur notre [dépôt GIT](https://github.com/xaviermichel/simple-edm).

*Vos propositions ou améliorations sont les bienvenues.*
