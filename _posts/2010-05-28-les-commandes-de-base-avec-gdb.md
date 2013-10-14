---
layout: post
category : premiers pas
tagline: ""
tags : [débogueur, gdb]
---
{% include JB/setup %}

GDB est LA référence des débogueurs. Je préfère l'utiliser avec une interface graphique mais on en a pas toujours une sous la main, d'où l'utilité de savoir s'en servir un minimum en console.

Cet article est donc une introduction à l'utilisation de gdb en console.

*****

Commençons par écrire un programme avec une jolie erreur, on fera semblant de ne pas savoir où elle se trouve :

    #include "stdio.h"
     
    int main() {
     
    	int a, b, c;
     
    	a = 3;
    	b = 4;
    	++a;
    	a += 2;
     
    	b = b/a;
    	c = a/b;
     
    	printf("c'est tout bon !");
     
    	return 0;
    }
	
Maintenant, pour utiliser efficacement gdb, nous devons compiler le programme avec l'option *-g* :

    gcc -g a.c
	
Maintenant lançons gdb avec en argument notre exécutable :

    gdb a.out
	
Pour avoir un aperçu du fichier, utilisons la commande *l (list)* :

    (gdb) l
    1	#include "stdio.h"
    2	
    3	int main() {
    4	
    5		int a, b, c;
    6	
    7		a = 3;
    8		b = 4;
    9		++a;
    10		a += 2;
	
Comme on ne sait pas où le programme bug, posons un break point à la ligne 7.
	
    (gdb) b 7
    Breakpoint 1 at 0x80483ed: file a.c, line 7.
	
Lançons l'exécution du programme :

    (gdb) r
	
Là nous pouvons profiter des avantages du debugeur, par exemple pour afficher le contenu des variables via la commande *p (print)*:
	
    (gdb) p a
    $1 = 2637812
    (gdb) p b
    $2 = 134513755
    (gdb) p c
    $3 = 1171648
	
Passons à l'instruction suivante avec la commande *n (next)*, puis vérifions que a à bien pris la valeur 3.

    (gdb) n
    8		b = 4;
    (gdb) p a
    $4 = 3
    (gdb) 

Tout fonctionne comme prévu B-)

Enfin utilisons la commande *c (continue)* pour laisser le programme s'exécuter jusqu'au plantage.
	
    (gdb) c
    Continuing.
     
    Program received signal SIGFPE, Arithmetic exception.
    0x08048421 in main () at a.c:13
    13		c = a/b; 
	
Voici donc la source d'erreur, on peut afficher la valeur de la variable b, on constate qu'elle vaut 0... Division par 0 donc.

