# Exercice

Écrivez trois playbooks pour afficher des informations sur chacun des Target Hosts :

    pkg-info.yml pour afficher le gestionnaire de paquets utilisé
```yaml
---
- name: Display package manager information
  hosts: all
  tasks:
    - name: Display package manager
      debug:
        msg: "The package manager on {{ inventory_hostname }} is {{ ansible_pkg_mgr }}"
```
    python-info.yml pour afficher la version de Python installée
```yaml
---
- name: Display Python version information
  hosts: all
  tasks:
    - name: Get Python version
      command: python3 --version
      register: python_version
      changed_when: false

    - name: Display Python version
      debug:
        msg: "Python version on {{ inventory_hostname }} is {{ python_version.stdout }}"
```
    dns-info.yml pour afficher le(s) serveur(s) DNS utilisé(s)
```yaml
---
- name: Display DNS server information
  hosts: all
  tasks:
    - name: Display DNS servers
      debug:
        msg: "DNS servers on {{ inventory_hostname }} are {{ ansible_dns.nameservers }}"
```
Quittez le Control Host :

$ exit

Supprimez toutes les VM :

$ vagrant destroy -f