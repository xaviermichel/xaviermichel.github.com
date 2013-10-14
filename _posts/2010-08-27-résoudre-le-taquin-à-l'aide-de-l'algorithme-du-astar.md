---
layout: post
category : algorithme
tagline: ""
tags : [taquin, A*, python, c++]
---
{% include JB/setup %}

Le taquin est un jeu qui nous taquine. Vous connaissez le principe j'en suis sûr, on retrouve ce problème dans plein de petits jeux stupides (par exemple dans "où est charlie" que j'ai téléchargé sur mon téléphone).

Voyons comment résoudre le taquin sans ce prendre la tête, avec l'aide de l'outil informatique et de l'algorithme A*.

*****

# Maquette

La complexité de la recherche étant énorme, nous avons besoin de performance. Qui dit performance dit c++... Cependant, il est bon de vérifier que l'algorithme fonctionne dans un premier temps. Nous n'avons cependant pas envie d'attaquer directement avec le c++ (choix des structures compliqué, syntaxe lourde...) ; nous allons donc commencer par vérifier la fonctionnalité de notre programme via un script python

    #!/bin/python
    #-*-coding:utf-8-*-
     
    import copy
     
    # donnees des noeuds
    F, G, H, JEU, PAPA = 0, 1, 2, 3, 4
     
    class Solveur :
        '''
        Le solveur de taquin
        '''
     
        def __init__(self, taquin) :
            self.openList = []
            self.closedList = []
     
            self.taquinInitial = taquin
            self.numeroCaseVide = taquin.numeroCaseVide
            self.taille = len(taquin.jeu)
     
            # on met l'etat initial
            self.openList.append([0, 0, 0, taquin.jeu, None])
     
     
        def resoudre(self) :
            print "Recherche de la solution..."
            while len(self.openList) > 0 :
     
                noeudCourant = self.openList.pop(0)
                taquin = noeudCourant[JEU]
     
                #self.closedList.append()
     
                if noeudCourant[JEU] == self.taquinInitial.jeuTrie :
                    return noeudCourant
     
                # ajout des possibilites ...
                for ligne in range(0, self.taille) :
                    for colonne in range(0, self.taille) :
     
                        if colonne > 0 and taquin[ligne][colonne-1] == self.numeroCaseVide :
                            tq = copy.deepcopy(taquin)
                            tq[ligne][colonne], tq[ligne][colonne-1] = tq[ligne][colonne-1], tq[ligne][colonne]
                            g = noeudCourant<strong>+1
                            h = self.heuristique(tq)
                            if not tq in self.closedList :
                                i = self.indiceDansListeOuverte(tq)
                                if i == -1 :
                                    self.openList.append([g+h, g, h, tq, noeudCourant])
                                elif g+h < self.openList<em>[F] :
                                    self.openList[i] = [g+h, g, h, tq, noeudCourant]
     
                        if colonne < self.taille - 1 and taquin[ligne][colonne+1] == self.numeroCaseVide :
                            tq = copy.deepcopy(taquin)
                            tq[ligne][colonne], tq[ligne][colonne+1] = tq[ligne][colonne+1], tq[ligne][colonne]
                            g = noeudCourant[G]+1
                            h = self.heuristique(tq)
                            if not tq in self.closedList :
                                i = self.indiceDansListeOuverte(tq)
                                if i == -1 :
                                    self.openList.append([g+h, g, h, tq, noeudCourant])
                                elif g+h < self.openList[i][F] :
                                    self.openList[i] = [g+h, g, h, tq, noeudCourant]
     
                        if ligne > 0 and taquin[ligne-1][colonne] == self.numeroCaseVide :
                            tq = copy.deepcopy(taquin)
                            tq[ligne][colonne], tq[ligne-1][colonne] = tq[ligne-1][colonne], tq[ligne][colonne]
                            g = noeudCourant[G]+1
                            h = self.heuristique(tq)
                            if not tq in self.closedList :
                                i = self.indiceDansListeOuverte(tq)
                                if i == -1 :
                                    self.openList.append([g+h, g, h, tq, noeudCourant])
                                elif g+h < self.openList[i][F] :
                                    self.openList[i] = [g+h, g, h, tq, noeudCourant]
     
                        if ligne < self.taille - 1 and taquin[ligne+1][colonne] == self.numeroCaseVide :
                            tq = copy.deepcopy(taquin)
                            tq[ligne][colonne], tq[ligne+1][colonne] = tq[ligne+1][colonne], tq[ligne][colonne]
                            g = noeudCourant[G]+1
                            h = self.heuristique(tq)
                            if not tq in self.closedList :
                                i = self.indiceDansListeOuverte(tq)
                                if i == -1 :
                                    self.openList.append([g+h, g, h, tq, noeudCourant])
                                elif g+h < self.openList[i][F] :
                                    self.openList[i] = [g+h, g, h, tq, noeudCourant]
     
                self.openList.sort()
     
            print None
     
     
        def indiceDansListeOuverte(self, jeu) :
            for i in range(0, len(self.openList)) :
                if self.openList[i][3] == jeu :
                    return i
            return -1
     
     
        def heuristique(self, taquin) :
            i = 0
            for ligne in range(0, self.taille) :
                for colonne in range(0, self.taille) :
                    if taquin[ligne][colonne] != self.taquinInitial.jeuTrie[ligne][colonne] :
                        i += 1
            return i
     
     
    class Taquin :
        '''
        Le jeu en lui meme
        '''
     
        def __init__(self, jeu = None) :
            if jeu != None :
                self.setJeu(jeu)
     
        def __repr__(self) :
            return 'n'.join( ''.join(str(i)) for i in self.jeu )
     
        def setJeu(self, jeu) :
            self.jeu = [ list(i) for i in jeu ]
            self.numeroCaseVide = len(self.jeu) - 1
     
            self.jeuTrie = [ [] for i in range(len(self.jeu)) ] # pour ne pas le recalculer a chaque fois
            for i in range(len(self.jeu)) :
                self.jeuTrie[i] = [ j for j in range(len(self.jeu)*i, len(self.jeu)*i + len(self.jeu)) ]
     
        def setNumeroCaseVide(self, i) :
            self.numeroCaseVide = i
     
        def printEtatFini(self) :
            print 'n' + 'n'.join( ''.join(str(i)) for i in self.jeuTrie )
     
        def termine(self) :
            return [ i for i in self.jeu ] == self.jeuTrie
     
        def resoudre(self) :
            solveur = Solveur(self)
            res = solveur.resoudre()
            if res == None :
                print "Pas de solution trouvee"
                return
            print "Solution trouvee"
     
            L = []
            while res[PAPA] != None :
                L.append(res[PAPA][JEU])
                res = res[PAPA]
            L.reverse()
     
            for elem in L :
                print 'n' + 'n'.join( ''.join(str(i)) for i in elem )
            self.printEtatFini()
     
    '''
    6 3 1
    8 0 2
    7 4 5
    '''
     
    if __name__ == "__main__" :
        tq = Taquin()
        tq.setJeu( [ [6, 3, 1], [8, 0, 2], [7, 4, 5] ] )
        tq.setNumeroCaseVide(2)
        tq.resoudre()
	
