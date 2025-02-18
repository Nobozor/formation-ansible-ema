# Exercice

    Écrivez un playbook kernel.yml qui affiche les infos détaillées du noyau sur tous vos Target Hosts. Utilisez la commande uname -a et le module debug avec le paramètre msg.  
    Playbook kernel.yml :
```yaml
--- 

- hosts: all
  gather_facts: false
  
  tasks:

    - name: Report kernel information
      command: uname -a
      changed_when: false
      register: kernel_info

    - debug:
        msg: "{{ kernel_info.stdout_lines }}"
```
    

    Ouput lors du lancement du playbook :  
```bash
[vagrant@ansible playbooks]$ ansible-playbook kernel.yml 

PLAY [all] ***********************************************************************************************************************************************************************************************************

TASK [Report kernel information] *************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [debug] *********************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": [
        "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
    ]
}
ok: [debian] => {
    "msg": [
        "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
    ]
}
ok: [suse] => {
    "msg": [
        "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
    ]
}
```
    Essayez d’obtenir le même résultat en utilisant le paramètre var du module debug.
    kernel_var.yml :
```yaml
--- 

- hosts: all
  gather_facts: false
  
  tasks:

    - name: Report kernel information
      command: uname -a
      changed_when: false
      register: kernel_info

    - debug:
        var: kernel_info.stdout_lines
```

    Écrivez un playbook packages.yml qui affiche le nombre total de paquets RPM installés sur les hôtes rocky et suse (rpm -qa | wc -l).  
    packages.yml :
```yaml
---

- hosts: suse, rocky

  tasks:

    - name: Report number of packet installed on rocky|suse distribution
      shell: "rpm -qa | wc -l"
      changed_when: false
      register: packet_nbr

    - debug:
        var: packet_nbr.stdout_lines
```

    exemple de résultat :
```bash
[vagrant@ansible playbooks]$ ansible-playbook packages.yml 

PLAY [suse, rocky] ***************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************************
ok: [rocky]
ok: [suse]

TASK [Report number of packet installed on rocky|suse distribution] **************************************************************************************************************************************************
ok: [rocky]
ok: [suse]

TASK [debug] *********************************************************************************************************************************************************************************************************
ok: [suse] => {
    "packet_nbr.stdout_lines": [
        "917"
    ]
}
ok: [rocky] => {
    "packet_nbr.stdout_lines": [
        "671"
    ]
}

PLAY RECAP ***********************************************************************************************************************************************************************************************************
rocky                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
Quittez le Control Host :

$ exit

Supprimez toutes les VM :

$ vagrant destroy -f