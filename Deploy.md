# Mise en ligne d'un projet web

Ce document résume ce qu'il faut connaître pour mettre en ligne une application web.

La première partie porte sur des notions de réseau : les adresses IP et le DNS.

La seconde partie aborde l'achat (ou plutôt la location) d'un hébergement et d'un nom de domaine.

La troisième partie aborde la configuration d'un serveur Linux de type VPS (*Virtual Private Server* ou serveur privé virtuel), jusqu'à l'installation du serveur web Nginx.

La quatrième partie abordera plus spécifiquement la mise en ligne d'un projet basé sur la "stack" Node.js + React.

Enfin, un annexe résume de façon très concise les opérations à effectuer.

## 1. Notions de réseau

### Internet Protocol et les adresses IP

Le terme de *protocole* t'est déjà familier, et tu en connais déjà un exemple : le protocole HTTP, qui régit les échanges entre un serveur web et un client (navigateur par exemple).

Il faut savoir que les protocoles réseau sur lesquels se base Internet sont organisés en couches ou en [piles](https://fr.wikipedia.org/wiki/Pile_de_protocoles) : un protocole peut reposer sur un autre protocole (tout comme, sur un ordinateur, une application s'appuie sur des librairies, qui elles-mêmes s'appuient sur des fonctionnalités du système d'exploitation).

Les protocoles tels qu'HTTP, FTP, IMAP, etc. reposent sur deux autres protocoles : TCP et IP (auquel on fait souvent référence par TCP/IP). IP signifie *Internet Protocol*. Il s'occupe notamment du routage des informations entre deux points du réseau. TCP signifie *Transmission Control Protocol* et s'occupe du transport des données, intégrant des mécanismes de contrôle et de correction d'erreurs.

Sur Internet, chaque ordinateur connecté au réseau dispose d'une adresse IP :

![IP](https://raw.githubusercontent.com/bhubr/deploy-projects/master/img/internet-protocol.svg)

Concrètement, lorsque tu requêtes une page à un serveur via ton navigateur, c'est grâce à l'adresse IP du serveur que la requête est dirigée vers lui. Mais comment le navigateur connait-il l'adresse IP du serveur ? C'est là qu'intervient le DNS.

### Le DNS ou Domain Name System

Le DNS est ce qui permet d'établir une correspondance entre un *nom de domaine* (par exemple github.com) et une adresse IP (par exemple 140.82.118.3). C'est une sorte de "registre", une base de données, dans lesquelles sont stockées ces correspondances. Ces correspondances sont dupliquées sur des centaines de serveurs, dits serveurs DNS, de par le monde.

Lorsque tu saisis une adresse de site en toutes lettres dans la barre d'adresses de ton navigateur, si ce site n'a jamais été visité, le navigateur interroge un serveur DNS pour obtenir l'adresse IP qui correspond au nom de domaine du site. L'adresse IP est par la suite stockée sur ton ordinateur dans un "cache DNS", afin d'éviter d'effectuer des requêtes DNS répétées pour un même nom de domaine.

![DNS](https://raw.githubusercontent.com/bhubr/deploy-projects/master/img/domain-name-system.svg)

## 2. Commande d'un serveur et d'un nom de domaine

### Les différentes options d'hébergement

Un petit point, d'abord, sur les différentes options d'hébergement. On trouve sur le marché :

* Des hébergements dits "mutualisés" : l'hébergeur nous accorde une certaine quantité de ressources, sur un serveur partagé, "mutualisé" donc entre différents utilisateurs. En fonction de l'option tarifaire choisie, on obtient plus ou moins d'espace disque, de bases de données (limite d'une base de données pour le tarif le plus bas), etc. C'est le plus simple à mettre en oeuvre, mais traditionnellement, il est plus adapté à des sites HTML statiques ou des sites/applications écrits en PHP, et ne permet pas d'utiliser d'autres technos comme Node.js.
* des serveurs privés : L'hébergeur nous met à disposition un serveur "rien que pour nous". En vérité, sauf à payer très cher pour avoir un vrai serveur, il s'agit d'une "machine virtuelle", d'un PC virtuel tournant sur un (très) gros serveur. On appelle cela un VPS pour *Virtual Private Server* : *virtual* pour mettre en évidence le fait que c'est une machine virtuelle, *private* pour indiquer qu'il nous est réservé. La virtualisation permet à chaque utilisateur peut gérer son serveur de A à Z, et notamment de le redémarrer ou de le réinstaller, sans que la machine physique, et les autres serveurs hébergés sur cette machine, ne soient impactés.
* des services tels qu'[Heroku](https://www.heroku.com/) ou [AWS](https://aws.amazon.com/fr/) (proches des VPS, souvent plus faciles à configurer, mais pouvant être plus coûteux)

Parmi les avantages du VPS :

* Un coût fixe, et pouvant être très bas (3.59€ TTC/mois pour le plus petit VPS chez OVH).
* La possibilité d'héberger de multiples sites sur le même serveur (et avec différentes technos : Node.js, Python, PHP, Java, etc.), dans la limite des capacités de la machine.

Parmi ses potentiels inconvénients :

* Le fait de devoir tout configurer soi-même : gestion des certificats pour le HTTPS, backups de base de données, etc., ce qui nécessite des compétences système (Linux)plus ou moins avancées.
* En particulier, il faut bien veiller à sécuriser le serveur : firewall, etc.

### Commande d'un VPS

Sans vouloir spécifiquement leur faire de la pub, OVH est l'hébergeur que nous utilisons majoritairement pour le déploiement de projets à la Wild Code School.

Pour quelqu'un qui souhaite se lancer à bas prix, leur gamme [Kimsufi](https://www.kimsufi.com/fr/) est suffisante, en particulier la gamme [VPS SSD](https://www.kimsufi.com/fr/vps-ssd.xml) : des serveurs pourvus, comme leur nom l'indique, de SSD au lieu de disques durs classiques.

La commande se fait en quelques clics, le plus important étant la 2ème étape :

* choix du datacenter où est hébergé le serveur physique
* choix de la version de Linux (Debian, Ubuntu, CentOS, etc.)

Ayant l'habitude d'utiliser Ubuntu à la Wild, tu peux choisir Ubuntu où Debian (dont Ubuntu est dérivée).

Une fois la commande validée, après une attente de 15-20 minutes, tu reçois un e-mail indiquant que ton serveur est prêt. Cet email contient :
* l'adresse IP et un nom de machine par défaut attribué par OVH (vps345678.ovh.net)
* Le mot de passe du compte Unix `root`.

Cette petite vidéo (5 min.) résume cette partie :

https://youtube.com

### Commande d'un nom de domaine

Le choix d'un nom de domaine demande souvent un peu de réflexion. De nombreuses ressources ([exemple](https://www.ovh.com/fr/blog/10-regles-choisir-nom-de-domaine/)) donnent quelques critères objectifs pour se décider, auxquels on ne pense pas forcément au premier abord.

Après avoir choisi, on peut réserver le nom de domaine pour un an (souvent renouvelé automatiquement, sauf résiliation avant la date anniversaire).

Chez OVH, la commande se passe [sur cette page](https://www.ovh.com/fr/domaines/). Le Le prix dépend de l'extension choisie (`.fr`, `.net`, `.com`, etc.), et oscille autour de 8-10€ par an.

Une vidéo pour cette partie :

https://youtube.com

## 3. Configuration du VPS

### Configuration DNS