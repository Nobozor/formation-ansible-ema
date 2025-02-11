# Exercices Ansible

## Exercice 1

1. Démarrez la VM Ubuntu depuis le répertoire `atelier-01`.
```bash
cd Atelier-01
vagrant up
```
2. Connectez-vous à cette VM.
```bash
vagrant ssh ubuntu
```
3. Rafraîchissez les informations sur les paquets.
```bash
sudo apt update
```
4. Recherchez le paquet Ansible avec les options qui vont bien.

```bash
sudo apt search ansible
```
5. Installez le paquet officiel fourni par la distribution.
```bash
sudo apt install ansible
```
6. Vérifiez si l’installation s’est bien déroulée.
```bash
ansible --version
```

7. Notez la version d’Ansible.
```bash
ansible 2.10.8
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
```

8. Déconnectez-vous et supprimez la VM.
```bash
exit
vagrant destroy -f
```
## Exercice 2

Répétez l’exercice précédent en configurant un dépôt PPA (Personal Package Archive) pour Ansible :
```bash
$ sudo apt-add-repository ppa:ansible/ansible
```
Notez la version fournie par ce dépôt tiers et comparez avec la version officielle de l’exercice précédent.

```bash
ansible [core 2.17.8]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True
``` 

Version plus récente et plus complète (jinja, libyaml)
## Exercice 3

Lancez une VM Rocky Linux et installez Ansible en utilisant PIP et Virtualenv.

```bash
sudo dnf install python3
python3 -m venv .venv
source .venv/bin/activate
pip3 install ansible
```  
Résultat :  
```bash
ansible [core 2.15.13]
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/vagrant/.venv/lib64/python3.9/site-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/vagrant/.venv/bin/ansible
  python version = 3.9.21 (main, Dec  5 2024, 00:00:00) [GCC 11.5.0 20240719 (Red Hat 11.5.0-2)] (/home/vagrant/.venv/bin/python3)
  jinja version = 3.1.5
  libyaml = True
```
Important :  
Notez bien que contrairement à Debian, le paquet python3-venv n’est pas nécessaire ici, étant donné que Virtualenv fait partie des modules standard de Python dans cette distribution.