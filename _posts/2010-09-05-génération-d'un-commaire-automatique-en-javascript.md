---
layout: post
category : Premiers pas
tagline: ""
tags : [javascript, sommaire]
---
{% include JB/setup %}

Le but de cet article est de présenter une solution pour générer automatiquement un sommaire sur une page html.

Cette solution est utilisée sur ce site.

*****

# Pré-requis

Afin de simplifier les choses, nous allons utiliser jquery : 

    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js"></script>
	
# La super fonction qui créé le sommaire

Cette fonction créée le sommaire que l'on peut voir sur les pages articles de ce site entre autre. Pour cela elle utilise les h2 et h3 mais rien n’empêche de l'étendre à une utilisation plus large, juste que ça me suffit à moi. Notons que le sommaire apparaîtra après la balise avant la balise #to_summary dans ce script.

    function tableofcontent() {
     
    	// On crée le sommaire tout en haut
    	$('#summaryPlace').after('<div id="summary" class="summary"><label>Sommaire :</label><ol class="level1"></ol></div>');
     
    	var no = 0;
    	var no2 = 0
     
    	$('#to_summary *').each(function(useless, title){ 
     
    		if ( title.tagName == "H2" ) {
     
    			// numéro de title suivant
    			no++;
     
    			// On ajoute l'id au title, pour l'ancre
    			$(title).attr('id', 'title-'+no);
     
    			// on met dans le sommaire
    			$('#summary .level1').append(
    				  '<li id="summary-'+no+'">'
    					+'<a href="#title-'+no+'">'+$(title).text()+'</a>'
    				 +'</li>');
     
    			// sous title repart de 0
    			no2 = 0;
     
    		} else if ( title.tagName == "H3" ) { 
     
    			// On crée une liste de sous-parties
    			if ( no2 == 0 ) {
    				$('#summary-'+no).append('<ol class="level2"></ol>');
    			}
     
    			// incrémentation du numéro de la sous partie
    			no2++;
     
    			// On ajoute l'id au sous-title, pour l'ancre
    		  	$(title).attr('id', 'title-'+no+'-'+no2);
     
    			// + le lien de l'ancre vers le sous-title
    			$('#summary-'+no+' ol').append(
    				  '<li>'
    				   +'<a href="#title-'+no+'-'+no2+'">'+$(title).text()+'</a>'
    				 +'</li>');
     
    		}
    	});
     
    	if ( no == 0 ) { // il ne faut pas afficher le sommaire 
    		$('div.summary').hide();
    	}	
    }
	
# L'appel de cette fonction

Il reste plus qu'à appeller la fonction qui va créer le sommaire dès lors que la page aura fini son chargement !

    <script language="javascript"> 
    	$(document).ready(function() {
    		tableofcontent();
    	});
    </script> 
    <div id="summaryPlace"></div> 
	
Et ben voilà on a un sommaire avec grande simplicité, bon c'est pas un algo de tueur loin de là, mais on trifouille déjà un peu deux petites variables ;)

***
>*Migration commentaires :*
>
>
>>alix1254, le 06/09/2013 à 13:37
>>Bonjour,
>>
>>J'utilise votre algo, il n'a pas l'air de fonctionner, c'est normal ?
>
> ***
>
>>Xavier, le 06/09/2013 à 13:45
>>Bonjour, non, cela n'est pas normal  ;)
>>
>>Avez-vous une erreur qui s'affiche en console ?
>
> ***
>
>>alix1254, le 06/09/2013 à 14:08
>>Oui mais je les ai réglée. En revanche, le java n'a pas l'air de fonctionner
>
> ***
>
>>Xavier, le 06/09/2013 à 14:29
>>Il n'y a pas de java, mais du javascript ;) 
>>Une erreur doit s'afficher en console s'il y un a un problème, sinon il faudra débugger 
>
> ***
>
>>alix1254, le 06/09/2013 à 14:31
>>C'est votre variable "no" apparemment qui déconne mais je sais pas d'ou ça peut venir et le sommaire ne s'affiche même pas
>
> ***
>
>>alix1254, le 06/09/2013 à 14:56
>>C'est bon je l'ai débeuguer, mais y'a un problème pour les sous parties, les liens des ancres ne marchent pas
***

	