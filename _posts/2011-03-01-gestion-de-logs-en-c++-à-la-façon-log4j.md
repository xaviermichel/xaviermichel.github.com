---
layout: post
category : projet
tagline: ""
tags : [c++, logs, log4j]
---
{% include JB/setup %}

Souvent, pour vérifier qu'un programme fonctionne bien on utilise à la fois un débogueur, mais également les flux de sortie pour afficher. 

La fonction assert nous permet de repérer les problèmes, mais on apprécie d'avoir à disposition tout un log qui déroule le programme, donc on préfère écrire sur la sortie standard.

Il est souvent fastidieux de trouver tous les cout que l'on a fait dans un programme pour les supprimer (question de performance et de propreté). 

Une classe de gestion des logs serait donc très pratique. Elle serait utilisée lors du développement mais disparaîtrait du binaire final.

*****

# Réflexion et cahier des charges

## Le but recherché

Mon but était d'obtenir une classe souple, qui disparaîtrait entièrement sur demande.

Il est intéressant de pouvoir afficher les logs sur la sortie standard, mais on apprécie également de pouvoir les consulter dans un fichier texte où il est plus facile de naviguer.

Pour filtrer les logs, le système de log4j est intéressant. Il définit différents niveaux de log, de verbeux à erreur.

## Problème technique

Le problème est que l'on veut que la classe log disparaisse quand on le souhaite, donc il faut une version fake que le compilateur supprimera.

On peut afficher directement les objets, et pour des raisons pratiques j'ai choisi de pouvoir spécialiser le template d'ajout de ligne dans Log.inl.

# La classe