# Vers une recherche des performances

Le programme étant fonctionnel, nous le portons maintenant en c++. Moins facile et plus long à implémenter, mais avec des performances accrues.

## Le code

Pour l'heuristique, j'ai codé deux fonctions. On peut choisir celle que l'on souhaite utiliser en changeant la fonction pointé dans le constructeur de Solveur. Les fonctions calculant l'heuristique ne me satisfont et j'essaye d'en trouver une mieux Le programme n'est pas entièrement fini car il est prévu pour être multi-thread mais l'implémentation n'est pas encore faite (flemme) ; cependant les mutex sont déjà en place donc ça devrait aller assez vite.

    #include <iostream>
    #include <vector>
    #include <map>
     
    #include <SFML/System.hpp>
     
    typedef unsigned char uchar;
     
    /************************/
     
    const uchar nombre_de_lignes    = 3;
    const uchar nombre_de_colonnes  = 3;
     
    const uchar lignes[nombre_de_lignes][nombre_de_colonnes] = {
        { 6, 3, 1 },
        { 8, 0, 2 },
        { 7, 4, 5 }
    };
     
    const uchar numero_case_vide    = 8;
     
    /************************/
     
    typedef unsigned int uint;
    typedef std::vector< std::vector<uchar> > Jeu;
     
    struct Noeud {
        uint F, G, H;
        Jeu parent;
    };
     
    typedef std::map<Jeu, Noeud> Liste;
     
    inline void afficherJeu(Jeu &jeu) {
        std::cout << std::endl;
        for (uchar i = 0; i < nombre_de_lignes; ++i) {
            for (uchar j = 0; j < nombre_de_colonnes; ++j) {
                std::cout << (uint)jeu[i][j] << " ";
            }
            std::cout << std::endl;
        }
    }
     
    // distance de manathan entre deux points
    typedef std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<uchar, uchar> Point;
    inline uint distanceManhattan(const Point &p1, const Point &p2) {
        return (p1.first-p2.first)*(p1.first-p2.first) + (p1.second-p2.second)*(p1.second-p2.second);
    }
     
    inline Point localiserPositionFinale(uchar valeur, const Jeu &etatFini) {
        for ( uchar i = 0; i < nombre_de_lignes; ++i) {
            for ( uchar j = 0; j < nombre_de_colonnes; ++j ) {
                if ( etatFini[i][j] == valeur ) {
                    return Point(i, j);
                }
            }
        }
    }
     
    // une case mal placée = +1 d'heuristique
    inline uint heuristiqueSimple(const Jeu &jeu, const Jeu &etatFini) {
        uint k = 0;
        for ( uchar i = 0; i < nombre_de_lignes; ++i) {
            for ( uchar j = 0; j < nombre_de_colonnes; ++j ) {
                if ( jeu[i][j] != etatFini[i][j] ) {
                    ++k;
                }
            }
        }
        return k;
    }
     
    // une case mal placée = +distance de manathan à la destination d'heuristique
    inline uint heuristiqueManathan(const Jeu &jeu, const Jeu &etatFini) {
        uint k = 0;
        for ( uchar i = 0; i < nombre_de_lignes; ++i) {
            for ( uchar j = 0; j < nombre_de_colonnes; ++j ) {
                k += distanceManhattan( Point(i, j), localiserPositionFinale(jeu[i][j], etatFini) );
            }
        }
        return k;
    }
     
     
    class Solveur : public sf::Thread {
     
        public :
            Solveur() {    
                // heuristique choisie
                heuristique = &heuristiqueManathan;
     
                // initialiser le jeu
                for (uchar i = 0; i < nombre_de_lignes; ++i) {
                    m_jeu.push_back( std::vector<uchar>(nombre_de_colonnes) );
                    for (uchar j = 0; j < nombre_de_colonnes; ++j) {
                        m_jeu[i][j] = lignes[i][j];
                    }
                }
     
                // trouver quel est le jeu dans son etat fini, pour ne pas le rechercher a chaque fois
                for (uchar i=0; i < nombre_de_lignes; ++i) {
                    m_etatFini .push_back( std::vector<uchar>(nombre_de_colonnes) );
                    for (uchar j=0; j < nombre_de_colonnes; ++j) {
                        m_etatFini[i][j] = nombre_de_lignes*i+j;
                    }
                }
     
                // on met le premier noeud dans l'open list
                Noeud noeud;
                noeud.F = noeud.G = 0;
                noeud.H = (*heuristique)(m_jeu, m_etatFini);
                noeud.parent = m_jeu;
                openList[m_jeu] = noeud;
     
            }
     
            void Run() {
     
                Noeud noeud;
                Jeu jeu;
     
                bool okPourCeTour;
                uint nombre_tentative = 0;
     
                while ( ! openList.empty() ) {
     
                    mutex.Lock();
     
                    // recuperation du meilleur noeud
                    meilleurJeuListeOuverte(jeu);
                    noeud = openList[jeu];
     
                    // passage dans la liste fermee
                    closedList[jeu] = noeud;
     
                    // suppression de la liste ouverte
                    openList.erase(jeu);
     
                    mutex.Unlock();
     
                    if ( jeu == m_etatFini ) {
                        break;
                    }
     
                    // regarder les permutations possibles
                    okPourCeTour = false;
                    for (uchar i=0; i < nombre_de_lignes && ! okPourCeTour; ++i) {
                        for (uchar j=0; j < nombre_de_colonnes && ! okPourCeTour; ++j ) {
     
                            // on cherche la case vide
                            if ( jeu[i][j] != numero_case_vide ) {
                                continue;
                            }
     
                            // on a notre case vide
                            okPourCeTour = true;
                            ++nombre_tentative;
     
                            if ( j > 0 ) {
                                Jeu nJeu = jeu;
                                nJeu[i][j]   = jeu[i][j-1];
                                nJeu[i][j-1] = jeu[i][j];
                                Noeud nNoeud;
                                nNoeud.G = noeud.G + 1;
                                nNoeud.H = (*heuristique)(nJeu, m_etatFini);
                                nNoeud.F = nNoeud.G + nNoeud.H;
                                nNoeud.parent = jeu;
                                ajouterDansOpenList(nJeu, nNoeud, noeud);
                            }
     
                            if ( j < nombre_de_colonnes - 1 ) {
                                Jeu nJeu = jeu;
                                nJeu[i][j]   = jeu[i][j+1];
                                nJeu[i][j+1] = jeu[i][j];
                                Noeud nNoeud;
                                nNoeud.G = noeud.G + 1;
                                nNoeud.H = (*heuristique)(nJeu, m_etatFini);
                                nNoeud.F = nNoeud.G + nNoeud.H;
                                nNoeud.parent = jeu;
                                ajouterDansOpenList(nJeu, nNoeud, noeud);
                            }
     
                            if ( i > 0 ) {
                                Jeu nJeu = jeu;
                                nJeu[i][j]   = jeu[i-1][j];
                                nJeu[i-1][j] = jeu[i][j];
                                Noeud nNoeud;
                                nNoeud.G = noeud.G + 1;
                                nNoeud.H = (*heuristique)(nJeu, m_etatFini);
                                nNoeud.F = nNoeud.G + nNoeud.H;
                                nNoeud.parent = jeu;
                                ajouterDansOpenList(nJeu, nNoeud, noeud);
                            }
     
                            if ( i < nombre_de_lignes - 1 ) {
                                Jeu nJeu = jeu;
                                nJeu[i][j]   = jeu[i+1][j];
                                nJeu[i+1][j] = jeu[i][j];
                                Noeud nNoeud;
                                nNoeud.G = noeud.G + 1;
                                nNoeud.H = (*heuristique)(nJeu, m_etatFini);
                                nNoeud.F = nNoeud.G + nNoeud.H;
                                nNoeud.parent = jeu;
                                ajouterDansOpenList(nJeu, nNoeud, noeud);
                            }
     
                        }
                    }
                }
     
                if ( jeu != m_etatFini ) {
                    std::cout << "pas de solution" << std::endl;
                    return;
                }
                std::cout << "solution trouvée en " << nombre_tentative << " tentatives" << std::endl;
     
                std::vector<Jeu> vj;
                vj.push_back(jeu);
                while ( noeud.parent != m_jeu ) {
                    jeu = noeud.parent;
                    vj.push_back( jeu );
                    noeud = closedList[jeu];
                }
                std::cout << "solution en " << vj.size() << " coups :" << std::endl;
     
                afficherJeu(m_jeu);
                for ( std::vector<Jeu>::iterator it = --vj.end(); it != vj.begin(); --it) {
                    afficherJeu(*it);
                }
                afficherJeu(m_etatFini);
            }
     
        protected :
     
            inline void ajouterDansOpenList(const Jeu &jeu, const Noeud &nouveauNoeud, const Noeud &noeudRemplace) {
                mutex.Lock();
                if ( openList.find(jeu) == openList.end() ) { // pas encore dans la liste ouverte, donc on l'ajoute
                    openList[jeu] = nouveauNoeud;
                } else { // déjà dans la liste ouverte, avons nous trouvé un meilleur chemin ?
                    if ( nouveauNoeud.F < noeudRemplace.F ) {
                       openList[jeu] = nouveauNoeud;
                    }
                }
                mutex.Unlock();
            }
     
            inline void meilleurJeuListeOuverte(Jeu &retour) {
                retour = openList.begin()->first;
                uint plusBasCout = openList.begin()->second.F;
     
                for ( Liste::iterator it = ++openList.begin(); it != openList.end(); ++it) {
                    if ( it->second.F < plusBasCout ) {
                        retour = it->first;
                        plusBasCout = it->second.F;
                    }
                }
            }
     
            // attributs
            Liste openList;
            Liste closedList;
     
            Jeu m_jeu;
            Jeu m_etatFini;
     
            uint (*heuristique)(const Jeu&, const Jeu&);
     
            sf::Mutex mutex;
    };
     
     
     
    int main(void) {
        Solveur solveur;
        solveur.Launch();
     
        solveur.Wait();
        return 0;
    }


