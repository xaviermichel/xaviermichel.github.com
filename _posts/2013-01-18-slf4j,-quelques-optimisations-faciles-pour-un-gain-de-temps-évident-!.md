---
layout: post
category : astuce
tagline: ""
tags : [python, x]
---
{% include JB/setup %}

Quand on développe une application java, on utilise un logger. Dans mon cas, j'utilise slf4j avec logback comme implémentation.

Cependant, un logger mal utilisé peut mener à des performances médiocres. Par exemple, on log des objets au niveau trace en local et une fois le passage en production, les objets ne sont pas loggés mais la jvm va quand même devoir faire des traitements sur l'objet. Quels traitements ? Comment les éviter ?

*****

Pour le format de l'article, j'ai choisi de faire un code tout pourri puis de l'améliorer petit à petit en prenant soin d'essayer de mesurer le gain de temps. Le code complet des tests est sur [github](https://github.com/xaviermichel/optimisation-slf4j).


# Un code très bof bof

## Mise en place d'un code pour les tests

Pour commencer, j'ai défini un POJO qui va me servir pour faire mes tests.

    package fr.xmichel.test;
     
    import java.util.Arrays;
     
    /**
     * Un simple POJO d'exemple
     * @author xavier
     *
     */
    public class Eleve {
     
    	private Integer id;
     
    	private String civilite;
     
    	private String nom;
     
    	private String prenom;
     
    	private int age;
     
    	private double taille;
     
    	private double poids;
     
    	private int[] notes;
     
    	public Eleve(String civilite, String nom, String prenom, int age,
    			double taille, double poids, int[] notes) {
    		super();
    		this.civilite = civilite;
    		this.nom = nom;
    		this.prenom = prenom;
    		this.age = age;
    		this.taille = taille;
    		this.poids = poids;
    		this.notes = notes;
    	}
     
    	/**
    	 * Ajoute une note à l'éleve
    	 * 
    	 * Cette version n'est pas optimisée du tout par rapport à une ArrayList ou LinkedList
    	 */
    	public void addNote(int note) {
    		notes = Arrays.copyOf(notes, notes.length + 1);
    		notes[notes.length-1] = note;
    	}
     
    	/**
    	 * La seul méthode qui a un vrai traitement
    	 */
    	public double getMoyenne() {
    		int total = 0;
    		for (int note : notes) {
    			total += note;
    		}
     
    		return ((double)total)/notes.length;
    	}
     
    	@Override
    	public String toString() {
    		String eleveString = "Eleve"
    				+ "{ id= " + id + ", "
    				+ "civ=" + civilite + ", "
    				+ "nom=" + nom + ", "
    				+ "prenom=" + prenom + ", "
    				+ "age=" + age + ", "
    				+ "taille=" + taille + ", "
    				+ "poids=" + poids + ", "
    				+ "notes=[";
    		for (int note : notes) {
    			eleveString += note + ",";
    		}
    		eleveString += "]," +
    		"moyenne=" + getMoyenne()
    		+ " }";
     
    		return eleveString;			
    	}
     
    	//
    	// tous les getters / settes
    	//
    
    }
	
J'ai ensuite mis en place un petit bout de code afin d'utiliser un peu ce POJO :

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
     
    public class EleveServiceImpl implements EleveService {
     
    	private static final Logger logger = LoggerFactory.getLogger(EleveServiceImpl.class);
     
    	@Override
    	public int passerExamen(Eleve e) {
    		logger.debug("L'eleve passe un examen...");
     
    		// du code...
    		logger.trace("Cet examen n'est pas facile !");
    		// du code...
     
    		int noteFinale = 10;
     
    		e.addNote(noteFinale);
     
    		logger.debug("On a obtenu la note : " + noteFinale);
    		logger.trace("e vaut maintenant" + e);
     
    		return noteFinale;
    	}
    }
	
## Configuration du logger

J'ai ensuite configuré mon logback pour qu'il n'affiche pas mes traces de debug

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
    	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    		<encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
    			<Pattern>%d{yyyy-MM-dd_HH:mm:ss.SSS} %-5level %logger{36} - %msg%n</Pattern>
    		</encoder>
    	</appender>
    	<root level="INFO">
    		<appender-ref ref="STDOUT" />
    	</root>
    </configuration>

## Boucle de test
	
Afin de pouvoir réellement voir les différences de performance, j'ai bouclé sur l'appel de méthode :

private static final int RUNNING_COUNT=10000;
 
private static Logger logger = LoggerFactory.getLogger(ProfilingEleveServiceTestCase.class);
 
    @Test
    public void test() {
    	ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
     
    	EleveService eleveService = (EleveService) context.getBean("eleveService");
     
    	long averageTime = 0;
     
    	Eleve e = new Eleve("Mr", "Doe", "John", 22, 1.72, 68.2, tableOfInt(1, 10));
     
    	for (int i=0; i<RUNNING_COUNT; ++i) {
    		long start = System.currentTimeMillis();
    		eleveService.passerExamen(e);
    		averageTime += System.currentTimeMillis() - start;
    	}
     
    	logger.info("Time for {} tries : {} ms", RUNNING_COUNT, averageTime);
    }
	
## Exécution !

Pour l’exécution, on va juste lancer cette méthode "test". Là, surprise (ou pas), le temps d’exécution est de plus de 4 minutes !

    [INFO] Aucune optimisation ............................... SUCCESS [4:21.295s]
	
Mais pourquoi ? On ne log aucun message... La réponse est simple, même si les logs ne sont pas actifs, la jvm doit tout de même faire des traitements lors de l'appel aux méthodes trace(...), debug(...).

Typiquement, prenons :

    // Eleve e
    logger.trace("e vaut maintenant" + e);
	
Lors de l'appel à la méthode trace, la JVM doit faire un toString de e (que j'ai bien implémenté en mode chèvre (garbage collecting, création d’instances à chaque opération (String est une classe immuable), copies de tableaux)) et concaténer le résultat obtenu avec "e vaut maintenant".
	
