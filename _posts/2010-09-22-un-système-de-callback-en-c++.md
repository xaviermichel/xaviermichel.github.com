---
layout: post
category : algorithme
tagline: ""
tags : [c++, callback]
---
{% include JB/setup %}

Comment coder un bon callback en c++ ? 

Pas si évident... les approches au premier instinct ne sont pas les plus simples. Par exemple ici on va tenter de simuler un déclenchement d’évènement lorsque l'on clique sur un bouton.

*****

# Métode naïve

Dans cette première partie, la méthode comme elle nous vient au premier abord.

## Première idée

En partant sur le modèle à la Qt, on se dit qu'il nous faut bien évidemment une super classe dont toutes les autres, ici on l’appellera Widget. 

Pour la callback, un simple pointeur de fonction qui va pointer sur une méthode de l'appelant.

    class Widget { 
    };
     
    class MonBouton {
     
    	public :
    		MonBouton(Widget *papa) : papa(papa) {
    		}
     
    		void setCallback(void (Widget::*f)()) {
    			myCallback = f;
    		}
     
    		void triggerCallback() { // il faudrait le déclencher sur un clicked par exemple
    			(papa->*myCallback)();
    		}
     
    	protected :
    		void (Widget::*myCallback)();
     
    		Widget *papa;
    };
     
     
    class MaFenetre : public Widget { // hérite de écran par exemple
     
    	public :
    		MaFenetre() {
    			MonBouton *btn  = new MonBouton(this);
    			btn->setCallback(&MaFenetre::btn_clicked);
    		}
     
    		void btn_clicked() {
    			std::cout << "bouton cliqué !" << std::endl;
    		}
    };
	
Bon aller, on se tente une compilation : 

    call2.cpp: In constructor 'MaFenetre::MaFenetre()':
    call2.cpp:35: error: no matching function for call to 'MonBouton::setCallback(void (MaFenetre::*)())'
    call2.cpp:13: note: candidates are: void MonBouton::setCallback(void (Widget::*)())

Inutile d'essayer de corriger, il apparaît que le compilateur à raison, alors on va essayer de modifier au minimum le code pour le rendre fonctionnel, template ?

## Avec un template

On va juste passer le type de papa en template :

On peut tout à fait conserver l'héritage de Widget, mais il devient inutile.

    #include <iostream>
     
    template <class T>
    class MonBouton {
     
    	public :
    		MonBouton(T *papa) : papa(papa) {
    		}
     
    		void setCallback(void (T::*f)()) {
    			myCallback = f;
    		}
     
    		void triggerCallback() { // se déclenche sur un clique par exemple 
    			(papa->*myCallback)();
    		}
     
    	protected :
    		void (T::*myCallback)();
     
    		T *papa;
    };
     
     
    class MaFenetre { // hérite de écran par exemple
     
    	public :
    		MaFenetre() {
    			MonBouton<MaFenetre> *btn  = new MonBouton<MaFenetre>(this);
    			btn->setCallback(&MaFenetre::btn_clicked);
    		}
     
    		void btn_clicked() {
    			std::cout << "bouton cliqué !" << std::endl;
    		}
    };
     
     
    int main(void) {
    	MaFenetre f;
    	return 0;
    }

L'avantage de cette méthode, qui est fonctionnelle, c'est qu'elle reste indépendante de boost ; par contre la syntaxe lors de la création du bouton est assez lourde >:(. Et voilà transition pour la partie 3, la méthode bien avec boost !

## Avec boost

    #include <iostream>
     
    #include <boost/function.hpp>
    #include <boost/bind.hpp>
     
    class Widget {
    };
     
    class MonBouton : public Widget {
     
    	public :
    		MonBouton(Widget *papa) : papa(papa) {
    		}
     
    		void setCallback(boost::function<void()> f) {
    			myCallback = f;
    		}
     
    		void triggerCallback() { // il faudrait le déclencher sur un clicked par exemple
    			myCallback();
    		}
     
    	public :
    		boost::function<void()> myCallback;
     
    		Widget *papa;
    };
     
    class MaFenetre : public Widget { // hérite de écran par exemple
     
    	public :
    		MaFenetre() : nombre(9) {
    			MonBouton *btn = new MonBouton(this);
    			btn->setCallback( boost::bind(&MaFenetre::btn_clicked, boost::ref(this)) );
     
                            // test
    			std::cout << nombre << std::endl;
    			btn->triggerCallback();	// ça marche !
    			std::cout << nombre << std::endl;
    		}
     
     
    		void btn_clicked() {
    			++nombre;
    			std::cout << "bouton cliqué !" << std::endl;
    		}
     
    	protected :
    		int nombre;
    };
     
     
    int main(void) {
    	MaFenetre f;
    	return 0;
    }
	
	
Cette méthode est pratique, mais pour l'instant on ne peut pas lier l’évènement à n'importe quelle méthode, il faudrait quelque chose de plus générique ... A méditer :)

## Simplifions nous la vie grâce aux macros
	
    // méthode slot à la Qt =)
    #define CALL(call) boost::bind(&call,boost::ref(this))
     
    // et l'appel
    btn->setCallback( CALL(MaFenetre::btn_clicked) );

	
# Méthode avancée : type erasure

Bon, on a le choix entre une syntaxe lourde ou une dépendance avec boost... Et comme on est des fous, 
on va tout refaire sans template direct. Avec [type erasure](http://alp.developpez.com/tutoriels/type-erasure/).
Une fois l'article lu, ça a l'air tellement simple en fait !

    #include <iostream>
     
    class Widget {
    };
     
     
    class Callback {
    	public :
    		virtual ~Callback() {
    		}
    		virtual void triggerCallback(Widget*) {
    		}
    };
     
     
    template<class T>
    class CallbackT : public Callback {
    	public :
    		CallbackT( void(T::*f)() ): f(f) {
    		}
    		void triggerCallback(Widget *w) {
    			(static_cast<T*>(w)->*f)();
    		}	
     
    	protected :
    		void (T::*f)();
    };
     
     
    class MonBouton : public Widget {
     
    	public :
    		MonBouton(Widget *papa) : papa(papa) {
    		}
     
    		template<class CLASS>
    		void setCallback( void(CLASS::*f)() ) {
    			myCallback = new CallbackT<CLASS>(f);
    		}
     
    		void triggerCallback() { // se déclenche sur un clique par exemple 
    			myCallback->triggerCallback(papa);	
    		}
     
    	protected :
    		Widget *papa;
    		Callback *myCallback;
     
    };
     
     
    class MaFenetre : public Widget { // hérite de écran par exemple
     
    	public :
    		MaFenetre() {
    			MonBouton *btn  = new MonBouton(this);
    			btn->setCallback(&MaFenetre::btn_clicked);
     
    			// simulation du clique
    			btn->triggerCallback();
    		}
     
    		void btn_clicked() {
    			std::cout << "bouton cliqué !" << std::endl;
    		}
    };
     
     
    int main(void) {
    	MaFenetre f;
    	return 0;
    }


Et voilà :D

Bon on remarquera que :

    btn->setCallback(&MaFenetre::btn_clicked);
	
C'est un peu lourd comme syntaxe, peut-être y a-t-il moyen de se simplifier la vie grâce aux macro ? A voir ;)

***
>*Migration commentaires :*
>
>>flow, le 10/10/2012 à 15:14
>>Merci pour ce tutorial fort clair et précis, il m'aura beaucoup aidé.
>
***

