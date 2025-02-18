# Exercice

Placez-vous dans le répertoire du quatorzième atelier pratique :

```bash

$ cd ~/formation-ansible/atelier-14
```

Voici les quatre machines virtuelles de cet atelier :
Machine virtuelle 	Adresse IP
control 	192.168.56.10
target01 	192.168.56.20
target02 	192.168.56.30
target03 	192.168.56.40

Les VM tournent toutes sous Rocky Linux. Démarrez-les :

```bash

$ vagrant up
```

Connectez-vous au Control Host :

```bash

$ vagrant ssh control

```
L’environnement de cet atelier est préconfiguré et prêt à l’emploi :

    Ansible est installé sur le Control Host.
    Le fichier /etc/hosts du Control Host est correctement renseigné.
    L’authentification par clé SSH est établie sur les trois Target Hosts.
    Le répertoire du projet existe et contient une configuration de base et un inventaire.
    Direnv est installé et activé pour le projet.
    Le validateur de syntaxe yamllint est également disponible.

Rendez-vous dans le répertoire du projet :
```bash
$ cd ansible/projets/ema/
direnv: loading ~/ansible/projets/ema/.envrc
direnv: export +ANSIBLE_CONFIG
$ ls -l
total 8
-rw-r--r--. 1 vagrant vagrant  65 Sep 19 14:26 ansible.cfg
-rw-r--r--. 1 vagrant vagrant 128 Sep 19 14:26 inventory
drwxr-xr-x. 2 vagrant vagrant   6 Sep 19 14:26 playbooks
```

    Écrivez un playbook myvars1.yml qui affiche respectivement votre voiture et votre moto préférée en utilisant le module debug et deux variables mycar et mybike définies en tant que play vars. 
```yaml
            ---
        - name: Afficher voiture et moto préférées
        hosts: localhost
        vars:
            mycar: "Toyota"
            mybike: "Yamaha"
        tasks:
            - name: Afficher les variables
            debug:
                msg: "Ma voiture préférée est {{ mycar }} et ma moto préférée est {{ mybike }}."
```
    En utilisant les extra vars, remplacez successivement l’une et l’autre marque – puis les deux à la fois – avant d’exécuter le play.  
    Écrivez un playbook myvars2.yml qui fait essentiellement la même chose que myvars1.yml, mais en utilisant une tâche avec set_fact pour définir les deux variables.  
```yaml
---
- name: Afficher voiture et moto préférées avec set_fact
  hosts: localhost
  tasks:
    - name: Définir les variables
      set_fact:
        mycar: "Toyota"
        mybike: "Yamaha"

    - name: Afficher les variables
      debug:
        msg: "Ma voiture préférée est {{ mycar }} et ma moto préférée est {{ mybike }}."
```
    Là aussi, essayez de remplacer les deux variables en utilisant des extra vars avant l’exécution du play.  

    Écrivez un playbook myvars3.yml qui affiche le contenu des deux variables mycar et mybike mais sans les définir. Avant d’exécuter le playbook, définissez VW et BMW comme valeurs par défaut pour mycar et mybike pour tous les hôtes, en utilisant l’endroit approprié. Effectuez le nécessaire pour remplacer VW et BMW par Mercedes et Honda sur l’hôte target02.  
```yaml
[all:vars]
mycar=VW
mybike=BMW

[target02:vars]
mycar=Mercedes
mybike=Honda
```
```yaml
---
- name: Afficher voiture et moto préférées sans les définir
  hosts: all
  tasks:
    - name: Afficher les variables
      debug:
        msg: "Ma voiture préférée est {{ mycar }} et ma moto préférée est {{ mybike }}."

```

    Écrivez un playbook display_user.yml qui affiche un utilisateur et son mot de passe correspondant à l’aide des variables user et password. Ces deux variables devront être saisies de manière interactive pendant l’exécution du playbook. Les valeurs par défaut seront microlinux pour user et yatahongaga pour password. Le mot de passe ne devra pas s’afficher pendant la saisie.

```yaml
---
- name: Afficher utilisateur et mot de passe
  hosts: localhost
  vars_prompt:
    - name: "user"
      prompt: "Entrez le nom d'utilisateur"
      default: "microlinux"
      private: no

    - name: "password"
      prompt: "Entrez le mot de passe"
      default: "yatahongaga"
      private: yes

  tasks:
    - name: Afficher les informations
      debug:
        msg: "Utilisateur : {{ user }}, Mot de passe : {{ password }}"

```
Exemple de sortie : 
```bash

[vagrant@control playbooks]$ ansible-playbook display_user.yml 
Entrez le nom d'utilisateur [microlinux]: toto
Entrez le mot de passe [yatahongaga]: 

PLAY [Afficher utilisateur et mot de passe] **************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Afficher les informations] *************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Utilisateur : toto, Mot de passe : ffds"
}

PLAY RECAP ***********************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Quittez le Control Host :
```bash
$ exit
```

Supprimez toutes les VM :


```bash
$ vagrant destroy -f
```