Deux axes se dessinent :
1. Le toString implémenté comme il l'est actuellement est très couteux !
2. Ne pourrait-on pas se passer totalement se passer de cet appel de méthode comme on ne va pas afficher l'information ?

Je vais les mettre en place individuellement afin de mesurer l'impact des modifications.

# Réécrivons la méthode toString de notre Eleve

## Modification du code

La méthode toString actuellement définie est extrêmement coûteuse à cause des nombreuses concaténations demandées.

Il est beaucoup plus judicieux d'utiliser un StringBuilder :

    @Override
    	public String toString() {
    		StringBuilder out = new StringBuilder();
     
    		out.append("Eleve");
     
    		out.append("{ id= ");
    		out.append(id);
    		out.append(", ");
     
    		out.append("civ= ");
    		out.append(civilite);
    		out.append(", ");
     
    		out.append("nom= ");
    		out.append(nom);
    		out.append(", ");
     
    		out.append("prenom= ");
    		out.append(prenom);
    		out.append(", ");
     
    		out.append("age= ");
    		out.append(age);
    		out.append(", ");
     
    		out.append("taille= ");
    		out.append(taille);
    		out.append(", ");
     
    		out.append("poids= ");
    		out.append(poids);
    		out.append(", ");
     
    		out.append("notes=[");
    		for (int note : notes) {
    			out.append(note);
    			out.append(", ");
    		}
    		out.append("],");
     
    		out.append("moyenne=");
    		out.append(getMoyenne());
    		out.append(" }");
     
    		return out.toString();			
    	}

	
## Exécution !

Une plutôt très bonne surprise...

    [INFO] Utilisation d'un string builder ................... SUCCESS [3.638s]
	
Pas mal du tout ! :D

# Eviter les appels inutiles

> Pour ce test, nous avons repris le premier code donné (avec une méthode toString pas optimisée du tout)

Reprenons notre ligne de trace :

    // Eleve e
    logger.trace("e vaut maintenant" + e);

Lors de cette action, la méthode toString de e va être appelée puis la concaténation avec "e vaut maintenant" va se faire. Mais ne pourrait-on pas ne pas faire cet appel à toString ? Si, en utilisant la [syntaxe avec accolade](https://github.com/qos-ch/slf4j/blob/master/slf4j-api/src/main/java/org/slf4j/Logger.java#L110) pour construire la chaine non pas au moment de l'appel, mais que logback s'en occupe uniquement si c'est nécessaire.

L'idée, c'est qu'au lieu de faire l'appel avec un string toute faite, on passe juste la référence et logback fera le traitement uniquement si nécessaire.

Pour éviter ces concaténations qui peuvent être extrêmement nombreuses

## Modification du code

    logger.debug("On a obtenu la note : {}", noteFinale);
    logger.trace("e vaut maintenant {}", e);
	
## Exécution !

Et là ça cartonne !

    [INFO] Passage en reference de l'object au logger ........ SUCCESS [1.509s]

	
# Les deux optimisations en même temps ?

Bonne idée ! Ainsi sera moins pénalisé en performance si on doit afficher l'objet. Par contre, dans l'état actuel des choses ça n'ira pas plus vite comme on a fait disparaître l'appel à toString.

En résumé :
    [INFO] Aucune optimisation ............................... SUCCESS [4:11.994s]
    [INFO] Utilisation d'un string builder ................... SUCCESS [3.819s]
    [INFO] Passage en reference de l'object au logger ........ SUCCESS [1.580s]
	
J’espère que ces optimisations faciles à mettre en place vous permettront de booster vos performances ;)


