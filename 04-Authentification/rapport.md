## Exercice

Placez-vous dans le répertoire du troisième atelier pratique :
```bash
$ cd ~/formation-ansible/atelier-03
```
Voici les quatre machines virtuelles Ubuntu 22.04 de cet atelier :  
Machine virtuelle 	Adresse IP  
control 	192.168.56.10  
target01 	192.168.56.20  
target02 	192.168.56.30  
target03 	192.168.56.40  

Démarrez les VM :
```bash
$ vagrant up
```
Connectez-vous au Control Host :
```
$ vagrant ssh control
```
Ansible est déjà installé sur cette machine :
```bash
$ type ansible
ansible is /usr/bin/ansible
```
Faites le nécessaire pour réussir un ping Ansible comme ceci :
```yaml
$ ansible all -i target01,target02,target03 -m ping
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

```

Conection sur le node control :   
```bash
vagrant ssh control
```
Je modifie mon hosts :
```bash
127.0.0.1      localhost.localdomain  localhost
192.168.56.10  control.sandbox.lan    control
192.168.56.20  target01.sandbox.lan   target01
192.168.56.30  target02.sandbox.lan   target02
192.168.56.40  target03.sandbox.lan   target03
```
```bash
vagrant@control:~$ ssh-keyscan -t rsa target01 target02 target03 >> .ssh/known_hosts
# target03:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
# target01:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
# target02:22 SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.10
``` 

```bash
ssh-keygen
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03

ansible all -i target01,target02,target03 -u vagrant -m ping
``` 
Résultat : 
```bash
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
``` 