Vous pouvez directement [télécharger la classe de log](/assets/posts/logs.tar.gz).


    //
    // Copyright (C) 2011 Xavier MICHEL (xavier.michel.mx440@gmail.com)
    //
    // This software is provided as-is, without any express or implied warranty.
    // In no event will the authors be held liable for any damages arising from the use of this software.
    //
    // Permission is granted to anyone to use this software for any purpose,
    // including commercial applications, and to alter it and redistribute it freely,
    // subject to the following restrictions:
    //
    // 1. The origin of this software must not be misrepresented;
    //    you must not claim that you wrote the original software.
    //    If you use this software in a product, an acknowledgment
    //    in the product documentation would be appreciated but is not required.
    //
    // 2. Altered source versions must be plainly marked as such,
    //    and must not be misrepresented as being the original software.
    //
    // 3. This notice may not be removed or altered from any source distribution.
    //
     
    #ifndef LOG_MANAGER_HPP
    #define LOG_MANAGER_HPP
     
    #include <iostream>
    #include <cstring>
    #include <sstream>
     
    #include <map>
    #include <set>
    #include <list>
    #include <vector>
    #include <deque>
     
     
    // comment next line if you don't wanna see log
    #define SHOW_LOG
     
     
     
    /*!
        \version 0.2
     
        \class Log
        Gestion des logs (en fait cette classe pourrait s'appeler LogManager, mais ce serait trop long à écrire pour saisir un log)
     
        Si le flag SHOW_LOG est défini, les logs sont traités sinon ils sont ignorés et ne seront pas dans le binaire final.
     
        De la même manière que pour les logs d'Andoid, il existe différents niveaux de log :
        - VERBOSE : v(tag)
        - DEBUG   : d(tag)
        - INFO    : i(tag)
        - WARNING : w(tag)
        - ERROR   : e(tag)
     
        Bien sûr la description des niveaux de log n'est pas objective, elle correspond plus à ce que moi je mettrais !
        ---
        - verbose : Salut ! Je suis le logiciel et j'ai décidé de faire ça, ça et ça ! C'est bien hein ? Bon je suis le niveau le plus bas alors j'affiche plein d'info sur le déroulement du programme pas très utiles.
        - debug   : A peine au dessus du verbose, j'essaye de filtrer un peu les données utiles en affichant des données à l'utilisateur par exemple "Tiens, tu savais que j'avais trouvé n items dans le xml ?"
        - info    : Je dirais que c'est pour indiquer que l'utilisateur essaye de se servir du logiciel, genre "arf, il tente un drag... c'est pas prévu, ça va bugger ou pas ! On verra bien après tout"
        - warning : Là pour moi, c'est quand le programme balance des exceptions, il affiche un warning au passage, exemple : "Objet sélectionné incorrect, normalement c'est prévu mais attention"
        - error   : Le summum, aucune excuse, la terrible segfault à tous les coups ! Exemple : "La factory ne connais pas cet argument, soyez sûr qu'elle va renvoyer n'importe quoi et priez !"
     
        On peut définir le niveau désiré via void setLogLevel(LogLevel).
     
        On peut définir le flux de sortie via void setOutput(std::ostream &stream).
     
        Exemple d'utilisation rapide :
     
        \verbatim
        int main() {
     
            std::ofstream file("logs.txt");
            Log::setOutput(file);   // écrire les messages de log dans un fichier
     
            Log::setOutput(std::cout); // revenir sur la sortie standard
     
            Log::setLogLevel(DEBUG);   // niveau minium des logs à afficher
     
            Log::v("main") << "Welcome in my testing prog";
     
            bool b = false;
            Log::i("main") << "Demain il " << 9 << " fera pas beau : " << b;    // voir spécialisation template bool (log.inl)
     
            Foo f;
            Log::d("foo") << f; // en supposant que foo redéfinisse l'opérateur << pour un ostream
     
            return 0;
        }
        \endverbatim
    */
     
     
    enum LogLevel {
        VERBOSE,
        DEBUG,
        INFO,
        WARNING,
        ERROR
    };
     
     
    class Log {
     
    public :
     
    #ifndef SHOW_LOG
     
        class FakeDebug;
     
        /*!
            \brief Nouvelle ligne de log en verbose, version fake
        */
        static inline FakeDebug v(const std::string & = "") {
            return FakeDebug();
        }
     
        /*!
            \brief Nouvelle ligne de log en debug, version fake
        */
        static inline FakeDebug d(const std::string & = "") {
            return FakeDebug();
        }
     
        /*!
            \brief Nouvelle ligne de log en information, version fake
        */
        static inline FakeDebug i(const std::string & = "") {
            return FakeDebug();
        }
     
        /*!
            \brief Nouvelle ligne de log en warning, version fake
        */
        static inline FakeDebug w(const std::string & = "") {
            return FakeDebug();
        }
     
        /*!
            \brief Nouvelle ligne de log en error, version fake
        */
        static inline FakeDebug e(const std::string & = "") {
            return FakeDebug();
        }
     
     
        /*!
            \brief Défini le niveau de sortie désirés, version fake
        */
        static inline void setLogLevel(LogLevel) {
        }
     
        /*!
            \brief Défini le flux de sortie, version fake
        */
        static inline void setOutput(const std::ostream &) {
        }
     
     
        /*!
            \class FakeDebug
            Cette classe sera effacée par le compilateur, elle sert quand il ne faut pas traiter le débugage
        */
        class FakeDebug
        {
            public:
     
                /*!
                    Un traitement bien inutile ...
                */
                template<class T>
                inline FakeDebug& operator<<(const T &) {
                    return *this;
                }
     
        };
     
     
    #
    #else // On doit traiter les logs
    #
     
        class LogLine;
     
        /*!
            \brief Nouvelle ligne de log en verbose
        */
        static inline LogLine v(const std::string &tag = "") {
            return LogLine(VERBOSE, tag);
        }
     
        /*!
            \brief Nouvelle ligne de log en debug
        */
        static inline LogLine d(const std::string &tag = "") {
            return LogLine(DEBUG, tag);
        }
     
        /*!
            \brief Nouvelle ligne de log en information
        */
        static inline LogLine i(const std::string &tag = "") {
            return LogLine(INFO, tag);
        }
     
        /*!
            \brief Nouvelle ligne de log en warning
        */
        static inline LogLine w(const std::string &tag = "") {
            return LogLine(WARNING, tag);
        }
     
        /*!
            \brief Nouvelle ligne de log en error
        */
        static inline LogLine e(const std::string &tag = "") {
            return LogLine(ERROR, tag);
        }
     
     
        /*!
            \brief Défini le niveau de sortie désirés
        */
        static void setLogLevel(LogLevel l);
     
        /*!
            \brief Défini le flux de sortie
        */
        static void setOutput(std::ostream &stream);
     
     
        /*!
            \class LogLine
            Une ligne à écrire dans les logs
        */
        class LogLine {
     
            public:
     
                /*!
                    \brief Construit une ligne de log de niveau l avec pour tag tag
                */
                inline LogLine(LogLevel l, const std::string &tag)
                    : m_logLevel(l), m_tag(tag), m_message(new std::ostringstream()) {
                }
     
                /*!
                    \brief A sa destruction, la ligne est envoyée au logManager pour être traitée (affichée)
                */
                inline ~LogLine() {
                    Log::writeLine(m_logLevel, m_tag, m_message->str());
                    delete m_message;
                }
     
                /*!
                    \brief On veut écrire quelque chose dans les logs
                    \attention Ce template est spécialisé / spécialisable dans log.inl
                */
                template<class T>
                inline LogLine& operator<<(const T &something) {
                    *m_message << something;
                    return addSpace();
                }
     
                /*!
                    \brief La spécialisation pour std::pair, définie dans log.inl
                */
                template<class T1, class T2>
                inline LogLine& operator<<(const std::pair<T1, T2> &pair);
     
                /*!
                    \brief La spécialisation pour std::map, définie dans log.inl
                */
                template<class T1, class T2>
                inline LogLine& operator<<(const std::map<T1, T2> &map);
     
                /*!
                    \brief La spécialisation pour std::set, définie dans log.inl
                */
                template<class T>
                inline LogLine& operator<<(const std::set<T> &set);
     
                /*!
                    \brief La spécialisation pour std::list, définie dans log.inl
                */
                template<class T>
                inline LogLine& operator<<(const std::list<T> &list);
     
                /*!
                    \brief La spécialisation pour std::vector, définie dans log.inl
                */
                template<class T>
                inline LogLine& operator<<(const std::vector<T> &vector);
     
                /*!
                    \brief La spécialisation pour std::deque, définie dans log.inl
                */
                template<class T>
                inline LogLine& operator<<(const std::deque<T> &deque);
     
                /*!
                    \brief La spécialisation pour std::multimap, définie dans log.inl
                */
                template<class T1, class T2>
                inline LogLine& operator<<(const std::multimap<T1, T2> &multimap);
     
                /*!
                    \brief La spécialisation pour std::multiset, définie dans log.inl
                */
                template<class T>
                inline LogLine& operator<<(const std::multiset<T> &multiset);
     
     
             private :
     
                 /*!
                    \brief Ajoute un espace en fin de ligne
                 */
                 inline LogLine& addSpace() {
                     *m_message << " ";
                     return *this;
                 }
     
     
                 LogLevel m_logLevel;               //!< Le niveau de log de cette ligne
     
                 std::string m_tag;                 //!< Le tag associé à cette ligne
     
                 std::ostringstream *m_message;     //!< Le flux dans lequel on écrit la ligne
     
         };
     
     
     
    private :
     
        Log();
     
        /*!
            \brief écris une ligne dans le log
        */
        static void writeLine(
                LogLevel level,
                const std::string &tag,
                const std::string &message
             );
     
     
     
        static Log m_log;                               //!< instance unique de Log (LogManager)
     
        std::map<LogLevel, std::string> m_logLabels;    //!< Les niveaux de log avec le message correspondant
     
        LogLevel m_logLevel;                            //!< Le niveau de log actuel
     
        std::ostream *m_ostream;                        //!< Le flux de sortie actuel
     
     
    #endif // SHOW_LOG
     
     
    };  // fin de la classe
     
     
    #ifdef SHOW_LOG
    #include "Log.inl"  // spécialisation template
    #endif // SHOW_LOG
     
     
    #endif // LOG_MANAGER_HPP


