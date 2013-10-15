---
layout: post
category : projet
tagline: ""
tags : [ged]
---
{% include JB/setup %}

Simple GED est un logiciel de gestion électronique de document à destination des particuliers, via une gestion simplifiée.

Ce logiciel est open source et il a pour but d'évoluer pour répondre à tous les besoins des utilisateurs, à vous de me suggérer des évolutions ! 

*****

# Présentation du projet

## Présentation de la GED

La GED est amplement présentée sur [wikipédia](http://fr.wikipedia.org/wiki/Gestion_%C3%A9lectronique_des_documents).

Bien sûr, notre logiciel ne vise pas une utilisation professionnelle mais plutôt personnelle avec une gestion simplifié, assez personnalisable et qui devrait encore s'améliorer dans le futur. Le mot d'ordre, c'est une GED pour une utilisation en tant que particulier. Les GED d'entreprise dont très complètes, mais trop complexe pour un usage maison. Le but était donc de simplifier !

On trouve notamment sur notre logiciel la possibilité de scanner un document, ou de l'ajouter directement via l'explorateur. Notons que tous les types de documents peuvent être sauvegarder (pdf, doc...). Grâce une recherche via le moteur de recherche, retrouvez votre document presque instantannément !

## Pourquoi ce projet ?

De plus en plus on vous demande la copie de tel ou tel document, où l'ais-je rangé déjà celui là ? Afin d'éviter ce genre de soucie, un stockage sur votre ordinateur de ce document de façon très simple et vous avez la possibilité de le retrouver en quelques cliques !

# Notre logiciel

## Les fonctionnalités de notre système de GED

Notre système propose donc les fonctionnalités suivantes :
- Possibilité de créer des gros groupes de documents (par exemple factures, scolaire...). Ces groupes sont appelés classeurs.
- On peut scanner, puis stocker un document en lui associant un nom, une description, un groupe de document, une date et une série de mots clef ou entrer un fichier sur le disque dur dans la base directement
- Un moteur de recherche puissant qui va chercher la correspondance dans le document (texte, word, ...) même !
- Un moteur de plugin, qui permet de facilement ajouter des fonctionnalités dans se plonger au coeur du code source.

## Stratégie

A l'heure où le cloud computing se développe, je préfère avoir mes documents en local car ce sont des données personnelles. C'est pour cette raison que simple GED garde vos documents en local uniquement.

Dans le futur, et cela fait partie des évolutions possibles, rien ne garantie qu'un sytème de sauvegarde sur un serveur personnel ne sera pas envisagé...

# Quelques captures d'écran

Voici enfin quelques aperçus des différents écrans. _Attention : ces images ne sont pas forcément celles de la dernière version !_
Vous pouvez bien entendu les agrandir moyennant un coûteux petit clique sur l'une des images.

![Accueil](/assets/posts/simple-ged-home.png)

![Parcours de documents](/assets/posts/simple-ged-browse_doc.png)

![Gestion de plugins](/assets/posts/simple-ged-plugins.png)

![Ajout de documents](/assets/posts/simple-ged-add_doc.png)

![Recherche](/assets/posts/simple-ged-quick_search.png)

# Téléchargement et installation

[Voir la page du wiki](https://github.com/xaviermichel/simple-ged/wiki/Comment-installer-simple-ged).

En résumé, on [télécharge simple GED](http://sourceforge.net/projects/simpleged/files/latest/download) ici puis on extrait le zip téléchargé et on lance "simple_ged.jar".

La mise à jour d'une version à une autre est automatique, il suffit d'accepter quand le logiciel vous propose de faire la mise à jour.

Si vous rencontrer une difficulté ou si vous voulez proposer une fonctionnalité, n'hésitez pas à me contacter via les commentaires.

Ce logiciel est open source, vous pouvez récupérer les sources sur notre [dépôt GIT](https://github.com/xaviermichel/simple-ged).

*Vos propositions ou améliorations sont les bienvenues.*

***
>*Migration commentaires :*
>
>>mcrehange, le 12/10/2012 à 08:41
>>Bonjour,
>>Je suis à la recherche d'un logiciel de GED, afin d'y adosser une application de gestion d'archives privées (PHP, MySql ou PostGreSql)
>>Un contact pourrait être établi pour mesurer la faisabilité du projet.
>>Cordialement
>>Marc Créhange
>
> ***
>
>>Xavier, le 12/10/2012 à 10:57
>>Bonjour,
>>
>>Le problème est que le logiciel manque encore de fonctionnalités, il est très basique. C'est à la fois une force dans la simplicité d'utilisation, mais aussi une limite dans l'évolution et le déploiement.
>>
>>C'est pour cela que je suis en train de réécrire une partie de celui-ci, vous pouvez suivre l'avancement sur le nouveau dépôt. Soyez indulgent, je travaille seul dessus (venez m'aider, faites des pull request ! smile ) et pas à plein temps. Je mettrai à jour l'article quand j'aurai de nouvelles choses à montrer !  wink
>>
>>Bref, l'idée pour moi c'est vraiment de consolider les bases avant qu'il ne soit utilisable dans un cadre industriel.
>>
>>@mcrehange : je vous fais une réponse plus complète par courriel.
>
> ***
>
>>duffie34, le 19/12/2012 à 14:37
>>Bonjour,
>>
>>Intéressé par votre appli mais là pas trouvé la manière de l'installer. J'ai un peu honte sur le coup mais cela m'ennuie de ne pas pouvoir la tester.
>>
>>Merci de votre aide.
>
> ***
>
>>Xavier, le 19/12/2012 à 14:46
>>Bonjour,
>>
>>Merci pour l'intérêt que vous portez à cette application.
>>
>>Pour l'installation, il suffit d'extraire le zip et de double cliquer sur "simple_ged.jar". Il n'y a pas d'installation à proprement parler, il s'agit directement du lanceur de l'application. Il faut bien entendu que java soit installé sur votre ordinateur.
>>
>>La version actuellement téléchargeable sur cette page n'est pas très à jour. J'ai réécris la partie interface utilisateur afin qu'elle soit plus simple à prendre en main et plus ergonomique. Je n'ai pas encore finalisé cette tâche et donc pas encore de téléchargements à vous proposer mais vous pouvez trouvez quelques captures d'écran de la prochaine mise à jour en avant première.
>>
>>Xavier
>
> ***
>
>>fely, le 27/12/2012 à 22:18
>>Bonjour,
>>
>>J'ai pas encore eu le temps de tester ce programme mais en tout cas je suis intéressé par cette application. je voulais juste t'encourager dans la démarche car c'est vraiment utile. je ne manquerai pas de te faire un retour dès que possible. encore merci.
>>
>>Fely
>
> ***
>
>>Xavier, le 30/12/2012 à 00:07
>>Bonjour,
>>
>>Merci pour ce message d'encouragement  grin
>>
>>Cela me motive à terminer la version 4 qui est la réécriture de l'interface graphique, une version qui apporte beaucoup du point de vue utilisateur. J’espère la finaliser courant janvier, ensuite, le système de mise à jour devrait faire son travail  smile
>>
>>Xavier
>
> ***
>
>>Xavier, le 11/01/2013 à 19:09
>>Bonjour,
>>
>>Je viens de mettre en ligne la nouvelle version (4.0). Elle nécessite la dernière version de java. Si vous avez une version précédente de simple ged, la mise à jour devrait vous être proposée. Sinon, vous pouvez la télécharger de puis le lien donné dans la section téléchargements ci-dessus.
>>
>>Notez qu'au premier démarrage, il vous sera demandé de re-entrer vos informations concernant la localisation de votre bibliothèque de documents. Les informations sur vos documents ne sont cependant pas perdues  wink.
>>
>>Si vous utilisez le plugin "orange", notez également qu'il est nécessaire de faire la mise à jour de ce plugin. Vous le trouverez également dans la section téléchargement ci-dessus (je sais que l'installation et la mise à jour de plugin reste un peu compliquée, mais comme il n'y a qu'un plugin de disponible (et fait fait par moi) ce n'est pas une priorité).
>>
>>Xavier
>
> ***
>
>>Jonathan, le 29/01/2013 à 16:11
>>Bonjour , 
>>
>>Question peut être bête , mais sous quel os peut on le tester ??
>>
>>Merci d avance
>
> ***
>
>>SadE, le 29/01/2013 à 17:30
>>Hello,
>>
>>Tout d'abord bravo pour cette initiative et ce bon début smile Il y a une bonne base pour faire de belles choses smile 
>>Vu l'objectif du logiciel, destiné à une utilisation pour la maison, voila ce que je suggère pour améliorations pour le futur :
>>- Tout d'abord une gestion des mots clés (indépendante du contenur de la description) couplé à un moteur de recherche
>>- Pouvoir importer les documents d'un répertoire dans une liste tampon qui nous permet ensuite de les classer avec les bon mots clés / vers classeurs désirés. C'est beaucoup plus pratique que faire document par document.
>>- Cryptage des documents 
>>- Comment le logiciel gére la charge avec des milliers de documents dans sa base ?
>>- Les documents sont ils directement insérés dans la base ou seulement leur lien vers leur chemin sur le disque ?
>>
>>Encore bravo pour la version 4 !
>>
>>Bien Cordialement,
>>
>>Yann
>
> ***
>
>>Xavier, le 29/01/2013 à 19:47
>>Bonjour,
>>
>>@Jonathan, comme c'est en java c'est multi-platefome grin ... Du moins en théorie  oh oh car il est vrai que tout est testé sous windows mais que le scanner par exemple n'est pas utilisable sous linux (je n'ai pas trouvé de solution idéale...).
>>
>>@SadE, merci pour ce retour très encourageant grin Je vais essayer de faire évoluer le logiciel régulièrement. Pour répondre plus précisément à tes questions :
>>- Une gestion des mots clés comme tu la propose est une bonne idée, je le mets dans ma TODO liste  tongue rolleye
>>- Pouvoir importer les documents via une liste tampon... Ce serait le rôle d'un plugin dit "worker", la possibilité de produire des plugins plus poussés est prévu dans la prochaine version.
>>- Cryptage des documents : pas à l’ordre du jour, comme les documents sont stockés en local uniquement ça n'a pas d'intérêt. Quand on pourra synchroniser ses données sur un serveur distant, cela aura du sens.
>>Enfin, les documents sont insérés dans la base avec leur chemin sur le disque (enfin c'est un peu plus subtile car ils doivent se trouver dans le répertoire de la GED, sinon ils sont copiés dans ce dernier).
>>
>>Pour terminer, je lance un nouvel appel au(x) développeur(s) motivé(s) qui voudrai(en)t m'aider  smirk, n'hésitez pas à forker mon repo github où à me contacter !
>>
>>Xavier
>
> ***
>
>>Bonjour monsieur Xavier, 
>>
>>Tout d'abord je tiens de vous féliciter pour le travail énorme que vous avez accompli, et je vous remercie de partager avec nous cette application GED, 
>>
>>bref, je suis Imad Eddine, et je suis un étudiant en informatique, je débute avec le langage JAVA, 
>>Le prof nous a demandé de développer une application GED en Java, 
>>
>>Et quand j'ai téléchargé le code source de votre application format Zip a partir du site : 
>>https://github.com/xaviermichel/simple-ged 
>>
>>J'ai trouvé beaucoup de Packages, j'ai pensé que votre projet se trouve dans : simple ged core,  excaim
>>mais j'ai reçu plusieurs erreurs, e j'ai pas pu l’exécuter,  red face
>>
>>Une autre question peut être bête , avec quelle bibliothèque vous avez réalisé l'interface : swing ou swt ....  question
>>
>>
>>Cordialement
>>
>>Imad Eddine
>
> ***
>
>>Xavier, le 31/01/2013 à 10:39
>>Bonjour Imad,
>>
>>Effectivement le core est le module "coeur" du projet mais il dépend des autres modules. Pour construire le projet dans sa globalité, je vous conseil fortement d'utiliser maven. Pour les instructions de constructions, vous pouvez les trouver sur la page de démarrage du wiki.
>>
>>Enfin, l'interface est réalisée à l'aide de javafx.
>>
>>Je reste à votre disposition si vous n'arrivez pas à builder le projet (normalement ça fonctionne selon travis).
>>
>>Xavier
>
> ***
>
>>Dany, le 21/02/2013 à 16:19
>>Bonjour,
>>
>>bravo pour GED si ... simple (c'est rare).
>>
>>petit question sous quel licence est elle publié ??? est il prévue quelques améliorations, ou pas ... comme par exemple : lors d'une recherche, ont trouve ce que l'ont voulais, ont efface le mot recherche la GED ne reviens pas avec une vue de l'arborescence générale.
>>
>>En tout cas en bravo
>
> ***
>
>>Bonjour,
>>
>>Tout d'abord merci pour ce message.
>>
>>Cette GED est publiée sous license zlib. Il est en effet prévu quelques améliorations, je vous laisse jeter un oeil aux tickets qui sont ouverts. 
>>
>>Le bug d'effacement de la recherche a déjà été relevé et a été corrigé. Le correctif sera déployé dans la prochaine version. Il faudrait d'ailleurs que je package une prochaine version. Prochaine version qui arrivera je ne sais pas quand... Je pense que je vais revoir mes objectifs de contenu à la baisse car je n'y arriverai jamais tout seul raspberry .
>>
>>Encore merci
>>
>>Xavier
>
> ***
>
>>Bonjour,
>>Merci pour cette initiative très utile… et très rare. j'ai cherché une solution de GED domestique, simple, ne tournant pas sur le modèle client-serveur (destiné aux entreprises, et trop usine à gaz pour un usage perso).
>>Mon PC tourne sous Linux 64 bits, sous Archlinux.
>>J'ai la version 7 de Java d'installée.
>>Je n'arrive pas à exécuter simple_ged. Je lance :
>>Code
>>java -jar simple_ged.jar
>>et j'obtiens une Exception Error :
>>Code
>>Exception in thread "main" java.lang.RuntimeException: java.lang.UnsatisfiedLinkError: Can't load library: /home/chris/Downloads/simple_ged/amd64/libglass.so 
>>Ci-dessous le compte-rendu détaillé (trop long pour ne pas polluer ce commentaire) :
>>http://pastebin.com/zhVW1i1L
>>Pour être plus précis sur la version de java que j'utilise :
>>Code
>>jre7-openjdk
>>Version 7.u13_2.3.7-2
>>Free Java environment based on OpenJDK 7.0 with IcedTea7 replacing binary plugs - Full Java runtime environment - needed for executing Java GUI and Webstart programs
>>J'ai déjà pas mal d'applis java qui tournent avec succès, comme :
>>aptana-studio freeguide freemind geogebra gpsprune jedit jpdftweak mytourbook sweethome3d tvbrowser
>>
>>Merci beaucoup pour votre aide.
>>PS :
>>La seule autre appli "GED perso" que j'ai trouvée, est une appli sous Python (donc aussi multi-plateforme) MALODOS :
>>https://sites.google.com/site/malodospage/home
>
> ***
>
>>Xavier, le 18/03/2013 à 10:28
>>Bonjour,
>>
>>Merci pour ce message smile
>>
>>Pour l'erreur, je dirais qu'il y a de fortes chances que ce soit lié à javafx. Je ne peux pas plus vous aider car j'ai déjà rencontré des difficultés sous winows... Java 8 devrait comporté javafx et résoudre ces problèmes exotiques.
>>
>>Sinon elle a l'air pas mal cette autre application de GED, ya de bonnes fonctionnalités smile
>>
>>Xavier
>
> ***
>
>>TANGUY, le 18/04/2013 à 11:06
>>BONJOUR 
>>j'ai un probleme lors de l'installation de simple ged au fait lorsque je decompresse l'archive 
>>le fichier jar affiche sous forme de document winrar j'ai windows 7 ,jai installer la derniere version de java mais rien
>
> ***
>
>>Virginie, le 29/04/2013 à 10:36
>>Bonjour, 
>>totalement novice dans ce domaine, j'ai téléchargé votre GED mais impossible de l'ouvrir !!! Quelqu'un peut il m'expliquer les démarches svp? 
>>D'avance MERCI ! 
>>Virginie
>
> ***
>
>>Mohammed amine, le 02/05/2013 à 00:28
>>Bonsoir,
>>votre logiciel est tres interessant merci pour vos efforts.
>>Je suis entain de developper un systeme de gestion electronique des documents qui est on JEE (hibenate et spring) 
>>le probleme que j'ai c'est qu'il manque la lecture des documents office (word,exel...) dans le navigateur.
>>vous pouvez m'aider et merci smile
>
> ***
>
>>Xavier, le 26/05/2013 à 11:23
>>Bonjour,
>>
>>Merci à tous pour vos messages encourageants.
>>
>>J'ai créé une page wiki afin de résumer les étapes d'installation.
>>
>>@Mohammed, je n'ai pas encore commencé à regarder cet aspect et je ne peux donc pas te conseiller à ce sujet.
>>
>>J'en profite pour relancer mon appel à l'aide si vous voulez m'aider à faire évoluer le projet  smile
>>
>>Xavier
>
> ***
>
>>neko, le 26/05/2013 à 17:25
>>Bonjour, et bravo pour votre programme,
>>
>>Je compte l'employer à titre personnel, mais aussi pour l'association que je gère, ce qui me sera d'un secours immense.
>>
>>A t-on la possibilité d'envoyer des fichier directement depuis SG ? je pensais naïvement que l'icone "enveloppe" était destiné à cet usage... mais non !
>>
>>Quand à l'archivage sur support Cloud, il suffit d'installer Dropbox en local, et le tour est joué !
>>
>>Merci pour votre réponse
>>
>>TC
>
> ***
>
>>Xavier, le 27/05/2013 à 10:04
>>Bonjour,
>>
>>Non, il n'y a pas de fonctionnalité de partage direct. L'enveloppe sert en fait à afficher les messages du logiciel (mise à jour, lancement de plugins...).
>>
>>Pour le cloud en effet, soit une solution privée comme celle là, soit via un logiciel qui fait des backups sur un serveur perso  wink
>>
>>Xavier
>
> ***
>
>>YVES, le 11/09/2013 à 11:57
>>La version 4.3 lancée sous windows vista, j'obtiens "a java Exception has occured" et le logiciel ne s'ouvre pas.
>>
>>Y a-t-il quelque chose à faire de manière spécifique pour obtenir un bon fonctionnement.
>>
>>Merci
>>
>>Cordialement
>>
>>Y. StG
>
> ***
>
>>charly, le 18/09/2013 à 17:17
>>bonjour,
>>
>>cela correspond exactement à ce que je cherchais
>>après installation cela semblait fonctionner
>>mais après mise à jour de JAVA impossible de lancer le programme (Windows XP)
>>quelle version de JAVA faut-il ?
>>
>>merci
>
> ***
>
>>Xavier, le 18/09/2013 à 19:40
>>Bonjour,
>>
>>Le logiciel est prévu pour java 7 au minimum. La dernière version devrait donc faire l'affaire.
>>
>>Xavier
>
> ***
>
>>CHARLY, le 19/09/2013 à 14:26
>>impossible à lancer, dommage !
>>
>>cordialemnt
>
> ***
>
>>Xavier, le 19/09/2013 à 14:34
>>Etrange... Mais vous n'êtes pas le premier à me remonter cela ! 
>>
>>A tout hasard, et si vous le souhaitez, pourriez vous à l'occasion m'envoyer le fichier de logs ("simple_ged.log", qui doit être dans le répertoire d’exécution) pour que je vois si une erreur apparaît ? Il s'agit d'un fichier texte avec des informations concernant l'execution.
>>
>>Vous pouvez me l'envoyer par message privé <xavier.michel.mx440@gmail.com> si cela vous convient.
>>
>>Xavier
>
> ***
>
>>math007, le 27/09/2013 à 19:05
>>Bonjour,
>>
>>Je rencontre moi aussi des problème de lancement. Le fichier de log ne trace rien par contre, si on lance l'application via l'invite de commande, on obtient l'erreur suivante :
>>
>>http://img11.hostingpics.net/thumbs/mini_816664erreursimpleged.png
>>
>>Configuration de la machine : 
>>Windows 7 x64
>>Java JDK 7u40 x64
>
> ***
>
>>Xavier, le 30/09/2013 à 16:21
>>Bonjour,
>>
>>Merci pour cette capture d'écran smile 
>>
>>Après quelques recherches, j'ai trouvé d'autres cas où ce problème de démarrage de javaFX est recensé, par exemple : Why is my Java Desktop Application Failing to Run?.
>>
>>JavaFX 2.2 est pourtant inclus dans le JDK 7 donc devrait être présent... Ce qu'ils proposent sur stack overflow comme solution est d'ajouter les DLL au classpath. Je pense qu'il faudrait tenter cette solution. Mais pour cela il faudrait que j'arrive à reproduire le bug chez moi.
>>
>>Xavier
>
> ***
>
>>feufeu31, le 09/10/2013 à 11:51
>>bonjour j utilise voitre GED pour mes documents perso . quelques plantages au depart mais rien de grave 
>>
>>je voulais changer les icones de classeur mai je n'y suis pas arrivé.
>>
>>pouvez vous m'aider
>>
>>cordialement
>>
>>feufeu31
***



