---
layout: post
category : projet
tagline: ""
tags : [android, anniversaires]
---
{% include JB/setup %}

Comme je commence un peu à programmer sous android, je cherchais des idées de programme simple et facilement réalisables.

J'ai eu l'idée de faire un petit programme pour prévenir l'utilisateur du téléphone lorsqu'il y a des anniversaires le jour courant. Cela m'a permis de pratiquer le développement android tout en produisant un produit utile.

*****

# Notes

- Auteur : Xavier MICHEL
- Version 2.0 du 28/05/2011
- Android version 2.1 minimum requise

Ce logiciel est testé sur une motodefy avec android version 2.2. Je n'ai pas assez de sous pour acheter d'autres téléphones alors je ne peux pas tester d'autres configurations.

Si vous utilisez task killer, pensez à retirer Never Forget Birthday des applications à tuer.

_Si vous utilisez task killer, pensez à retirer Never Forget Birthday des applications à tuer._


# Téléchargement

- [Télécharger Never Forget Birthday (apk)](/assets/posts/neverForgetBirthday.apk)
- Pour les sources, voir [le dépôt sur github](https://github.com/xaviermichel/android-birthday)

# Fonctionnalités

L'application lance un service au démarrage du téléphone.

Au démarrage, on trouve la liste des contacts qui ont leur date d'anniversaire renseignée, cette liste est classée selon l'arrivée plus ou moins proche de l'anniversaire.

![L'application](/assets/posts/neverForgetBirthday-main.png)
 
Ces contacts sont bien entendu ceux de votre compte google. S'ils ont une image associée elle est affichée, sinon on utilise le logo de l'application.

La première chose à faire est de configurer l'heure de vérification des nouveaux anniversaires, menu -> préférences -> heure de rafraîchissement.

![Configuration](/assets/posts/neverForgetBirthday-config.png)
 
Avec cette configuration, le logiciel affichera une notification d'anniversaires tous les jours à 7h30 (s'il y a des anniversaires ce jour là). Si cette heure se trouve pendant les heures creuses du téléphone, la notification sera créée au prochain réveil du téléphone après l'heure configurée.

Note : Si avec cette configuration vous démarrez votre téléphone à 9h, la notification sera quand même créée. Il n'y a donc pas besoin de mettre midi pour être sûr d'être levé smile, vous aurez la notification au démarrage du téléphone. Si par contre vous aviez mis 15 heures, la notification n’apparaîtra pas avant 15 heures.

Revenons sur l'écran d'accueil, un clique long sur un contact ouvre une popup qui vous permet de masquer le contact de la liste :

![Popup](/assets/posts/neverForgetBirthday-popup_hide.png)
 
Nous allons voir par la suite comment remettre un contact masqué dans la liste des anniversaires.

Le bouton menu vous donne accès à d'autres possibilités du logiciel.

![Menu](/assets/posts/neverForgetBirthday-menu.png)
 
*Paramètres :* permet de configurer à quelle heure le logiciel vérifie s'il y a des anniversaires et créé la notification.

Cette notification ne produit aucun son et à l'allure suivante :

![Menu](/assets/posts/neverForgetBirthday-notification.png)
 
Quand on clique dessus on arrive sur l'écran principal de l'application, avec la liste des anniversaires qui vont arriver.

*Contacts exclus :* permet de voir la liste des contacts exclus de l'application et des les remettre dans l'application.

![Liste exclusion](/assets/posts/neverForgetBirthday-excluded.png)
 
Pour remettre un contact dans l'application, il suffit de faire un clique long sur son nom et une popup vous demandera si vous voulez annuler l'exclusion

![Menu](/assets/posts/neverForgetBirthday-unexclude.png)
 
*Rafraîchir :* permet de forcer la mise à jour la liste des contacts manuellement

*A propos :* une petite note sur le logiciel et sa version

*Quitter :* pour quitter l'application mais le service continuera à tourner en arrière plan pour vous avertir des nouveaux anniversaires chaque jour.

# Droits demandés

- *RECEIVE_BOOT_COMPLETED* : Pour savoir quand le téléphone est démarré et lancer le service de vérification des anniversaires
- *READ_CONTACTS* : pour lire les données des contacts (nom, date anniversaires...) Notez bien que l'application n'a pas le droit de communiquer avec l'internet donc vos données restent sur votre téléphone
- *VIBRATE* : Pour pouvoir produire une mini vibration lors de l'ouverture du menu pour exclure ou remettre les contacts dans la liste. La notification ne produit ni bruit, ni vibration.

Nous avons fait le tour des fonctionnalités, il s'agit d'un programme assez simple et pourtant assez complet (liste d'exclusion, configuration de l'heure...). D'un point de vue technique elle est intéressante car elle permet d'utiliser activités, services, layouts, menus, ... tant de possibilités offertes par Android qui ne demandent qu'à être apprivoisées.

Si vous avez des questions, des idées d'amélioration je vous écoute !

 

 
 
 
 
 
 