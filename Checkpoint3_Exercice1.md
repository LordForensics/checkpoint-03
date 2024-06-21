# Exercice 1 : Manipulations pratiques sur VM Windows (temps estimé : 1h30)

Pour cet exercice tu as besoin de la VM SRVWIN01.

## Partie 1 : Gestion des utilisateurs

L'utilisateur Kelly Rhameur a quitté l'entreprise.
Elle est remplacée par Lionel Lemarchand

Q.1.1.1 Créer l'utilisateur Lionel Lemarchand avec les même attribut de société que Kelly Rhameur.

![Ex](/attachment/Ex_1_1_1.PNG)

Dans l'OU `DirectionDesRessourcesHumaines` où se trouve Kelly Rhameur

Clic-droit sur `Kelly.Rhameur` puis `Copy...` et on complète les champs :
* First Name : Lionel
* Last Name : Lemarchand
* Full Name : Lionel.Lemarchand
* User logon.name : Lionel.Lemarchand

`Next`

* Password : Azerty1*
* On laisse décocher les options

`Finish`

On complète dans le profil utilisateur ensuite les champs 
* Général > E-mail : Lionel.Lemarchand@TSSR.LAN
* Organization > Job Title : Directeur des Ressources Humaines
* Organization > Direct reports : Camille.Martin

`Apply` puis `OK`

Q.1.1.2 Créer une OU DeactivatedUsers et déplace le compte désactivé de Kelly Rhameur dedans.

Clic-droit sur l'OU `LabUsers` > `New` > `Organizational Unit` : Name ( DeactivatedUsers) > `OK`

On désactive le compte : clic-droit sur le profil puis `Disable Account` > `Move...` dans l'OU fraichement créée DeactivatedUsers 

![Ex](/attachment/Ex_1_1_2.PNG)

Q.1.1.3 Modifier le groupe de l'OU dans laquelle était Kelly Rhameur en conséquence.

Clic-droit sur le groupe GrpUsersDirectionDesRessourcesHumaines > Onglet Members > profil Kelly.Rahmeur > `Remove` > `Yes` > `Apply` > `OK`

Lionel Lemarchand a été ajouté automatiquement au groupe à la création du profil

![Ex](/attachment/Ex_1_1_3.PNG)

Q.1.1.4 Créer le dossier Individuel du nouvel utilisateur et archive celui de Kelly Rhameur en le suffixant par -ARCHIVE.

Dans la partition Dossier individuel (F:), on crée le dossier `lionel.lemarchand` et on modifie le dossier `kelly.rhameur` en `kelly.rhameur-ARCHIVE`

![Ex](/attachment/Ex_1_1_4.PNG)


## Partie 2 : Restriction utilisateurs

Q.1.2.1 Faire en sorte que l'utilisateur Gabriel Ghul ne puisse se connecter que du lundi au vendredi, de 7h à 17h.

Sur le profil de Gabriel Ghul dans l'OU `DirectionFinanciere` `Finance` > Onglet `Account` > `Logon Hours...`

On modifie les heures pour qu'il ne puisse pas se connecter en dehors de la plage > `OK` > `Apply` > `OK`

![Ex](/attachment/Ex_1_2_1.PNG)

Q.1.2.2 De même, bloquer sa connexion au seul ordinateur CLIENT01.

Sur le profil de Gabriel Ghul dans l'OU `DirectionFinanciere` `Finance` > Onglet `Account` > `Logon On To...`

On coche `The folowing computers` > `Computer Name` : CLIENT01 > `Add` > `OK` > `Apply` > `OK`

![Ex](/attachment/Ex_1_2_2.PNG)

Q.1.2.3 Mettre en place une stratégie de mot de passe pour durcir les comptes des utilisateurs de l'OU LabUsers.

`Server Manager` > `Tools` > `Group Policy Management` > clic-droit sur `LabUsers` > `Create a GPO in this Domain, and link it here...`

On la nomme avec un nom explicite `Manage_Hard_Password_Users` > `OK`

Sur la GPO > Onglet `Settings` > clic-droit sur la fenêtre > `Edit` 

Group Policy Managment Editor > `Computer Configuration` > `Policies` > `Windows Settings` > `Security Policies` > `Account Policies` > `Password Policy`

On modifie `Minimum password length` > `Defin this policy setting` coché > `Password must be at least 14 characters` > `OK` > `Refresh` > La GPO est en place.

On désactive la partie User Configuration sur cette GPO  `Edit` > `Properties` > `Disable User Configuration settings` > `Yes` > `Apply` > `OK`

![Ex](/attachment/Ex_1_2_3.PNG)


## Partie 3 : Lecteurs réseaux

Q.1.3.1 Créer une GPO Drive-Mount qui monte les lecteurs E: et F: sur les clients.

`Server Manager` > `Tools` > `Group Policy Management` > clic-droit sur `LabComputers` > `Create a GPO in this Domain, and link it here...`

On la nomme avec un nom explicite `Drive_Mount_E_F_Computers` > `OK`

Sur la GPO > Onglet `Settings` > clic-droit sur la fenêtre > `Edit` 

Group Policy Managment Editor > `User Configuration` > `Preferences` > `Windows Settings` > `Drive Maps` > `New` > `Mapped Drive`

`Drive Letter` > `Existing` > `E` > `Apply` > `OK`

![Ex](/attachment/Ex_1_3_1.PNG)

On désactive la partie Computer Configuration sur cette GPO  `Edit` > `Properties` > `Disable Computer Configuration settings` > `Yes` > `Apply` > `OK`

![Ex](/attachment/Ex_1_3_1_2.PNG)

