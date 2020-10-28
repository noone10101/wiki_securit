---
title: Challenge 1
description: 
published: true
date: 2020-01-13T13:09:27.935Z
tags: 
editor: undefined
dateCreated: 2019-11-03T22:46:46.712Z
---

# Social Hacking

Dans ce challenge, il va falloir retrouver le flag en traversant un ou plusieurs réseau social du groupe Secur'IT.

Tout d'abord, on peut comprendre que quel que soit l'endroit ou se trouve le flag, c'est un endroit "public".

**Un indice** : Nous avons un  **"#"** au début du challenge. On sait qque les hastags sont généralement utilisées sur les plates-formes de réseaux sociaux, la ou tout le monde peut accéder. 

Maintenant, il faut se poser la question, "Sur quel réseau social dois-je commencer à chercher?".

On peut utiliser des outils en ligne pour réussir ce challenge. 

Donc, on va commencer à chercher dans les barres de recherches facebook, instagram, discord, etc... Mais, il faut savoir qu'il y a également les plateformes comme "Linkdn" ou bien "Twitter". Ce sont des plates-formes un peu moins populaire mais qui existent.

<p style="text-align:left">En regardant sur Twitter et en ayant un peu de jugeote, ils vont aller chercher "#SecurIT" dans la barre de recherche twitter (ou bien Linkdn on peut adapter le challenge à notre souhait). Ils vont tomber sur un tweet de la page Secur'IT disant "Hey, tu chercherai pas un flag par hasard ? #SecurITCTF" avec un lien. En cliquant sur ce lien, ils vont tomber sur un QR Code. En scannant ce QR Code avec des outils en lignes, ils vont obtenir une URL comme "https://wiki.securit.edu.esiee.fr/SocialHacking/". Quand ils iront visiter cette URL, il n'y aura qu'un message dessus indiquant "Tu pensais que c'était si simple? :P". En regardant les codes sources de la page avec le fameux "Clique droit -> Code Source de la page", ils tomberont sur notre flag en commentaire dans le script de la page!</p>

-----

