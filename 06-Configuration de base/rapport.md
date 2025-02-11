## 1. Exercice

Le moment est venu de faire un petit exercice récapitulatif. Placez-vous dans le répertoire du sixième atelier pratique :
```
$ cd ~/formation-ansible/atelier-06
```

Voici les quatre machines virtuelles Ubuntu 22.04 de cet atelier :
```
Machine virtuelle 	Adresse IP
control 	192.168.56.10
target01 	192.168.56.20
target02 	192.168.56.30
target03 	192.168.56.40
```

Démarrez les VM :

```
$ vagrant up

```
Connectez-vous au Control Host :

```
$ vagrant ssh control

```
- Éditez /etc/hosts de manière à ce que les Target Hosts soient joignables par leur nom d’hôte simple.
```bash
192.168.56.10  control.sandbox.lan    control
192.168.56.20  target01.sandbox.lan   target01
192.168.56.30  target02.sandbox.lan   target02
192.168.56.40  target03.sandbox.lan   target03
``` 
- Configurez l’authentification par clé SSH avec les trois Target Hosts.
```bash
vagrant@control:~$ ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts

```
- Installez Ansible.
```bash

```
- Envoyez un premier ping Ansible sans configuration.
- Créez un répertoire de projet ~/monprojet.
- Créez un fichier vide ansible.cfg dans ce répertoire.
- Vérifiez si ce fichier est bien pris en compte par Ansible.
- Spécifiez un inventaire nommé hosts.
- Activez la journalisation dans ~/journal/ansible.log.
- Testez la journalisation.
- Créez un groupe [testlab] avec vos trois Target Hosts.
- Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles.
- Envoyez un ping Ansible vers le groupe de machines [all].
- Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts.
- Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts.
- Quittez le Control Host et supprimez toutes les VM de l’atelier.
