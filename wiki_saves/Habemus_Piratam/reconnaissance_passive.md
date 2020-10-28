---
title: Reconnaissance Passive
description: L'art de récolter furtivement et sans action invasive de l'information sur une cible.
published: true
date: 2020-09-27T23:09:16.795Z
tags: 
editor: undefined
dateCreated: 2020-09-22T07:42:57.932Z
---

# Network Sniffing

> Le sniffing est l'action d'écouter, de regrouper et d'analyser un ensemble de trames entrant et sortant d'une carte réseau. Plus généralement, c'est le fait d'intercepter du traffic, qu'il nous soit destiné ou non.
{.is-info}

L'objectif des outils ci-dessous est de rendre compréhensible et ergonomique le traitement des trames capturées, qui ne sont au départ qu'une suite de bits.

> Pour pouvoir écouter toutes les trames il vous faut un prérequis essentiel :
> 
> - Une carte réseau compatible avec le mode "***Promiscuous***".

> Le mode **Promiscuous** est une configuration de la carte réseau, qui permet à celle-ci d'accepter tous les paquets qu'elle voit transiter, **même si ceux-ci ne lui sont pas adressés.** (*Ne prends pas en compte le champs "Destination" de la trame*).
{.is-info}

![71ygjm+zmil._ac_sl1500_.jpg](/uploads/reconnaissance_passive/71ygjm+zmil._ac_sl1500_.jpg){.align-center}
Ci-dessous une liste de cartes réseau USB compatibles : 

`Alfa AWUS1900`
`Alfa AWUS036ACH`
`TRENDnet TEW 809UB`
`ASUS USB AC68`
`TP LINK Archer T9UH`

Pour pouvoir utiliser une carte réseau sans fil USB avec Kali Linux, il est nécessaire d'installer le driver rtl
Pour paramétrer une carte **SANS** adresse IP, il est important de supprimer l'utilitaire de réseau **NetworkManager** : `apt-get remove NetworkManager`

Après un redémarrage, il faut à présent modifier le fichier de configuration réseau : `sudo nano /etc/network/interfaces`

Rajoutez les lignes suivantes  : 

<kbd>allow-hotplug [Interface]
iface [Interface] inet manual
pre-up ifconfig $IFACE up
post-down ifconfig $IFACE down</kbd>

![vmware_1meimvnafj.png](/uploads/reconnaissance_passive/vmware_1meimvnafj.png){.align-center}

Il suffit ensuite de relancer la carte pour que les modifications soient prisent en compte : 

`ifdown [Carte_Réseau]`
`ifup [Carte_Réseau]`


## WireShark
![firefox_9r5s49u9em.png](/uploads/reconnaissance_passive/firefox_9r5s49u9em.png){.align-center}

> Wireshark est l’analyseur de protocole de réseau le plus utilisé au monde. Il vous permet de voir ce qui se passe sur le réseau à un niveau microscopique et constitue la norme de facto dans de nombreuses entreprises commerciales et à but non lucratif, agences gouvernementales et établissements d'enseignement. Le développement de Wireshark prospère grâce aux contributions volontaires d'experts du monde entier et est la continuation d'un projet lancé par Gerald Combs en 1998.
> 
> Source : https://www.wireshark.org/
{.is-info}


Une fois que vous aurez vérifié que votre carte ne possède **aucune adresse IP** et ne peut donc pas émettre de trames sur le réseau, vous pourrez lancer **WireShark**.












## TCPDump
(à venir)
# OSINT
## WhoIs
(à venir)
## Google Dorks
(à venir)
## NetCraft
(à venir)
## ReconNg
(à venir)
## Open Source Code
(à venir)
## Shodan
(à venir)
## Security Header Scanner
(à venir)
## SSL Serveur Test
(à venir)
## PasteBin
(à venir)
# Mail Information
## The Harvester
(à venir)
## Social Media
(à venir)
## LinkedIn2Username
(à venir)
## Twitter Wordlist
(à venir)