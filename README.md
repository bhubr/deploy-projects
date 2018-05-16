# Mise en ligne d'un projet web

## Déploiement en local

Avant de mettre en ligne réellement sur un projet, on va faire un exercice : on va se préparer,
en le déployant localement.

Au niveau des connaissances, il y a quelques pré-requis, mais ne t'en fais pas,
une courte explication de chacun de ces points suivra :
* Adresse IP
* Traduction du nom d'hôte dans une URL en adresse IP
* DNS ou Domain Name System
* Fichier hosts pour adresses IP locales

### Adresse IP

Chaque ordinateur connecté sur un réseau a une adresse IP (IP pour Internet Protocol).
Une adresse IP est composée de 4 nombres, séparés par des points. Exemples :
* 192.168.1.1
* 10.0.0.33
* 77.136.57.210

La valeur de chacun des nombres peut être comprise entre 0 et 255 (donc 256 valeurs possibles).
Je t'épargne le calcul, mais le nombre de combinaisons possibles pour 4 nombres pouvant prendre
chacun 256 valeurs, est 4 294 967 296 (ou : "4 milliards et des brouettes").

Donc théoriquement, on pourrait avoir 4 milliards d'ordinateurs connectés en même temps sur Internet.
Autant cela semblait suffisant aux débuts de l'Internet (années 70), autant ça devient "juste"
avec la multiplication des smartphones et autres objets connectés. Les adresses IP à 4 chiffres
sont appelées adresses IPv4, car elles correspondent à la version 4 du protocole IP (sur lequel
s'appuient tous les autres protocoles comme FTP, HTTP, etc.).

En prévision de la prochaine [pénurie d'adresses IP](https://fr.wikipedia.org/wiki/%C3%89puisement_des_adresses_IPv4),
des travaux ont commencé depuis longtemps sur son successeur, IPv6, qui permet d'avoir
un nombre d'adresses bien supérieur.

Mais pour l'instant, les adresses IPv4 sont encore utilisées, et la transition de IPv4 à IPv6 se fera progressivement.

## Plusieurs adresses IP

J'ai parlé précédemment d'*une* adresse IP par ordinateur. Les choses sont un peu plus complexes
que ça en réalité.

Par exemple, quand tu es chez toi - ou dans une école, une entreprise, ton ordinateur n'est pas
*directement* connecté à Internet, et ne dispose pas lui-même d'une adresse IP "publique" sur
Internet. La connexion passe en effet par une "box" qui fait office de "routeur"
(équipement qui sert de relais entre deux réseaux).

![Réseau local et Internet](https://github.com/bhubr/deploy-projects/raw/master/img/reseau-local-et-internet.png)

La box est connectée à deux réseaux, et a une adresse IP pour chaque :
* à Internet directement, via l'ADSL ou la fibre : elle obtient son adresse IP de la part
de ton fournisseur d'accès.
* au réseau local : c'est elle qui attribue les adresses IP des ordinateurs, tablettes, etc.
connectés localement.



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
