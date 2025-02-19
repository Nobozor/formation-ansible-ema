# Exercice

Dans notre précédent exercice, nous avons mis en place la synchronisation NTP avec Chrony sur nos Target Hosts, en installant le fichier de configuration ci-dessous sur chacune des quatre cible
```bash
# chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
Écrivez un playbook chrony.yml qui installe un fichier de configuration personnalisé sur vos cibles. La première ligne de commentaire devra indiquer le chemin complet vers le fichier :
    Dans certains cas ce sera /etc/chrony/chrony.conf.
    Dans d’autres cas ce sera simplement /etc/chrony.conf.

> J'ai repris la base de mon playbook et mon fichier var de l'exercice précédent pour réaliser ce playbook.
Pour ce faire j'ai créer un template jinja pour ma configuration chrony :
```yaml
# {{ chrony_conf_path }}
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

Et mon playbook chrony.yml :
```yaml
---
- hosts: all

  tasks:

    - name: load vars
      include_vars: >
        chrony_vars.yml

    - name: Install chrony
      package:
        name: "{{ chrony_package[ansible_os_family] }}"
        state: present

    - name: Start and enabled service chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: started
        enabled: true

    - name: Deploy chrony conf template
      template:
        src: templates/chrony.conf.j2
        dest: "{{ chrony_conf_path[ansible_os_family] }}"
      notify: Restart chrony

  handlers:

    - name: Restart chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: restarted
```

Et mon fichier de variables chrony_vars.yml :
```yaml
---
chrony_package:
  Debian: chrony
  RedHat: chrony
  Suse: chrony

chrony_service:
  Debian: chrony
  RedHat: chronyd
  Suse: chronyd

chrony_conf_path:
  Debian: /etc/chrony/chrony.conf
  RedHat: /etc/chrony.conf
  Suse: /etc/chrony.conf
```