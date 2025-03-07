Exercice 2 - VM Linux

Partie 1 - Gestion des utilisateurs

Q.2.1.1 
On commence par faire un apt update && apt upgrade -y

![image](https://github.com/user-attachments/assets/9275d7ba-559e-4433-8be2-642a13a3f9a2)
 
Q.2.1.2 
Pour ce compte on applique une politique de mot de passe fort 
Droits limités : Ne pas ajouter l'utilisateur au groupe sudo sauf si nécessaire.
Accès SSH restreint : Limiter l'accès SSH à cet utilisateur spécifique.
Authentification par clé SSH : Désactiver l'authentification par mot de passe.

Partie 2 - Configuration de SSH

Q.2.2.1 
 ![image](https://github.com/user-attachments/assets/ef5e933d-c5cb-4dda-a5ec-3ea7284b4058)
![image](https://github.com/user-attachments/assets/f1d210ad-8ce4-4cc9-b2a8-29d1803f9e92)

 
Q.2.2.2 
 ![image](https://github.com/user-attachments/assets/96b28a7e-285b-40ed-9299-bce3834a1b37)

 
![image](https://github.com/user-attachments/assets/3acb5d1e-ab09-4b9c-b921-4df23f31fae1)






Q.2.2.3 
 
![image](https://github.com/user-attachments/assets/f9cc3a0f-e505-4e4a-b312-3d35fbf4966e)

![image](https://github.com/user-attachments/assets/3a501a0b-415c-4551-bd75-495688fd1ae5)

 ![image](https://github.com/user-attachments/assets/af531314-52ac-4bf9-b726-1b65c1f3d6de)

![image](https://github.com/user-attachments/assets/d56a11e8-7135-4be9-93c8-ac05d6fe9adb)

 

 

Partie 3 - Analyse du stockage

Q.2.3.1 
 ![image](https://github.com/user-attachments/assets/83cd98a6-58a0-41b3-b0ea-54478b995406)

Q.2.3.2 
 ![image](https://github.com/user-attachments/assets/7ade82bd-73ad-4663-bf5a-ffd68c0d7d00)

Ce système utilise donc une combinaison de RAID logiciel (md0), LVM pour la gestion des volumes, et des partitions temporaires en tmpfs/devtmpfs.

Q.2.3.3 
 ![image](https://github.com/user-attachments/assets/739c7894-0293-49b4-8fd0-e1730768d6a0)

Q.2.3.4

Créer une partition sur le disque

fdisk /dev/sdc
n nouvelle partiton
Primaire
Type : 8e lvm
W valider

Créer le volume physique
pvcreate /dev/sdc1

Trouver le nom de la VG
vgdisplay pour savoir le nom  du VG

Ajouter au groupe de volumes
 vgextend cp3-vg /dev/sdc1

Créer le volume logique

lvcreate -L 2G -n backup_lv cp3-vg

Formater et monter automatiquement
sudo mkfs.ext4 /dev/cp3-vg/backup_lv sudo mkdir -p /var/lib/bareos/storage echo "/dev/cp3-vg/backup_lv /var/lib/bareos/storage ext4 defaults 0 2" | tee -a /etc/fstab sudo mount -a

Q.2.3.5 

 ![image](https://github.com/user-attachments/assets/790b9d89-2c85-41aa-9e02-42da6990d76e)



Partie 4 - Sauvegardes

Q.2.4.1 

ls -l /etc/bareos/

Explication des composants Bareos
bareos-dir (Director) : Gère les sauvegardes, planifications et restaurations.
bareos-sd (Storage Daemon) : Stocke et gère les fichiers sauvegardés.
bareos-fd (File Daemon) : Installe sur les machines clientes, envoie les fichiers au serveur.


Partie 5 - Filtrage et analyse réseau

Q.2.5.1
  ![image](https://github.com/user-attachments/assets/cf08a535-171e-4b11-8d1c-465221422826)

Q.2.5.2 

•  Trafic correspondant à des connexions déjà établies ou reliées (ct state established,related) 
•  Tout trafic sur l'interface loopback (iifname "lo") 
•  Connexions entrantes sur le port TCP 22 (SSH) 
•  Protocole ICMP (pings et autres messages de diagnostic réseau IPv4) 
•  Protocole ICMPv6 (version IPv6 des pings et messages de diagnostic réseau

Q.2.5.3 
D'après la sortie de nftables, les types de communications interdites sont:
1.	Trafic avec un état de connexion "INVALID" (ct state invalid drop)
2.	Tout autre trafic entrant qui ne correspond pas aux règles spécifiquement autorisées (en raison de la politique par défaut "policy drop" sur la chaîne d'entrée)

Q.2.5.4 
Déterminez d'abord l'interface réseau et l'adresse du réseau local :
ip a
ip route
 Ajouter les règles nécessaires pour autoriser Bareos
 
![image](https://github.com/user-attachments/assets/e4dfcb37-14e4-4545-8ea1-b139cfc4a3c2)
![image](https://github.com/user-attachments/assets/2df2a54c-f23f-4636-93d9-cd3eb8d9d149)

 ![image](https://github.com/user-attachments/assets/b1678ede-e6df-4faa-aa46-e1a7d77c4ae0)



 


Partie 6 - Analyse de logs

Q.2.6.1 
J’ai testé cette commande
grep "Failed password" /var/log/auth.log | tail -n 10 | awk '{print $1, $2, $3, $11}'
mais rien ne sort en résultats 
![image](https://github.com/user-attachments/assets/c98e87e3-fcfc-41bb-82a6-6a4eabe066b0)

 
Il y a plusieurs auth.log
ok : /24
nr : 
faux : 

