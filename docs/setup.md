# Setting up active directory from scratch

## 1. Installation de la VM

- Choix de VMWare Pro for Windows.

- Installation de Windows Server 2022.

## 2. Configuration réseau

- Donner une IP fixe au server :

    Il faut trouver l'adresse IP de sorte a ce qu'elle soit sur le même réseau que la passerelle par defaut. On trouve la Gateaway adress dans les menu : 192.168.29.2.

    - IP : 192.168.29.10 (Sur le même reseau que la carte ethernet VMWare NAT)
    - Masque ss reseau : 255.255.255.0
    - Passerelle par defaut : 192.168.29.2
    - Server DNS : 127.0.0.1 (adresse loopback)

    Redemarrer le serveur pour appliquer les modifs.

## 3. Configuration Rôles et Features

- Choix des rôles du serveur :
    - Active Directory Domain Services :
        - Gerer les utilisateurs
        - Gerer les ordinateurs du réseau
        - Gerer les mots de passe
        - Gerer les GPOs (politiques de sécurité)
        - Authentifier tout le monde sur le réseau

    - DNS Server

- Features :
    - Group Policy Management (GPO):
        - Dicte les regles de chaque machines connectés.

## 4. Domain Controller

Une fois installé, on promote le serveur to a domain controller pour :

- Créer le domaine et son compte admin (un domaine est une bulle regroupant les users, machines, serveurs, GPO et est geré par un seul serveur)
- Créer la base de donnée AD (Active Directory)
- Active le DNS
- On donne le nom lab.local

    - On installe et on redemarre
    - Les roles et features ont bien été appliqué !


## 5. Organizationnal Units (OUs)
Les OUs sont des dossiers ou on peut organiser les objets (machines, user, groups, servers etc...)

On ouvre, sur le serveur, l'application "Active directory users and computer" et on ajoute un nouveau objet Organizational Units a partir de notre domain controller.

<!-- 

## X. Jointure du client
- Configuration DNS
- Join domain : lab.local

-->