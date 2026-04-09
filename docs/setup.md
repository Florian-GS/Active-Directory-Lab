# Setting up active directory from scratch

## 1. Installation de la VM

- Choix de VMWare Pro for Windows.

- Installation de Windows Server 2022.

![alt text](<../screenshots/1.Installation de l'iso de la WM.png>)

![alt text](<../screenshots/2.Windows Server installé.png>)

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
    
![alt text](<../screenshots/3.Nom de domaine Login.png>)

## 5. Organizationnal Units (OUs)
Les OUs sont des dossiers ou on peut organiser les objets (machines, user, groups, servers etc...)

On ouvre, sur le serveur, l'application "Active directory users and computer" et on ajoute de nouveaux objets Organizational Units a partir de notre domain controller :


- Utilisateurs            ← OU
    - Admins              ← OU
        - alice           ← User
    - IT                  ← OU
        - bob             ← User
    - RH                  ← OU
        - carol           ← User

- Ordinateurs             ← OU
    - Users-Post          ← OU
        - WIN11-01        ← Computer

- Groupes                 ← OU
    - GRP-Admins          ← Groupe
    - GRP-IT    
    - GRP-RH     

On met le Group Scope des groupes en Global et Group Type Security
On dit que alice appartient Domain admin pour que windows la concidere comme une admin
L'ajout des computers peut se faire apres. Un ordi connecté au domain apparaitra dans la liste, il suffira de la deplacer à l'endroit desiré.

![alt text](<../screenshots/4. Organigramme OUs.png>)

## 6. Group Policy Objects (GPOs)
Les GPOs sont gerés par les admins pour controler les parametres des machines et users connectées.
Il faut installer le rôle Group Policy Management Console (GPMC) qui est d'ailleurs proposé par defaut a l'installation de Active Directory Domain Services (AD DS).

On ouvre l'app Group Policy Management Editor.

A chaque création d'un GPO, il existe 2 Types de GPO par defaut :
- Computer Configuration (s'appliquent peu importe l'user).
    - Policies          ← Ne peuvent pas etre changé par l'user
    - Preferences       ← Peuvent  etre changé par l'user
- User Configuration
    - Policies
    - Preferences


    ### 6.1 Password Policy

    On veut créer un GPO a l'interieur de notre domaine : Password Policy qu'on va venir editer avec le Group Policy Management editor. Il s'agit d'un Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Account Policy -> Password policy; et changer ce qui nous interesse.
    
    ![alt text](<../screenshots/5. Password length au moins 4 caracteres .png>)

## 7. Mappage des disques en fonction des groupes et users

On veut que lorsque les users se connectent a leurs compte sur n'importe quelles machines ils ont leur disque perso et de groupe.
Pour ça on :

- Créer les dossiers physiques sur le serveur.
- Les partage

Ils sont donc visible sur le réseau.

- On créer une nouvelle GPO appelé Drive Mapping. On l'edit et dans User Configuration -> Preferences -> Windows Settings -> Drive Maps :
- New mapped Drive
- on créer autant de disque que le nombre de groupe (1 disque pour chaque groupe)
- On créer un disque pour les utilisateurs en utilisant la varibale %USERNAME% pour que l'user accede a son propre disque lors de la connection (exemple : si bob se connecte, le disque de bob apparait).

![alt text](<../screenshots/7. Configuration des drives dans la GPO.png>)


- Une fois parametré on installe un nouvel ordinateur :
    - Nouvelle VM sous Windows 11, on configure les parametre reseau :
        - IP address  : 192.168.29.20 (meme reseau que le serveur mais hote different)
        - Subnet mask : 255.255.255.0
        - Gateway     : 192.168.29.2        
        - DNS         : 192.168.29.10  (IP du serveur)
    - On ping le server pour etre sur que les deux machines sont reliés :

![alt text](<../screenshots/8. Communication entre une machine et le server.png>)


- On joint le domain et on redemarre pour appliquer les changements

![alt text](<../screenshots/9. Attribution du domaine au pc hote.png>)

- On se connecte avec les log de bob (it)
    - On a bien son disque perso et son disque de groupe

![alt text](<../screenshots/10. Les deux disques de BOB.png>)

- Attention, sur le serveur dans les dossiers de groupe partagés, il faut bien ajouter le groupe IT et le groupe RH de notre domain.


![alt text](<../screenshots/11. Ajout du groupe IT au dossier grp-it sur le server.png>)

- On se connecte avec les log de Carol (rh)
- On a bien son disque perso et son disque de groupe


<!-- 

## X. Jointure du client
- Configuration DNS
- Join domain : lab.local

-->