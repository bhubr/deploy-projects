# Mise en ligne d'un projet web

Ce document résume ce qu'il faut connaître pour mettre en ligne une application web.

La première partie portera sur des notions de réseau : les adresses IP et le DNS.

La seconde partie abordera la configuration d'un serveur Linux de type VPS (*Virtual Private Server* ou serveur privé virtuel), jusqu'à l'installation du serveur web Nginx.

La troisième partie abordera plus spécifiquement la mise en ligne d'un projet basé sur la "stack" Node.js + React.

## 1. Notions de réseau

### Internet Protocol et les adresses IP

Le terme de *protocole* t'est déjà familier, et tu en connais déjà un exemple : le protocole HTTP, qui régit les échanges entre un serveur web et un client (navigateur par exemple).

Il faut savoir que les protocoles réseau sur lesquels se base Internet sont organisés en couches ou en [piles](https://fr.wikipedia.org/wiki/Pile_de_protocoles) : un protocole peut reposer sur un autre protocole (tout comme, sur un ordinateur, une application s'appuie sur des librairies, qui elles-mêmes s'appuient sur des fonctionnalités du système d'exploitation).

Les protocoles tels qu'HTTP, FTP, IMAP, etc. reposent sur deux autres protocoles : TCP et IP (auquel on fait souvent référence par TCP/IP). IP signifie *Internet Protocol*. Il s'occupe notamment du routage des informations entre deux points du réseau. TCP signifie *Transmission Control Protocol* et s'occupe du transport des données, intégrant des mécanismes de contrôle et de correction d'erreurs.

Sur Internet, chaque ordinateur connecté au réseau dispose d'une adresse IP :

![Internet Protocol](https://raw.githubusercontent.com/bhubr/deploy-projects/master/img/internet-protocol.svg)