# Le test

Voici la résolution proposé pour la taquin en entrée :

    Démarrage de /home/xavier/foo/prog/py-build-desktop/py...
    solution trouvée en 58 tentatives
    solution en 11 coups :
     
    6 3 1 
    8 0 2 
    7 4 5 
     
    8 3 1 
    6 0 2 
    7 4 5 
     
    3 8 1 
    6 0 2 
    7 4 5 
     
    3 0 1 
    6 8 2 
    7 4 5 
     
    3 0 1 
    6 4 2 
    7 8 5 
     
    3 0 1 
    6 4 2 
    8 7 5 
     
    3 0 1 
    8 4 2 
    6 7 5 
     
    8 0 1 
    3 4 2 
    6 7 5 
     
    0 8 1 
    3 4 2 
    6 7 5 
     
    0 1 8 
    3 4 2 
    6 7 5 
     
    0 1 2 
    3 4 8 
    6 7 5 
     
    0 1 2 
    3 4 5 
    6 7 8 
    /home/xavier/foo/prog/py-build-desktop/py s'est terminé avec le code 0

Il nous a trouvé une solution. Mais, parce qu'il y a toujours un mais, il semblerais que le programme peine a trouver la solution dans certains cas (ce qui n'est pas terrible pour un simple taquin de 3x3) ; il reste donc pas mal de boulot. En réalité, il affiche "j'ai trouvé une solution " mais il n'arrive pas à remonter le "chemin" du A*. A voir...

