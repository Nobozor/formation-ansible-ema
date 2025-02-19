# Exercice

Écrivez successivement deux playbooks pour mettre en place la synchronisation NTP avec Chrony sur vos quatre Target Hosts sous Debian, Rocky Linux, SUSE Linux et Ubuntu. Il vous faudra identifier le nom du paquet, le service correspondant et le chemin vers le fichier de configuration par défaut pour chacune des distributions.

Voici la configuration qu’il faudra installer sur chacune des quatre cibles :
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
    Le premier playbook chrony-01.yml utilisera les modules de gestion de paquets natifs apt, dnf et zypper et s’inspirera de la méthode « gros sabots » utilisée plus haut dans cet article.
```yaml
---
- hosts: all

  tasks:

    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install chrony on Debian/Ubuntu
      apt:
        name: chrony
      when: ansible_os_family == "Debian"

    - name: Install chrony  on Rocky Linux
      dnf:
        name: chrony
      when: ansible_distribution == "Rocky"

    - name: Install chrony  on SUSE Linux
      zypper:
        name: chrony
      when: ansible_distribution == "openSUSE Leap"

    - name: Start and enabled service chrony
      service:
        name: chronyd
        state: started
        enabled: true

    - name: copy conf file on debian / ubuntu
      copy:
        dest: /etc/chrony/chrony.conf
        content: |
          # chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
          # toto
      when: ansible_distribution in ["Debian", "Ubuntu"]
      notify: Reload chrony

    - name: copy conf file on rocky / suse
      copy:
        dest: /etc/chrony.conf
        content: |
          # chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      when: ansible_distribution in ["openSUSE Leap", "Rocky"]
      notify: Reload chrony

  handlers:

    - name: Reload chrony
      service:
        name: chronyd
        state: restarted
```
    Le deuxième playbook chrony-02.yml définira trois variables chrony_package, chrony_service et chrony_confdir et utilisera le module de gestion de paquets générique package.
    Fichier de vars:  
```yaml
[vagrant@ansible playbooks]$ cat vars/chrony_vars.yml
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
    Le playbook chrony-02.yml :
```yaml
[vagrant@ansible playbooks]$ cat chrony-02.yml
---
- hosts: all

  tasks:

    - name: load vars
      include_vars: >
        chrony_vars.yml

    - name: Install chrony on Debian/Ubuntu
      package:
        name: "{{ chrony_package[ansible_os_family] }}"
        state: present

    - name: Start and enabled service chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: started
        enabled: true

    - name: copy conf file on debian / ubuntu
      copy:
        dest: "{{ chrony_conf_path[ansible_os_family] }}"
        content: |
          # chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Reload chrony

  handlers:

    - name: Reload chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: restarted
```