## Log.inl

    //
    // Copyright (C) 2011 Xavier MICHEL (xavier.michel.mx440@gmail.com)
    //
    // This software is provided as-is, without any express or implied warranty.
    // In no event will the authors be held liable for any damages arising from the use of this software.
    //
    // Permission is granted to anyone to use this software for any purpose,
    // including commercial applications, and to alter it and redistribute it freely,
    // subject to the following restrictions:
    //
    // 1. The origin of this software must not be misrepresented;
    //    you must not claim that you wrote the original software.
    //    If you use this software in a product, an acknowledgment
    //    in the product documentation would be appreciated but is not required.
    //
    // 2. Altered source versions must be plainly marked as such,
    //    and must not be misrepresented as being the original software.
    //
    // 3. This notice may not be removed or altered from any source distribution.
    //
     
     
     
    // spécialisation template << sur ajout de ligne, ainsi vous pouvez vous faire plaisir et ajouter vos types =)
    // bien sûr si vous modifiez ce fichier, en accord avec la licence, sachez que vos changements m'intéresse !
     
     
    // spécialisation pour booléen
    template<>
    inline Log::LogLine& Log::LogLine::operator<< <bool>(const bool &b) {
        *m_message << (b ? "true" : "false");
        return addSpace();
    }
     
     
    // spécialisation pour std::string
    template<>
    inline Log::LogLine& Log::LogLine::operator<< <std::string>(const std::string &s) {
        *m_message << '"' << s << '"';
        return addSpace();
    }
     
     
    // spécialisation pour std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air
    template<class T1, class T2>
    inline Log::LogLine& Log::LogLine::operator<<(const std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<T1, T2> &pair) {
        *this << "pair (" << pair.first << "," << pair.second << ")";
        return addSpace();
    }
     
     
    // spécialisation pour std::map
    template<class T1, class T2>
    inline Log::LogLine& Log::LogLine::operator<<(const std::map<T1, T2> &map) {
        *m_message << "map [ ";
        bool first = true;
        for (typename std::map<T1, T2>::const_iterator it = map.begin(); it != map.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << "(" << it->first << "," << it->second << ")";
        }
        *m_message << "]";
        return addSpace();
    }
     
     
    // spécialisation pour std::set
    template<class T>
    inline Log::LogLine& Log::LogLine::operator<<(const std::set<T> &set) {
        *m_message << "set { ";
        bool first = true;
        for (typename std::set<T>::const_iterator it = set.begin(); it != set.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << *it;
        }
        *m_message << "}";
        return addSpace();
    }
     
     
    // spécialisation pour std::list
    template<class T>
    inline Log::LogLine& Log::LogLine::operator<<(const std::list<T> &list) {
        *m_message << "list [ ";
        bool first = true;
        for (typename std::list<T>::const_iterator it = list.begin(); it != list.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << *it;
        }
        *m_message << "]";
        return addSpace();
    }
     
     
    // spécialisation pour std::vector
    template<class T>
    inline Log::LogLine& Log::LogLine::operator<<(const std::vector<T> &vector) {
        *m_message << "vector [ ";
        bool first = true;
        for (typename std::vector<T>::const_iterator it = vector.begin(); it != vector.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << *it;
        }
        *m_message << "]";
        return addSpace();
    }
     
     
    // spécialisation pour std::deque
    template<class T>
    inline Log::LogLine& Log::LogLine::operator<<(const std::deque<T> &deque) {
        *m_message << "deque [ ";
        bool first = true;
        for (typename std::deque<T>::const_iterator it = deque.begin(); it != deque.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << *it;
        }
        *m_message << "]";
        return addSpace();
    }
     
     
    // spécialisation pour std::multimap
    template<class T1, class T2>
    inline Log::LogLine& Log::LogLine::operator<<(const std::multimap<T1, T2> &multimap) {
        *m_message << "multimap [ ";
        bool first = true;
        for (typename std::multimap<T1, T2>::const_iterator it = multimap.begin(); it != multimap.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << "(" << it->first << "," << it->second << ")";
        }
        *m_message << "]";
        return addSpace();
    }
     
     
    // spécialisation pour std::multiset
    template<class T>
    inline Log::LogLine& Log::LogLine::operator<<(const std::multiset<T> &multiset) {
        *m_message << "multiset { ";
        bool first = true;
        for (typename std::multiset<T>::const_iterator it = multiset.begin(); it != multiset.end(); ++it) {
            if ( ! first ) {
                *m_message << ", ";
            } else {
                first = false;
            }
            *this << *it;
        }
        *m_message << "}";
        return addSpace();
    }
     
     
     
     
    // spécialisations spéciales Qt, à voir à l'usage ...
    #ifdef QT_VERSION
     
    class QString;
     
    // spécialisation pour QString
    template<>
    inline Log::LogLine& Log::LogLine::operator<< <QString>(const QString &s) {
        *m_message << '"' << s.toStdString() << '"';
        return addSpace();
    }
     
    #endif

