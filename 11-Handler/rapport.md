# Exercice

Placez-vous dans le répertoire du douzième atelier pratique :
```bash
$ cd ~/formation-ansible/atelier-12

Voici les quatre machines virtuelles de cet atelier :
Machine virtuelle 	Adresse IP
control 	192.168.56.10
target01 	192.168.56.20
target02 	192.168.56.30
target03 	192.168.56.40
```

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
L’inventaire des machines définit un groupe [redhat] avec les trois Target Hosts :

```yaml
$ cat inventory 
[redhat]
target01
target02
target03

[redhat:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=yes

```
Écrivez un playbook chrony.yml qui assure la synchronisation NTP de tous vos Target Hosts :

    Installez le paquet chrony.
    Activez et démarrez le service chronyd correspondant.
    Jetez un œil sur le fichier de configuration /etc/chrony.conf fourni par défaut.
    Installez une configuration personnalisée (cf. ci-dessous).
    Prenez en compte cette nouvelle configuration.
    Vérifiez la syntaxe correcte de votre playbook chrony.yml.
    Vérifiez l’idempotence de votre playbook.

Voici la configuration à installer :

```bash
# /etc/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

## Solution Playbook :
```yaml
---
- name: Configure NTP with Chrony
  hosts: redhat
  become: yes
  tasks:
    - name: Install chrony package
      ansible.builtin.package:
        name: chrony
        state: present

    - name: Enable and start chronyd service
      ansible.builtin.service:
        name: chronyd
        enabled: yes
        state: started

    - name: Check default chrony configuration
      ansible.builtin.command: cat /etc/chrony.conf
      register: default_config
      changed_when: false

    - name: Display default chrony configuration
      ansible.builtin.debug:
        var: default_config.stdout_lines

    - name: Install custom chrony configuration
      ansible.builtin.copy:
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: '0644'
      notify: Restart chronyd

    - name: Ensure chronyd is reloaded with new configuration
      ansible.builtin.meta: flush_handlers

  handlers:
    - name: Restart chronyd
      ansible.builtin.service:
        name: chronyd
        state: restarted
```

## Résultat du playbook au 1er lancement : 

```bash
[vagrant@control playbooks]$ ansible-playbook chrony.yml 

PLAY [Configure NTP with Chrony] *************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Install chrony package] ****************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Enable and start chronyd service] ******************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Check default chrony configuration] ****************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Display default chrony configuration] **************************************************************************************************************************************************************************
ok: [target01] => {
    "default_config.stdout_lines": [
        "# Use public servers from the pool.ntp.org project.",
...........
        "#log measurements statistics tracking"
    ]
}
ok: [target02] => {
    "default_config.stdout_lines": [
        "# Use public servers from the pool.ntp.org project."
...........
        "#log measurements statistics tracking"
    ]
}
ok: [target03] => {
    "default_config.stdout_lines": [
        "# Use public servers from the pool.ntp.org project.",
...........
        "#log measurements statistics tracking"
    ]
}

TASK [Install custom chrony configuration] ***************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Ensure chronyd is reloaded with new configuration] *************************************************************************************************************************************************************

TASK [Ensure chronyd is reloaded with new configuration] *************************************************************************************************************************************************************

TASK [Ensure chronyd is reloaded with new configuration] *************************************************************************************************************************************************************

RUNNING HANDLER [Restart chronyd] ************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

PLAY RECAP ***********************************************************************************************************************************************************************************************************
target01                   : ok=7    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=7    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=7    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```