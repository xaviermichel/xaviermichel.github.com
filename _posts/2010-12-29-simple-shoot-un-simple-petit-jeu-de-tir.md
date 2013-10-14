---
layout: post
category : projet
tagline: ""
tags : [javascript, sommaire]
---
{% include JB/setup %}

Simple shoot est un simple jeu de tir. Vous incarnez un personnage qui se défend face à l'assaut d'ennemis de plus en plus nombreux et de plus en plus féroces.

*****

# Notes

Auteurs :
- Xavier MICHEL
- Game design by Daniel Cook ([Lostgarden.com](http://www.lostgarden.com/))
- Nombreuse images/animations reprisent du projet [bamboclash](http://www.bigz.fr/bamboclash/) (auquel j'ai activement participé)

Version actuelle :
- Version 0.2 en date du 29/12/2010

# Meilleurs scores

Les scores sont désactivés (simple shoot n'est plus soutenu pour l'instant, suite à un changement de serveur), voici néanmoins le top 5 (merci à tous les autres joueurs qui ont essayé ce jeu) :
1. Totorvic	237.9
2. Warr	153.26
3. nexus	131.18
4. saffir	123.77
5. Charlelie	118.27

# Quel est le but du jeu ?

Simple shoot est un simple jeu de tir dans lequel vous incarnez un petit personnage qui va subir des assauts de la part de plus en plus d'ennemis, qui tireront de plus en plus vite.

Sur une carte, les missiles peuvent passer sur l'eau mais vous ne pouvez pas la traverser. Les missiles explosent quand ils touchent un ennemi ou s'ils atteignent le bord de la carte, l'explosion ne provoque pas de dégats.

Vous ne pouvez pas être tué par vos propres missiles, mais les missile d'une IA peuvent tuer une autre IA.

J'estime la durée d'une partie à environ deux minutes, mais le jeu commence à devenir difficile au bout de 80 secondes.

L'IA apparait aléatoirement dans l'un des quatre angles de la carte. Plus le jeu dur longtemps, plus l'IA apparaitra vite et plus elle sera puissante.

# Quelques captures d'écran et une vidéo

## Captures d'écran

![Une roquette qui va être difficile à éviter](/assets/posts/simple-shoot-1.png)
![Une lutte sans merci](/assets/posts/simple-shoot-2.png)
![Une gestion des meilleurs scores en ligne](/assets/posts/simple-shoot-3.png)

## Vidéo

[Une partie simple en vidéo !](/assets/posts/simple-shoot-1.webm)


# Comment jouer ?

Ce jeu étant assez simple, les touches qui vous permettent de jouer le sont aussi.
- *Z, Q, S, D* Vous serviront à vous déplacer dans le monde
- *R* Redémarrer la partie
- *Clique gauche souris* Pour tirer un projectile dans la direction dans laquelle vous avez cliqué
- *Molette souris* Pour changer d'arme
- *M (mute)* Permet de mettre le jeu en muet, ou de remettre le son

Si vous le souhaité, vous pouvez télécharger le jeu via les liens suivant :
- Télécharger [Simple Shoot pour windows](/assets/posts/simple-shoot-windows.zip)
- Télécharger [Simple Shoot pour linux](/assets/posts/simple-shoot-linux.zip) (vous devez avoir la SFML >= 1.6 pour exécuter)
Si vous êtes sous mac et que vous voulez tester, vous n'avez qu'à compiler les sources
- Télécharger [les sources Simple Shoot](/assets/posts/simple-shoot-sources.zip)

# Présentation technique du projet

Je vous invite à générer la documentation *doxygen* du projet !



