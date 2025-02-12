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
ssh-keygen
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```
- Installez Ansible.
```bash
sudo apt update && sudo apt install python3-venv python3-pip -y
python3 -m venv ansible-env
source ansible-env/bin/activate
pip install ansible
```
- Envoyez un premier ping Ansible sans configuration.
```bash
ansible all -i target01,target02,target03 -u vagrant -m ping
```
- Créez un répertoire de projet ~/monprojet.
```bash
mkdir ~/monprojet
cd ~/monprojet
```
- Créez un fichier vide ansible.cfg dans ce répertoire.
```bash
touch ansible.cfg
``` 
- Vérifiez si ce fichier est bien pris en compte par Ansible.
```bash
(ansible-env) vagrant@control:~/monprojet$ ansible --version
ansible [core 2.17.8]
  config file = /home/vagrant/monprojet/ansible.cfg
```
- Spécifiez un inventaire nommé hosts.
```bash
(ansible-env) vagrant@control:~/monprojet$ ansible-inventory --list -i hosts
{
    "_meta": {
        "hostvars": {
            "target01": {
                "ansible_python_interpreter": "/usr/bin/python3",
                "ansible_user": "vagrant"
            },
            "target02": {
                "ansible_python_interpreter": "/usr/bin/python3",
                "ansible_user": "vagrant"
            },
            "target03": {
                "ansible_python_interpreter": "/usr/bin/python3",
                "ansible_user": "vagrant"
            }
        }
    },
    "all": {
        "children": [
            "ungrouped",
            "testing"
        ]
    },
    "testing": {
        "hosts": [
            "target01",
            "target02",
            "target03"
        ]
    }
}
```
- Activez la journalisation dans ~/journal/ansible.log.
```bash
mkdir ~/journal
# Fichier ansible.cfg :
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```
- Testez la journalisation.
```bash
(ansible-env) vagrant@control:~/monprojet$ ansible all -m ping
(ansible-env) vagrant@control:~/monprojet$ cat ../journal/ansible.log 
2025-02-12 10:25:36,361 p=3830 u=vagrant n=ansible | target02 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:25:36,369 p=3830 u=vagrant n=ansible | target03 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:25:36,377 p=3830 u=vagrant n=ansible | target01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
- Créez un groupe [testlab] avec vos trois Target Hosts.
```bash
[testlab]
target01
target02
target03
```
- Définissez explicitement l’utilisateur vagrant pour la connexion à vos cibles.
```bash
# /etc/ansible/hosts
[testlab]
target01
target02
target03
[testlab:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
``` 
- Envoyez un ping Ansible vers le groupe de machines [all].
```bash
(ansible-env) vagrant@control:~/monprojet$ ansible all -m ping
```
- Définissez l’élévation des droits pour l’utilisateur vagrant sur les Target Hosts.
```bash
# /etc/ansible/hosts
[testlab]
target01
target02
target03
[testlab:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=true
```
- Affichez la première ligne du fichier /etc/shadow sur tous les Target Hosts.
```bash
(ansible-env) vagrant@control:~/monprojet$ ansible all -a "head -n 1 /etc/shadow"
target03 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target01 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
```
- Quittez le Control Host et supprimez toutes les VM de l’atelier.
```bash
exit
vagrant destroy -f
```