## Log.cpp

    //
    // Copyright (C) 2011 Xavier MICHEL (xavier.michel.mx440@gmail.com)
    //
    // This software is provided as-is, without any express or implied warranty.
    // In no event will the authors be held liable for any damages arising from the use of this software.
    //
    // Permission is granted to anyone to use this software for any purpose,
    // including commercial applications, and to alter it and redistribute it freely,
    // subject to the following restrictions:
    //
    // 1. The origin of this software must not be misrepresented;
    //    you must not claim that you wrote the original software.
    //    If you use this software in a product, an acknowledgment
    //    in the product documentation would be appreciated but is not required.
    //
    // 2. Altered source versions must be plainly marked as such,
    //    and must not be misrepresented as being the original software.
    //
    // 3. This notice may not be removed or altered from any source distribution.
    //
     
    #include "Log.hpp"
     
     
    #ifdef SHOW_LOG
     
    Log::Log Log::m_log;
     
     
    Log::Log()
        : m_logLevel(VERBOSE), m_ostream(&std::cout)
    {
        // là on choisi le message qui sera affiché pour indiquer le niveau de log
        m_logLabels[VERBOSE] = "[v] ";
        m_logLabels[DEBUG]   = "[d] ";
        m_logLabels[INFO]    = "[i] ";
        m_logLabels[WARNING] = "[w] ";
        m_logLabels[ERROR]   = "[e] ";
    }
     
     
    void Log::writeLine(LogLevel level, const std::string &tag, const std::string &message) {
        if ( level >= m_log.m_logLevel ) {
            *m_log.m_ostream << m_log.m_logLabels[level] << tag << " : " << message << std::endl << std::flush;
        }
    }
     
     
    void Log::setLogLevel(LogLevel l) {
        m_log.m_logLevel = l;
    }
     
     
    void Log::setOutput(std::ostream &stream) {
        m_log.m_ostream = &stream;
    }
     
     
    #endif // SHOW_LOG

