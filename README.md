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

> La première étape est de configurer le domaine qu'on vient de louer. Il faut lier
le domaine à l'hébergement.

Voici une petite capture de mon espace client chez OVH.

![Espace client OVH](https://github.com/bhubr/deploy-projects/raw/master/img/espace-client-ovh-zone-dns.png)

J'ai une liste de domaines que je loue chez eux (barre latérale, à gauche). Dans l'affichage principale,
j'ai la configuration du domaine wild31.com, et particulièrement, la **Zone DNS**.

Le DNS ou *Domain Name System*, pour faire court, c'est ce qui permet de faire le lien entre:
* Le nom d'un serveur, comme www.google.fr, qui est lisible et compréhensible par un être humain
* L'adresse IP du serveur, composée de 4 nombres, par exemple 216.58.209.227

Dans l'onglet "Zone DNS" montré ci-dessus, il y a une liste d'entrées, chacune ayant 4 colonnes : Domaine, TTL, Type, Cible.

Ce qui nous intéresse est l'entrée dont le domaine est `wild31.com` et dont le type est `A`.
L'adresse par défaut qui lui est associée est 213.186.33.5, qui est un serveur chez
OVH.

Je vais lui associer une autre adresse IP, qui est celle de mon serveur. Je clique donc sur l'engrenage de la ligne
dont le type est `A` :

![Modifier entrée DNS](https://github.com/bhubr/deploy-projects/raw/master/img/zone-dns-modifier.png)

Je choisis "Modifier l'entrée", ce qui ouvre une fenêtre "modale", avec un formulaire :

![Modifier adresse IP cible](https://github.com/bhubr/deploy-projects/raw/master/img/zone-dns-ip.png)

Dans le formulaire, je remplace la valeur courante du champ cible par l'adresse IP de mon serveur.
Dans mon cas, mon serveur s'est fait attribuer l'adresse 5.39.82.119, je mets donc cette valeur comme cible, puis je valide. Un dernier écran me confirme la prise en compte des modifications :

![Valider modification entrée](https://github.com/bhubr/deploy-projects/raw/master/img/zone-dns-valider.png)
