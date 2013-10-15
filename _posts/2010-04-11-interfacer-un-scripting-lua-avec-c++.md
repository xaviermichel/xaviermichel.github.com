---
layout: post
category : premiers pas
tagline: ""
tags : [scripting, lua, c++, luna]
---
{% include JB/setup %}

Comme je n'ai pas trouvé de documents assez simple à comprendre sur le net, je vais ici détailler comment on peut utiliser lua comme langage de script associé à C++.

*****

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

Pour les includes, on inclut la classe que l'on a créé précédemment, je mettrai luna.h à la fin de cette page mais on peut le trouver [ici](http://lua-users.org/wiki/SimplerCppBinding).

    // LuaFoo.cpp
    #ifndef H_LUAFOO
    #define H_LUAFOO
     
    extern "C" {
    #include <lua/lua.h>
    #include <lua/lauxlib.h>
    #include <lua/lualib.h>
    }
     
    #include "luna.h"
    #include "Foo.cpp"
     
    class LuaFoo {
     
    	public :
    		static const char className[];
    		static Luna<LuaFoo>::RegType methods[];
     
    		LuaFoo(lua_State *L) {
    			objetReel = (Foo*)lua_touserdata(L, 1);
    		}
     
    		~LuaFoo() {
    		}
     
    		void setObject(lua_State *L){
    			objetReel = (Foo*)lua_touserdata(L, 1);
    		}
     
    		int setToto(lua_State *L){
    			objetReel->setToto((int)luaL_checknumber(L, 1));
    			return 0; // return 0 signifie que l'on a mis 0 arguments sur la pile
    		}
     
    		int getToto(lua_State *L){
    			lua_pushnumber(L, objetReel->getToto());
    			return 0;
    		}
     
    		int incremente(lua_State *L) {
    			objetReel->incremente();
    			return 0;
    		}
     
    	private :
    		Foo *objetReel;
     
    };
     
    #endif


# Un petit main

Maintenant créons le main du programme, on inclut les déclarations des classes, on fait pas mal de bordel pour faire que lua sache quoi appeler... On lance lua. Je créé une instance de Foo et je met l'objet en commun entre le programme et le script (qui n'est pas encore créé).

Le script à effectuer sera passé en premier argument au programme.


    // main.cpp
    #include <iostream>
    using namespace std;
     
    #include "Foo.cpp"
    #include "LuaFoo.cpp"
     
    /*
    Dans l'idéal, on placera ces lignes en haut de LuaFoo.cpp
    (comme la déclaration de la classe sera dans le .hpp)
    */
    const char LuaFoo::className[] = "LuaFoo";
     
    #define method(class, name) {#name, &class::name}
    Luna<LuaFoo>::RegType LuaFoo::methods[] = {
    	method(LuaFoo, setToto),
    	method(LuaFoo, getToto),
    	method(LuaFoo, incremente),
    	{0,0}
    };
    /*
     
    */
     
    int main(int argc, char *argv[]){
     
    	// Initialisation, on charge les bibliothèques
    		// qui seront utilisées par lua
    	lua_State *L = lua_open();
    	luaopen_base(L);
    	luaopen_table(L);
    	luaopen_io(L);
    	luaopen_string(L);
    	luaopen_math(L);
    	luaopen_debug(L);
     
    	// On enregistre le type d'objet auprès de lua
    		// chaque type d'objet enregistré dans être enregistré !
    	Luna<LuaFoo>::Register(L);
     
    		// création de l'objet de test
    	Foo temp(9);
     
    		// on passe l'objet de test à LUA
    	lua_pushlightuserdata(L, (void*)&temp);
    	// Et on lui donne un petit nom, qui nous servira pour
    		// son utilisation coté LUA
    	lua_setglobal(L, "fooGlobal");
     
    	cout << "Avant toto = " << temp.getToto() << endl;
    	luaL_loadfile(L, argv[1]);
    	lua_pcall(L, 0, 0, 0);
    	cout << "Après toto = " << temp.getToto() << endl;
     
    	lua_close(L);
    	return 0;
    }


Bon aller hop on compile tout ça :


    xavier@xavier-desktop:~/foo/lunar/ppp% g++ -o test *.cpp -llua -llualib


# On écrit le script lua


    // test.lua
    --[[
    Xavier MICHEL
    Test Scripting lua-c++
    --]]
     
    -- récupération de l'objet global
    monObjet = LuaFoo(fooGlobal)
     
    -- Appel de méthodes de l'objet
    monObjet:setToto(18)
    monObjet:incremente()


# Test du programme


    xavier@xavier-desktop:~/foo/lunar/ppp% ./test test.lua                 
    Avant toto = 9
    Après toto = 19