# Exemple d'utilisation

    Afin de tester le bon fonctionnement, nous allons faire le tour des fonctions mises à disposition.
    
    /*
        Exemple d'utilisation de la classe Log
    */
     
    #include <iostream>
    #include <fstream>
     
    #include <map>
    #include <set>
    #include <list>
    #include <vector>
    #include <deque>
     
    #include "Log.hpp"
     
    struct Foo {
     
        int bar;
     
        Foo() : bar(9) {
            Log::i("Foo") << "some Foo has been created, bar is : " << bar;
        }
     
        void doSomething() {
            Log::d("Foo") << "I'm doing something...";
        }
     
        void criticalFunction() {
            Log::d("Foo") << "Enter in critical part... Cross fingers";
            // do someting ...
            Log::w("Foo") << "It's going to crash";
            // some code ...
            Log::e("Foo") << "The sky is blue";       // mettre un message pour savoir pourquoi l'appli génère une erreur (erreur de type dans une fabrique par exemple)
        }
     
        friend std::ostream& operator <<(std::ostream&, const Foo&);
    };
     
    std::ostream& operator <<(std::ostream& stream, const Foo& foo) {
        stream << "Je suis Foo =) -> " << foo.bar;
        return stream;
    }
     
     
     
     
     
     
    int main(void) {
     
        // au démarage, TOUS les logs sont écris sur la sortie standard
        Log::v("main") << "Welcome in my testing prog";
     
     
        // on veut écrire les messages dans un fichier
        std::ofstream file("logs.txt");  // c'est à vous de traiter l'erreur d'ouverture du fichier !
        Log::setOutput(file);
     
     
        // message sans tag : fonctionne mais à éviter le plus possible (on ne sait pas vraiment d'où vient le message)
        Log::v() << "Voici un message sans tag";
     
     
        // on ne veut afficher que les messages qui sont au dessus du niveau DEBUG (donc ignorer les VERBOSES)
        Log::setLogLevel(DEBUG);
     
     
        Foo f;
        f.doSomething();
        f.criticalFunction();
     
        Log::d("main") << f;    // on peut bien entendu balancer des objets coutables
     
     
        bool b = true;
        Log::i("main") << "Demain il fera beau : " << b;    // voir spécialisation template bool
     
     
        // On peut basculer sur la sortie standard (ou n'importe quel ostream) quand on veut
        Log::setOutput(std::cout);
        Log::i("main") << "critical function -> ok";
     
     
        // une pair
        std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, std::string> somePair(42, "THE answer !");
        Log::d() << somePair;
     
        // une map
        std::map<int, std::string> someMap;
        someMap[18] = "my favorite number, so perfect...";
        someMap[42] = "the answer";
        Log::d() << someMap;
     
        // un set
        std::set<int> someSet;
        someSet.insert(42);
        someSet.insert(18);
        Log::d() << someSet;
     
        // une liste... de pair !
        std::list< std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, std::string> > someList;
        someList.push_back(somePair);
        someList.push_back( std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, std::string>(18, "my favorite number, so perfect...") );
        Log::d() << someList;
     
        // un vecteur
        std::vector<std::string> someVector;
        someVector.push_back("my favorite number, so perfect...");
        someVector.push_back("THE answer !");
        Log::d() << someVector;
     
        // un deque
        std::deque<int> someDeque;
        someDeque.push_back(18);
        someDeque.push_back(42);
        Log::d() << someDeque;
     
        // une multimap
        std::multimap<int, char> someMultimap;
        someMultimap.insert(std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, char>(1, 'a'));
        someMultimap.insert(std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, char>(1, 'b'));
        someMultimap.insert(std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, char>(2, 'c'));
        someMultimap.insert(std:<img src="http://www.xaviermichel.info/data/smileys/tongue_laugh.gif" width="19" height="19" alt="tongue laugh" style="border:0;" />air<int, char>(2, 'a'));
        Log::d() << someMultimap;
     
        // multiset
        std::multiset<int> someMultiset;
        someMultiset.insert(9);
        someMultiset.insert(42);
        someMultiset.insert(18);
        someMultiset.insert(42);
        Log::d() << someMultiset;
     
     
        Log::v("main") << "Hope I'll see you later, bye !";       // ce verbose sera donc ignoré (on ne s'intéresse qu'au dessus de DEBUG)
     
        return 0;
    }

