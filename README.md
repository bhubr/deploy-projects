# Mise en ligne d'un projet web

## Nom de domaine et hébergement
Pour mettre en ligne un projet web, on a besoin de deux choses :
- un nom de domaine, comme `mondomaine.com`
- un hébergement

Pour obtenir cela, on fait généralement appel à des fournisseurs de services spécialisés,
souvent appelés des *hébergeurs*.

## Quelques hébergeurs

* [OVH](https://www.ovh.com/fr/)
* [Gandi](https://www.gandi.net/fr)
* [Amen](https://www.amen.fr/)
* [GoDaddy](https://fr.godaddy.com/)
* [1&1](https://www.1and1.fr/)

Les hébergeurs proposent souvent d'acheter un nom de domaine **et** un hébergement
ensemble, mais il est possible d'acheter l'un ou l'autre séparément.

## Noms de domaines

Les domaines se "louent" généralement pour une durée d'un an, reconductible à volonté.
Un domaine peut coûter de 3€ par an à 40€ par an, suivant l'extension (.com, .net, .tech, .io),
voire bien plus pour certains noms de domaine prisés.

## Hébergements

Pour les hébergements, il y a globalement deux possibilités : hébergement dit "partagé", ou hébergement privé

### Hébergement partagé

L'hébergeur nous crée un compte sur un gros serveur Linux,
nous alloue de la bande passante sur le réseau, un certain espace disque (100Mo, 500Mo, ou +),
et nous crée une ou plusieurs bases de données. Ce sont des hébergements souvent
prévus pour les CMS (système de gestion de contenu) ou plateformes de blog, le plus souvent
basés sur PHP comme WordPress, Joomla, Drupal, Spip.
Beaucoup de choses sont pré-configurées, et c'est bien quand on ne veut pas *trop*
s'occuper de technique.
Un tel hébergement coûte aux environs de 50€/an, ou plus si on a besoin de performances accrues.

### Hébergement privé

L'hébergeur nous met à disposition un serveur rien que pour nous !
En vérité, sauf à payer très cher pour avoir un vrai serveur, il s'agit d'une "machine virtuelle",
d'un PC virtuel tournant sur un (très) gros serveur. On appelle cela un VPS pour
*Virtual Private Server* : *virtual* pour mettre en évidence le fait que c'est une
machine virtuelle, *private* pour indiquer que c'est un service qui nous est réservé.

## Et pour nos projets ?

On a acheté un domaine pour la Wild de Toulouse : [wild31.com](http://wild31.com), sans hébergement.

On aurait plusieurs solutions pour l'hébergement.

* Soit utiliser le crédit offert avec votre pack GitHub Education, pour des services
comme Amazon AWS, ou DigitalOcean. Seul souci, le crédit offert risque d'être tout juste suffisant pour 6 mois.
* Soit commander un VPS chez OVH.
* Soit en utiliser un qui existe... Il se trouve que j'en ai un que j'utilise pour héberger différentes choses.
Il est suffisamment puissant (a priori) pour pouvoir "encaisser" la charge de 3 applications Node.js en plus.

## Configuration du domaine

Voici une petite capture de mon espace client chez OVH.

[!Espace client OVH](https://github.com/bhubr/deploy-projects/raw/master/espace-client-ovh-zone-dns.png)
