# Exercice 2 : Manipulations pratiques sur VM Linux (temps estimé : 2h30)
Pour cet exercice tu as besoin de la VM SRVLX01.

## Partie 1 : Gestion des utilisateurs

Q.2.1.1 Sur le serveur, créer un compte pour ton usage personnel.

En tant que root > adduser forensics > on complète les prompts

![Ex](/attachment/Ex_2_1_1.PNG)

Q.2.1.2 Quelles préconisations proposes-tu concernant ce compte ?

_Erreur : A revoir_

## Partie 2 : Configuration de SSH

Un serveur SSH est lancé sur le port par défaut.
Il est possible de s'y connecter avec n'importe quel compte, y compris le compte root.

Q.2.2.1 Désactiver complètement l'accès à distance de l'utilisateur root.

Il faut modifier le fichier `/etc/ssh/sshd_config` > on cherche la ligne `PermitRootLogin` > on décommente la ligne et on modifie `PermitRootLogin no` > CTRL + O pour enregistrer > CTRL + X pour quitter

![Ex](/attachment/Ex_2_2_1.PNG)

Q.2.2.2 Autoriser l'accès à distance à ton compte personnel uniquement.

Dans le même fichier, on ajoute la ligne `AllowUsers forensics`

![Ex](/attachment/Ex_2_2_2.PNG)

Q.2.2.3 Mettre en place une authentification par clé valide et désactiver l'authentification par mot de passe

Dans le même fichier, on cherche la ligne `PubkeyAuthentication yes` > on décommente et on cherche la ligne `PasswordAuthentication` > on décommente et on modifie par `PasswordAuthentication no`

![Ex](/attachment/Ex_2_2_3.PNG)

## Partie 3 : Analyse du stockage

Q.2.3.1 Quels sont les systèmes de fichiers actuellement montés ?

Avec la commande `lsblk -f`, on peut s'apercevoir que nous avons :
* `sad1` est en linux
* `md0p1` est en ext2
* `md0p5` `cp3--vg-root` en ext4
* `md0p5` `cp3--vg-swap_1` en swap

![Ex](/attachment/Ex_2_3_1.PNG)

Q.2.3.2 Quel type de système de stockage ils utilisent ?

Avec la commande `lsblk`, nous avons un RAID1 sur `sda1` qui compose le Physical volume `md0`, lui-même découpé en Volume Group `md0p5`

![Ex](/attachment/Ex_2_3_2.PNG)

Q.2.3.3 Ajouter un nouveau disque de 8,00 Gio au serveur et réparer le volume RAID

On ajoute un disque de 8Go dans VirtualBox

![Ex](/attachment/Ex_2_3_3_1.PNG)

On vérifie la présence du disque au démarrage avec `lsblk`, il apparait en tant que `sdb`

On partitionne le disque avec la commande `fdisk /dev/sdb`

![Ex](/attachment/Ex_2_3_3_2.PNG)

On modifies le type de partition en RAID, `t` code hexa `fd`

on sauvegarde avec `w`

![Ex](/attachment/Ex_2_3_3_3.PNG)

On télécharge le paquet mdadm avec `apt install mdadm` si celui-ci n'est pas installé.

on peut voir avec la commande `mdadm --detail /dev/md0` que le RAID est en status `degraded`

![Ex](/attachment/Ex_2_3_3_4.PNG)

On va par conséquent ajouter la partition que nous venons de configurer avec `mdadm --manage /dev/md0 --add /dev/sdb1`

![Ex](/attachment/Ex_2_3_3_5.PNG)

Nous avons désormais un status `recovering`

Après un petit laps de temps, le RAID est reconstruit, le status repasse en `clean`

![Ex](/attachment/Ex_2_3_3_6.PNG)

![Ex](/attachment/Ex_2_3_3_7.PNG)

Q.2.3.4 Ajouter un nouveau volume logique LVM de 2 Gio qui servira à héberger des sauvegardes. Ce volume doit être monté automatiquement à chaque démarrage dans l'emplacement par défaut : /var/lib/bareos/storage.

Avec la commande `/sbin /lvdisplay` nous pouvons voir que nous avons actuellement 2 volumes logiques

![Ex](/attachment/Ex_2_3_4_1.PNG)

Nous allons ajouter un nouveau volume avec la commande `lvcreate -n Sauvegarde -L 2g  cp3-vg`

![Ex](/attachment/Ex_2_3_4_2.PNG)

On rend la partition créée disponible avec `lvm lvchange -ay /dev/cp3-vg/Sauvegarde`

Le chemin `/var/lib/bareos/storage` existe déjà

On monte la partition dans l'emplacement avec `mount /dev/cp3-vg/Sauvegarde /var/lib/bareos/storage`

![Ex](/attachment/Ex_2_3_4_3.PNG)

_Erreur : A revoir_

Q.2.3.5 Combien d'espace disponible reste-t-il dans le groupe de volume ?

En toute logique, sur notre PV `md0p5` de 7.5G, nous avons :
* 2.8Go pour le VG root
* 976Mo pour le VG swap
* 2Go pour le VG Sauveragde

Il devrait nous rester 4Go disponible...

_Erreur : Réponse incomplète_


## Partie 4 : Sauvegardes

Le logiciel bareos est installé sur le serveur.
Les composants bareos-dir, bareos-sd et bareos-fd sont installés avec une configuration par défaut.

Q.2.4.1 Expliquer succinctement les rôles respectifs des 3 composants bareos installés sur la VM.

_Erreur : A revoir_


## Partie 5 : Filtrage et analyse réseau

Q.2.5.1 Quelles sont actuellement les règles appliquées sur Netfilter ?

On cherche les tables disponibles avec `nft list tables`, puis on va voir les règles établies avec `nft list table inet inet_filter_table`

![Ex](/attachment/Ex_2_3_5_1.PNG)

Q.2.5.2 Quels types de communications sont autorisées ?

On autorise donc :
* l'accés via SSH sur le port 22
* Le protocole ICMP sur l'IPv4
* 

_Erreur : A revoir_

Q.2.5.3 Quels types sont interdit ?

_Erreur : A revoir_

Q.2.5.4 Sur nftables, ajouter les règles nécessaires pour autoriser bareos à communiquer avec les clients bareos potentiellement présents sur l'ensemble des machines du réseau local sur lequel se trouve le serveur.

Rappel : Bareos utilise les ports TCP 9101 à 9103 pour la communication entre ses différents composants.

_Erreur : A revoir_


## Partie 6 : Analyse de logs

Q.2.6.1 Lister les 10 derniers échecs de connexion ayant eu lieu sur le serveur en indiquant pour chacun :

`journalctl | grep authentication`

La date et l'heure de la tentative
L'adresse IP de la machine ayant fait la tentative

![Ex](/attachment/Ex_2_3_6_1.PNG)