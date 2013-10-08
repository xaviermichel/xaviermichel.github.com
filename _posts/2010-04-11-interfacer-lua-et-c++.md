---
layout: post
category : lessons
tagline: ""
tags : [scripting, lua, c++, luna]
---
{% include JB/setup %}

Comme je n'ai pas trouvé de documents assez simple à comprendre sur le net, je vais ici détailler comment on peut utiliser lua comme langage de script associé à C++.

# Un première classe de base

Pour commencer, créons une classe simple (Foo pour être original ;) ) qui va posséder un attribut toto (un entier) et quelques méthodes simples (getter, setter, et incrémentation).

Cette classe est très simple car c'est juste pour l'exemple.

    // Foo.cpp
    #ifndef H_FOO
    #define H_FOO

    class Foo {

    	public :

    		Foo() : toto() {
    		}

    		Foo(int t) : toto(t) {
    		}

    		~Foo() {
    		}

    		int getToto() {
    			return toto;
    		}

	    	void setToto(int t) {
    			toto = t;
    		}

    		void incremente() {
    			++toto;
    		}

    	private :
    		int toto;

    };
    #endif

# Une classe de liaison

Nous allons maintenant mettre en place une classe qui va faire la liaison entre le c++ et lua. Nous appellerons cette classe LuaFoo pour ne pas nous perdre.

