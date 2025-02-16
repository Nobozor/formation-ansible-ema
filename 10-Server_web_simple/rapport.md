# Exercice

Placez-vous dans le répertoire du dixième atelier pratique :
```bash
$ cd ~/formation-ansible/atelier-10
```

Voici les quatre machines virtuelles de cet atelier :
```bash
Machine virtuelle 	Adresse IP
ansible 	192.168.56.10
rocky 	192.168.56.20
debian 	192.168.56.30
suse 	192.168.56.40
```
Démarrez les VM :
```bash
$ vagrant up
```

Connectez-vous au Control Host :

```bash
$ vagrant ssh ansible

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

Écrivez trois playbooks :

    Un premier playbook apache-debian.yml qui installe Apache sur l’hôte debian avec une page personnalisée Apache web server running on Debian Linux.
```yaml
[vagrant@ansible playbooks]$ cat apache-debian.yml 
---
- hosts: debian

  tasks: 

    - name: Update cache information
      apt:
        update_cache: true

    - name: install apache
      apt:
        name: apache2

    - name: start and enable apache2 service
      service:
        name: apache2
        state: started
        enabled: true

    - name: change la page web par defaut
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content : |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>DEBIAN WEB SERVER CONFIGURED BY ANSIBLE</h1>
            </body>
          </html>
```
`Verification : `
```bash
[vagrant@ansible playbooks]$ curl debian
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
      <h1>DEBIAN WEB SERVER CONFIGURED BY ANSIBLE</h1>
  </body>
</html>
```
    Un deuxième playbook apache-rocky.yml qui installe Apache sur l’hôte rocky avec une page personnalisée Apache web server running on Rocky Linux.
```yaml
    [vagrant@ansible playbooks]$ cat apache-rocky.yml 
---
- hosts: rocky
  tasks: 
    - name: install httpd (apache2)
      dnf:
        name: httpd
        state: latest

    - name: start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: true

    - name: change la page web par defaut
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content : |
         <!doctype html>
         <html>
           <head>
             <meta charset="utf-8">
             <title>Test</title>
           </head>
           <body>
               <h1>ROCKY WEB SERVER CONFIGURED BY ANSIBLE</h1>
           </body>
         </html>
``` 
`Verification : `
```bash
[vagrant@ansible playbooks]$ curl rocky
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
      <h1>ROCKY WEB SERVER CONFIGURED BY ANSIBLE</h1>
  </body>
</html>
```
    Un troisième playbook apache-suse.yml qui installe Apache sur l’hôte suse avec une page personnalisée Apache web server running on SUSE Linux.
```yml
[vagrant@ansible playbooks]$ cat apache-suse.yml 
---
- hosts: suse
  tasks:
    - name: Install apache2 with recommended packages
      zypper:
        name: apache2
        state: present

    - name: start and enable httpd service
      service:
        name: apache2
        state: started
        enabled: true

    - name: change la page web par defaut
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content : |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
                <h1>SUSE WEB SERVER CONFIGURED BY ANSIBLE</h1>
            </body>
          </html>
```
`Verification : `
```bash
[vagrant@ansible playbooks]$ curl suse
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
      <h1>SUSE WEB SERVER CONFIGURED BY ANSIBLE</h1>
  </body>
</html>
```
Voici quelques pistes de réflexion :  
>    Pas la peine de rafraîchir le cache de paquets sous Rocky Linux et SUSE.  
    Apache n’a pas forcément le même nom sur ces distributions.  
    La même chose vaut pour le service correspondant.  
    Rocky Linux utilise le gestionnaire de paquets dnf.  
    SUSE Linux utilise le gestionnaire de paquets zypper.  
    Les modules de gestion de paquets correspondants portent le même nom.  
    L’emplacement de la page web par défaut (directive DocumentRoot) peut varier.  
    J’ai supprimé le pare-feu FirewallD installé par défaut sous Rocky Linux pour ne pas trop vous embrouiller.  