On obtient une partie sur la sortie standard :

    [v] main : Welcome in my testing prog 
    [i] main : critical function -> ok 
    [d]  : pair ( 42 , "THE answer !" )  
    [d]  : map [ ( 18 , "my favorite number, so perfect..." ) , ( 42 , "the answer" ) ] 
    [d]  : set { 18 , 42 } 
    [d]  : list [ pair ( 42 , "THE answer !" )  , pair ( 18 , "my favorite number, so perfect..." )  ] 
    [d]  : vector [ "my favorite number, so perfect..." , "THE answer !" ] 
    [d]  : deque [ 18 , 42 ] 
    [d]  : multimap [ ( 1 , a ) , ( 1 , b ) , ( 2 , c ) , ( 2 , a ) ] 
    [d]  : multiset { 9 , 18 , 42 , 42 } 

Et dans le fichier de logs :

    [info] Foo :                  some Foo has been created                                                                                                                             (../logs/main.cpp:13)
    [debug] Foo :                  I'm doing something...                                                                                                                                (../logs/main.cpp:17)
    [debug] Foo :                  Enter in critical part... Cross fingers                                                                                                               (../logs/main.cpp:21)
    [warning] Foo :                  It's going to crash                                                                                                                                   (../logs/main.cpp:23)
    [error] Foo :                  The sky is blue                                                                                                                                       (../logs/main.cpp:25)

	
# Aller plus loin
	
On pourrait écrire un petit programme affiche le fichier en mettant différentes couleurs selon le niveau du log, cela permettrait de faire ressortir les informations importantes noyés dans le fichier. Allez on est motivé, on lance vim et c'est parti 

On veut pouvoir filtrer les niveau de logs depuis ce logiciel, l'interface se veut minimaliste car il n'y a pas moultes choses à gérer.

Bon la le problème c'est de simuler la commande tail, avec des bons vieux flux ça fonctionnait bien sous windows mais pas sous linux... Etrange... Enfin bon du coup j'ai fait une version qui fonctionne et vous pouvez [télécharger les sources de logwatcher](/assets/posts/logwatcher.tar.gz). Un screen pour le plaisir des yeux ?

Enfin tout ça pour dire que vous pouvez mettre les couleurs que vous voulez dans Tools.cpp.

# Conclusion

Bon ben du coup, on a notre outil pour enregistrer les logs (qui va évoluer mais la base restera surement la même) et un petit plus qui est un second outil pour les regarder, objectif atteint 


	