# Annexe, comparaison des performances

Notre implémentation c++ est-elle réellement plus rapide que celle en python ? Question légitime ! Pour y répondre, faisons simplement appel à la fonction time :

    python taquin.py  6,36s user 0,00s system 99% cpu 6,371 total]

    ./prog_cpp  0,01s user 0,00s system 80% cpu 0,010 total
		
Je vous laisse deviner qui a gagné au niveau performances, les chiffres parlent d'eux même ;)

***
>*Migration commentaires :*
>
>>yabo84, le 16/07/2011 à 00:19
>>Bsr en copilant sous DevC++ votre code en écrit en C++, j'obtien les resultat suvant:
>>
>>
>>\Users\FAUBA\Desktop\taquin.cpp C:\Users\FAUBA\Desktop\C SFML/System.hpp: No such file or directory. 
>>45 C:\Users\FAUBA\Desktop\taquin.cpp invalid function declaration 
>>45:22 C:\Users\FAUBA\Desktop\taquin.cpp incomplete universal character name \U 
>>45:22 C:\Users\FAUBA\Desktop\taquin.cpp [Warning] unknown escape sequence '\F' 
>>45:22 C:\Users\FAUBA\Desktop\taquin.cpp [Warning] unknown escape sequence '\D' 
>>46 C:\Users\FAUBA\Desktop\taquin.cpp expected `,' or `...' before '&' token 
>>46 C:\Users\FAUBA\Desktop\taquin.cpp ISO C++ forbids declaration of `Point' with no type 
>>C:\Users\FAUBA\Desktop\taquin.cpp In function `uint distanceManhattan(int)': 
>>47 C:\Users\FAUBA\Desktop\taquin.cpp `p1' undeclared (first use this function) 
>>(Each undeclared identifier is reported only once for each function it appears in.) 
>>47 C:\Users\FAUBA\Desktop\taquin.cpp `p2' undeclared (first use this function) 
>>47 C:\Users\FAUBA\Desktop\taquin.cpp At global scope: 
>>50 C:\Users\FAUBA\Desktop\taquin.cpp `Point' does not name a type 
>>C:\Users\FAUBA\Desktop\taquin.cpp In function `uint heuristiqueManathan(const Jeu&, const Jeu&wink': 
>>78 C:\Users\FAUBA\Desktop\taquin.cpp `Point' undeclared (first use this function) 
>>78 C:\Users\FAUBA\Desktop\taquin.cpp `localiserPositionFinale' undeclared (first use this function) 
>>78 C:\Users\FAUBA\Desktop\taquin.cpp At global scope: 
>>85 C:\Users\FAUBA\Desktop\taquin.cpp `sf' has not been declared 
>>85 C:\Users\FAUBA\Desktop\taquin.cpp expected class-name before '{' token 
>>268 C:\Users\FAUBA\Desktop\taquin.cpp `sf' has not been declared 
>>268 C:\Users\FAUBA\Desktop\taquin.cpp ISO C++ forbids declaration of `Mutex' with no type 
>>
>>268 C:\Users\FAUBA\Desktop\taquin.cpp expected `;' before "mutex" 
>>C:\Users\FAUBA\Desktop\taquin.cpp In member function `void Solveur::Run()': 
>>127 C:\Users\FAUBA\Desktop\taquin.cpp `mutex' undeclared (first use this function) 
>>C:\Users\FAUBA\Desktop\taquin.cpp In member function `void Solveur::ajouterDansOpenList(const Jeu&, const Noeud&, const Noeud&wink': 
>>236 C:\Users\FAUBA\Desktop\taquin.cpp `mutex' undeclared (first use this function) 
>>C:\Users\FAUBA\Desktop\taquin.cpp In function `int main()': 
>>275 C:\Users\FAUBA\Desktop\taquin.cpp 'class Solveur' has no member named 'Launch' 
>>277 C:\Users\FAUBA\Desktop\taquin.cpp 'class Solveur' has no member named 'Wait' 
>>
>>
>>Alors cmt faire? coup de main[title]
>>[/*] [*]
>>[/title]
>
> ***
>
>>Xavier, le 16/07/2011 à 14:37
>>Code
>>\Users\FAUBA\Desktop\taquin.cpp C:\Users\FAUBA\Desktop\C SFML/System.hpp: No such file or directory. 
>>
>>J'ai utilisé le module système de la [SFML](http://www.sfml-dev.org/index-fr.php) pour threader le logiciel, il faut donc l'installer pour compiler le code.  wink
>>
>>PS : [comment installer la SFML](http://www.sfml-dev.org/tutorials/1.6/start-cb-fr.php)
>
> ***
>
>>yabo84, le 19/07/2011 à 15:04
>>bonsoir merrci pourla reponse je essayer avec votre chemin.
>>Pourrais-je avoir la version delphi ou la version C++ avec interface graphique car je suis un peu debutant en c++. merci d'avance
>
> ***
>
>>Xavier, le 19/07/2011 à 15:51
>>Désolé, bien que je code pour le plaisir je ne fais pas de bénévolat
>>
>>La version c++ est fonctionnelle, il ne vous reste qu'à ajouter une interface, ce qui devrait aller assez vite, mais qui ne présente que peu d’intérêt...
>
> ***
>
>>yoga, le 12/08/2013 à 17:08
>>Bonjour,j'ai compilé ce code et il exploite tous les ressources de ma mémoire vive. Ma machine plante. Pouvez-vous me décrire quel est ce problème?
***


