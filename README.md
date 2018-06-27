# Mise en ligne d'un projet web

## Déploiement en local

Avant de mettre en ligne réellement sur un projet, on va faire un exercice : on va se préparer,
en le déployant localement.

Au niveau des connaissances, il y a quelques pré-requis, mais ne t'en fais pas,
une courte explication de chacun de ces points suivra :
* Adresse IP
* Traduction du nom d'hôte dans une URL en adresse IP
* Fichier `hosts`
* DNS ou Domain Name System

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

### Plusieurs adresses IP

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

Quant à ton ordinateur... Combien d'adresses IP a-t-il ? Une seule, pourrait-on penser...
Eh bien, pour les ordinateurs récents, non ! Sous Linux, tu peux taper la commande `ifconfig` dans
le terminal, pour voir ta (ou tes) interfaces réseau. Tu dois voir quelque chose comme ceci :

![ifconfig dans le terminal](https://github.com/bhubr/deploy-projects/raw/master/img/terminal-ifconfig.png)

Alors, quand on parle d'interfaces réseau, il s'agit :
* des périphériques physiques de l'ordinateur, intégrés sur la carte mère de l'ordinateur
dans le cas d'un portable :
    * interface filaire (Ethernet est la norme la plus répandue pour ce type d'interface). Sur la capture,
    c'est la 1ère de la liste : dans `enp0s25`, le `en` est l'abréviation de EtherNet.
    * interface wifi, en 3ème dans la liste : dans `wlp3s0`, le `wl` est l'abréviation de WireLess.
* d'une interface appelée "boucle locale" ou *loopback*. Elle est présente sur tous les ordinateurs,
qu'on soit sous Linux, Windows ou OS X. Elle ne correspond pas à une carte réseau physique. C'est une interface "virtuelle"
qui permet de faire des connections, de ton ordi... à lui-même. C'est très pratique quand on veut
développer des applications web localement, et sans le savoir, tu l'utilises depuis le projet 2 :
que ce soit quand on accède à un serveur Node.js localement via [http://localhost:3000](http://localhost:3000),
ou quand on accède à [phpMyAdmin en local](http://localhost/phpmyadmin), on passe par cette interface.

Chaque interface a une adresse IP dédiée. Dans ma capture, les adresses IP sont encadrées en orange.
Les adresses IPv4 sont précédées de `inet`, les IPv6 de `inet6`.
Il n'y en a que deux :
* l'interface *loopback* est *toujours* connectée, et se voit *toujours* attribuer l'adresse IPv4
127.0.0.1, de sorte qu'entrer [http://localhost](http://localhost) ou [http://127.0.0.1](http://127.0.0.1)
dans la barre d'adresse de ton navigateur mène au même endroit !
* l'interface wifi est connectée dans mon cas. L'adresse IP qui lui a été attribuée par le routeur de l'école
est 10.0.0.33 (IPv4).
Par contre, bien que mon ordi ait une interface filaire, aucun câble n'est branché sur le port Ethernet.
L'interface n'est donc pas connectée, et ne peut pas se voir attribuer une adresse IP.

### Fichier `hosts`

Je viens de mentionner le fait que l'IP 127.0.0.1 est associée au nom d'hôte `localhost`.

Comment se fait la correspondance entre les noms d'hôtes et les adresses IP ? De deux façons
(complémentaires, l'une n'empêchant pas l'autre):
* Via les DNS qu'on verra par la suite
* Via le fichier `hosts` présent sur chaque ordi moderne, quel que soit le système. Seule son emplacement varie :
    * `/etc/hosts` sur les systèmes Unix, donc sous Linux et OS X
    * `c:\windows\system32\drivers\etc\hosts` sous Windows

Commence par l'afficher, en entrant `cat /etc/hosts` dans le terminal. Tu obtiens quelque
chose de semblable à ceci :

    127.0.0.1   localhost
    127.0.1.1   wilder-ThinkPad-T420

    # The following lines are desirable for IPv6 capable hosts
    ::1     ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters

On ne va pas s'occuper des lignes sous le commentaire qui mentionne IPv6. Il nous reste
donc les deux premières lignes : chacune contient une adresse IP, suivie d'un nom d'hôte.

**C'est l'intérêt du fichier `hosts`: associer un ou des noms à une adresse IP**.
Tu vas modifier ce fichier (`sudo nano /etc/hosts`), pour associer un autre nom à ta machine,
qui soit plus proche d'un "vrai" nom de domaine. On ne va modifier que la première ligne.

Avant ça, il faut décider d'un nom pour notre machine, qui ressemble à un nom de domaine
réel, à la différence qu'on ne va pas utiliser une des "extensions" classiques comme .com,
.net, .fr, etc. mais une extension qui n'existe pas (si tu veux savoir ceux qui existent
réellement, consulte [cette page](https://fr.wikipedia.org/wiki/Liste_des_domaines_Internet_de_premier_niveau)).

Comme on est à la Wild Code School, on va utiliser les initiales du nom de l'école
comme extension, ce qui nous donne .wcs. Pour le nom du domaine, je te propose d'utiliser
ton prénom et nom, tout en minuscules, sans accents, ce qui pour moi va donner : `benoithubert.wcs`.

J'ajoute ça à la première ligne du fichier, qui devient donc :

    127.0.0.1 localhost benoithubert.wcs

Puis je sauvegarde et quitte (Ctrl+O puis Ctrl+X). Pour vérifier que ça va marcher, je vais
utiliser une commande bien connue des administrateurs système et réseau :
[ping](https://fr.wikipedia.org/wiki/Ping_(logiciel)). C'est une commande qui permet
de vérifier si une machine est accessible via le réseau. Tu peux lui donner un nom de
machine, ou une adresse IP.

Je teste d'abord `ping localhost` :

    wilder@wilder-ThinkPad-T420:[~/Dev/deploy-projects] (deploy-local *%=) $ ping localhost
    PING localhost (127.0.0.1) 56(84) bytes of data.
    64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.083 ms
    64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.077 ms
    ...

Après avoir saisi la commande, des lignes s'affichent à intervalles réguliers, indiquant
entre autres l'adresse IP, et le temps de réponse (tout à la fin, en millisecondes).

Je teste `ping benoithubert.wcs` :

    wilder@wilder-ThinkPad-T420:[~/Dev/deploy-projects] (deploy-local *%=) $ ping benoithubert.wcs
    PING localhost (127.0.0.1) 56(84) bytes of data.
    64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.088 ms
    64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.061 ms

Visiblement, ça marche !

> Par curiosité, tu peux essayer `ping google.fr`, `ping wildcodeschool.fr`, etc.
Cela te donnera notamment les adresses IP de ces machines.

Il faut bien garder à l'esprit que ce nom de domaine qu'on a ajouté est factice.
De plus, le fait qu'on l'ait ajouté dans *notre* fichier `/etc/hosts` sur *notre*
machine signifie que d'autres machines n'auront aucune connaissance de cette correspondance
entre notre nom de domaine fictif et l'adresse de notre machine. Ce n'est pas grave,
c'est amplement suffisant pour des tests.

### Le DNS ou Domain Name System

Juste avant de passer à la suite, un petit mot sur le DNS : c'est un service offert
par de nombreux serveurs sur le réseau Internet, et qui permet d'établir une correspondance
entre les noms de machines et les adresses IP.

Quand tu accèdes à un site via ton navigateur, il se passe des choses en arrière-plan,
sans que ce soit visible pour toi :
* Le navigateur interroge le serveur DNS de ton fournisseur d'accès Internet, pour
obtenir l'IP du site que tu veux consulter.
* Le serveur DNS lui renvoie l'adresse IP correspondante.
* Le navigateur utilise l'adresse IP pour établir une connexion avec le serveur.

Pas besoin d'en rajouter pour l'instant, on va passer à la configuration d'un serveur
web local !

### Serveur web local

#### Vérification du serveur installé

Tu as peut-être déjà un serveur web sur ta machine ! Soit qu'il soit installé
par défaut avec ton système d'exploitation, soit que tu l'aies installé manuellement.

Pour le vérifier, va sur [http://localhost](http://localhost). Si ça répond, c'est
que tu as bien un serveur web installé. Pour vérifier de quel serveur il s'agit,
essaie une adresse "bidon", au hasard:
[http://localhost/winteriscoming](http://localhost/winteriscoming).

Tu vas obtenir une erreur 404, et par défaut, le serveur doit te donner une page ressemblant
à ceci :

![Apache - Not Found](https://github.com/bhubr/deploy-projects/raw/master/img/apache-not-found.png)

Tout en bas, le serveur t'indique qui il est - ce qui, au passage, sur un serveur "réel", exposé sur Internet,
n'est pas une très bonne idée : cela indique en effet quel logiciel serveur est utilisé
(ici, Apache), sa version (2.4.18), et l'OS sur lequel il tourne (ici Linux Ubuntu)...
Un individu mal intentionné pourrait s'en servir pour exploiter une vulnérabilité du logiciel serveur,
pour peu que tu aies une version un peu ancienne...

Comme tu as probablement Apache installé, et qu'on va utiliser son concurrent [nginx](https://fr.wikipedia.org/wiki/Nginx),
il faut d'abord arrêter Apache, par cette commande :

    sudo service apache2 stop

Si tu recharges ta page web restée sur localhost, ça ne doit plus marcher !

#### Installation de nginx

Comme d'habitude sous Ubuntu, un `apt-get install` va nous permettre d'installer
nginx :

    sudo apt-get install nginx

Si tu recharges la page web sur localhost, ça doit marcher à nouveau.

#### Configuration de nginx

##### Configuration globale et virtual hosts

Je vais essayer de ne pas te noyer ici ! La configuration d'un serveur web est en
effet assez complexe. Mais par chance, lors de l'installation, une configuration
de base existe déjà.

Comme ce serait le cas pour Apache, la configuration de nginx se fait à deux endroits :
* Un fichier de configuration global : `/etc/nginx/nginx.conf`.
* Un fichier de configuration par "virtual host"...

Wait! "Virtual host" ? What the heck is that ? Il faut savoir qu'une même machine
peut servir de serveur web pour plusieurs sites ! Et on n'a pas besoin, dans ce cas,
d'installer plusieurs logiciels serveurs. D'ailleurs, on ne le pourrait pas,
car le serveur web doit par défaut utiliser le port 80, et deux programmes ne peuvent
pas accéder au même port.

On peut donc configurer nginx pour qu'il soit capable de gérer plusieurs noms de domaines.
Pour chaque nom de domaine, il y a une configuration particulière. C'est ça un "virtual host".
On n'a qu'une machine réelle, mais une configuration qui expose plusieurs domaines au monde extérieur.

On va d'abord ouvrir `/etc/nginx/nginx.conf`. Je te conseille d'utiliser un plugin
permettant d'avoir le "syntax highlighting" (coloration syntaxique) pour ton éditeur.
Il en existe pour [Atom](https://atom.io/packages/language-nginx), [Sublime](https://github.com/brandonwamboldt/sublime-nginx),
[VSCode](https://github.com/brandonwamboldt/sublime-nginx), entre autres.

Alors, attache ta ceinture, et prends deux aspirines, car ça risque de piquer un peu...

Voilà à quoi ressemble la bête :

    user www-data;
    worker_processes auto;
    pid /run/nginx.pid;

    events {
        worker_connections 768;
        # multi_accept on;
    }

    http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        gzip on;
        gzip_disable "msie6";

        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
    }


    #mail {
    #   # See sample authentication script at:
    #   # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
    #
    #   # auth_http localhost/auth.php;
    #   # pop3_capabilities "TOP" "USER";
    #   # imap_capabilities "IMAP4rev1" "UIDPLUS";
    #
    #   server {
    #       listen     localhost:110;
    #       protocol   pop3;
    #       proxy      on;
    #   }
    #
    #   server {
    #       listen     localhost:143;
    #       protocol   imap;
    #       proxy      on;
    #   }
    #}

Bon, je te rassure, tu ne vas pas avoir besoin de modifier quoi que ce soit dans ce fichier !
Remarque juste qu'il y a des "sections" commençant par un mot-clé suivi d'accolades.

Par exemple, tout ce qui se trouve dans les accolades suivant `http`, est une configuration
globale, commune à tous les virtual hosts.

Ce qui nous intéresse particulièrement ici, ce sont deux lignes consécutives à la fin
du bloc `http` :

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

Ce sont des "includes". C'est un terme commun en informatique, pour exprimer qu'un
fichier peut en inclure un autre. C'est utilisé aussi bien pour des fichiers de configuration
que pour des programmes (PHP par exemple dispose d'une instruction `include` pour
appeler un autre fichier PHP depuis un programme).

Ici, ces lignes permettent d'inclure les fichiers de configuration des virtual hosts.
La ligne `include /etc/nginx/sites-enabled/*;` inclut tous les fichiers présents dans
le répertoire `/etc/nginx/sites-enabled/`. Regardons d'ailleurs ce qu'il contient :

    ls -l /etc/nginx/sites-enabled

Le flag `-l` permet d'avoir une liste détaillée des fichiers. On obtient le résultat suivant :

    lrwxrwxrwx 1 root root 34 mars  27 10:06 default -> /etc/nginx/sites-available/default

Il y a un seul fichier virtual host par défaut quand on vient d'installer nginx : `default`.
Par ailleurs, dans le terminal Linux, son nom s'affiche en bleu ciel. Cela, ajouté à la lettre `l`
au tout début de la ligne, indique qu'il s'agit d'un *lien symbolique*. Encore un terme barbare !
En résumé, c'est juste un "raccourci" vers un autre fichier. Le lien symbolique ne prend
pas de place sur le disque. Par contre, si on le lit ou si on écrit dessus, le "vrai"
fichier, celui vers lequel il pointe, est lu ou modifié.

Ici, on nous indique que le fichier pointé par `default` est `/etc/nginx/sites-available/default`.
Il est pratique d'avoir deux répertoires distincts : `sites-available` et `sites-enabled`.
On peut ainsi créer un fichier de configuration pour un virtual host dans `sites-available`,
et une fois qu'il est prêt, créer un lien dans `sites-enabled`. Ensuite, il faut redémarrer le serveur.

Si jamais on veut par la suite désactiver ce virtual host, on n'a pas besoin de supprimer
son fichier de configuration dans `sites-available`. Au lieu de cela, on peut juste
supprimer le lien symbolique correspondant dans `sites-enabled`, puis redémarrer le serveur.

##### Ajout d'un virtual host

On va maintenant créer un fichier virtual host pour notre domaine fictif `prenomnom.wcs`.

On copie d'abord le fichier `default` dans un fichier portant le nom du domaine
(ce n'est pas obligatoire mais c'est une convention bien pratique) :

    sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/benoithubert.wcs

Puis on l'édite. Autant le faire avec notre éditeur préféré. Ci-dessus j'utilise Atom,
`code` pour Visual Studio ne marchant pas avec `sudo`. Mais j'aurais pu utiliser `subl` pour Sublime Text.

    sudo atom /etc/nginx/sites-available/benoithubert.wcs

En fait on a juste besoin du bloc qui se trouve entièrement commenté
en fin de fichier, et précédé de la mention :

    # Virtual Host configuration for example.com

On *supprime* tout ce qui se trouve *au-dessus* de cette ligne, et on décommente tout ce qui se trouve à partir du bloc `server` *sous* cette ligne. Puis on remplace toutes les mentions de `example.com` par notre
nom de domaine. J'obtiens pour ma part ceci :

    # Virtual Host configuration for benoithubert.wcs

    server {
    	listen 80;
    	listen [::]:80;

    	server_name benoithubert.wcs;

    	root /var/www/benoithubert.wcs;
    	index index.html;

    	location / {
    		try_files $uri $uri/ =404;
    	}
    }

##### Création du lien symbolique

Une fois le fichier sauvegardé, je crée un lien symbolique de ce fichier vers
son équivalent dans `sites-enabled` :

    sudo ln -s /etc/nginx/sites-available/benoithubert.wcs /etc/nginx/sites-enabled/benoithubert.wcs

##### Création du répertoire de documents et d'un index

Quelques explications tout de même, et quelques opérations à effectuer pour en finir
avec cette étape...

La ligne `server_name` indique que ce virtual host ne traitera *que* les requêtes
envoyées sur l'adresse `benoithubert.wcs`.

La ligne `root` indique où se trouvent les fichiers correspondants à ce virtual host.
Il faut créer le répertoire correspondant :

    sudo mkdir /var/www/benoithubert.wcs

La ligne `index` indique le nom du fichier envoyé au client par défaut : par convention,
c'est `index.html`. On va garder cette convention, et créer le fichier.

    sudo atom /var/www/benoithubert.wcs/index.html

Pour tester, j'y colle un contenu HTML minimal :

    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="utf-8">
        <title>Test virtual host</title>
      </head>
      <body>
        <h1>Welcome to benoithubert.wcs</h1>
        <p>It works! Champagne!</p>
      </body>
    </html>

##### Redémarrage du serveur

On n'a plus qu'à tester notre config, en redémarrant nginx :

    sudo service nginx restart

Verdict, si tu as scrupuleusement suivi les étapes, tu dois être récompensé
par cette flamboyante page d'accueil :


![Nginx - It works](https://github.com/bhubr/deploy-projects/raw/master/img/nginx-vhost-it-works.png)

##### Note annexe !!

[... CETTE SECTION S'AUTO DETRUIRA QUAND J'AURAI TERMINE ...]

Dans cette partie, je t'ai fait désactiver Apache pour utiliser nginx. Mais cela
peut te rendre phpMyAdmin inaccessible, car on n'a pas encore configuré nginx
pour fonctionner avec PHP.

Si tu as besoin de phpMyAdmin, tu peux désactiver nginx et réactiver Apache comme ceci :

    sudo service nginx stop
    sudo service apache2 start

Et si tu veux faire l'opération inverse pour accéder de nouveau à ton domaine virtuel :

    sudo service apache2 stop
    sudo service nginx start

Voilà, c'est juste en attendant d'ajouter comment configurer nginx pour PHP !

[... A COMPLETER ...]


## Déploiement réel

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
voire bien plus pour certains noms de domaine prisés... Pour le fun et pour te donner une
idée des prix que ça peut atteindre (capture d'une page de commande de domaine chez OVH) :

![prix noms de domaine](https://github.com/bhubr/deploy-projects/raw/master/img/prix-noms-de-domaine.png)

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


[... A COMPLETER ...]