Tout fonctionne, ce monde n'est-il pas merveilleux :D ?

# Annexe : luna.h

Ce script a été pris [ici](http://lua-users.org/wiki/SimplerCppBinding).


    extern "C" {
    #include <lua/lua.h>
    #include <lua/lauxlib.h>
    }
     
    template <typename T> class Luna {
      typedef struct { T *pT; } userdataType;
    public:
      typedef int (T::*mfp)(lua_State *L);
      typedef struct { const char *name; mfp mfunc; } RegType;
     
      static void Register(lua_State *L) {
    	lua_newtable(L);
    	int methods = lua_gettop(L);
     
    	luaL_newmetatable(L, T::className);
    	int metatable = lua_gettop(L);
     
    	// store method table in globals so that
    	// scripts can add functions written in Lua.
    	lua_pushstring(L, T::className);
    	lua_pushvalue(L, methods);
    	lua_settable(L, LUA_GLOBALSINDEX);
     
    	lua_pushliteral(L, "__metatable");
    	lua_pushvalue(L, methods);
    	lua_settable(L, metatable);  // hide metatable from Lua getmetatable()
     
    	lua_pushliteral(L, "__index");
    	lua_pushvalue(L, methods);
    	lua_settable(L, metatable);
     
    	lua_pushliteral(L, "__tostring");
    	lua_pushcfunction(L, tostring_T);
    	lua_settable(L, metatable);
     
    	lua_pushliteral(L, "__gc");
    	lua_pushcfunction(L, gc_T);
    	lua_settable(L, metatable);
     
    	lua_newtable(L);                // mt for method table
    	int mt = lua_gettop(L);
    	lua_pushliteral(L, "__call");
    	lua_pushcfunction(L, new_T);
    	lua_pushliteral(L, "new");
    	lua_pushvalue(L, -2);           // dup new_T function
    	lua_settable(L, methods);       // add new_T to method table
    	lua_settable(L, mt);            // mt.__call = new_T
    	lua_setmetatable(L, methods);
     
    	// fill method table with methods from class T
    	for (RegType *l = T::methods; l->name; l++) {
    	/* edited by Snaily: shouldn't it be const RegType *l ... ? */
    	  lua_pushstring(L, l->name);
    	  lua_pushlightuserdata(L, (void*)l);
    	  lua_pushcclosure(L, thunk, 1);
    	  lua_settable(L, methods);
    	}
     
    	lua_pop(L, 2);  // drop metatable and method table
      }
     
      // get userdata from Lua stack and return pointer to T object
      static T *check(lua_State *L, int narg) {
    	userdataType *ud =
    	  static_cast<userdataType*>(luaL_checkudata(L, narg, T::className));
    	if(!ud) luaL_typerror(L, narg, T::className);
    	return ud->pT;  // pointer to T object
      }
     
    private:
      Luna();  // hide default constructor
     
      // member function dispatcher
      static int thunk(lua_State *L) {
    	// stack has userdata, followed by method args
    	T *obj = check(L, 1);  // get 'self', or if you prefer, 'this'
    	lua_remove(L, 1);  // remove self so member function args start at index 1
    	// get member function from upvalue
    	RegType *l = static_cast<RegType*>(lua_touserdata(L, lua_upvalueindex(1)));
    	return (obj->*(l->mfunc))(L);  // call member function
      }
     
      // create a new T object and
      // push onto the Lua stack a userdata containing a pointer to T object
      static int new_T(lua_State *L) {
    	lua_remove(L, 1);   // use classname:new(), instead of classname.new()
    	T *obj = new T(L);  // call constructor for T objects
    	userdataType *ud =
    	  static_cast<userdataType*>(lua_newuserdata(L, sizeof(userdataType)));
    	ud->pT = obj;  // store pointer to object in userdata
    	luaL_getmetatable(L, T::className);  // lookup metatable in Lua registry
    	lua_setmetatable(L, -2);
    	return 1;  // userdata containing pointer to T object
      }
     
      // garbage collection metamethod
      static int gc_T(lua_State *L) {
    	userdataType *ud = static_cast<userdataType*>(lua_touserdata(L, 1));
    	T *obj = ud->pT;
    	delete obj;  // call destructor for T objects
    	return 0;
      }
     
      static int tostring_T (lua_State *L) {
    	char buff[32];
    	userdataType *ud = static_cast<userdataType*>(lua_touserdata(L, 1));
    	T *obj = ud->pT;
    	sprintf(buff, "%p", obj);
    	lua_pushfstring(L, "%s (%s)", T::className, buff);
    	return 1;
      }
    };

