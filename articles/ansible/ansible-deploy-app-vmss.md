---
title: 'Tutoriel : Déployer des applications sur des groupes identiques de machines virtuelles dans Azure à l’aide d’Ansible'
description: Découvrez comment utiliser Ansible pour configurer groupes de machines virtuelles identiques Azure et déployer l’application sur le groupe identique
keywords: ansible, azure, devops, bash, playbook, machine virtuelle, groupe de machines virtuelles identiques, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.openlocfilehash: d638ae3f0c33734b42ef5456772fcd2bc62e35a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75940852"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutoriel : Déployer des applications sur des groupes de machines virtuelles identiques dans Azure à l’aide d’Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Récupérer des informations sur l’hôte pour une groupe de machines virtuelles Azure
> * Cloner et générer l’exemple d’application
> * Installer l’environnement Java Runtime (JRE) sur un groupe identique
> * Déployer l’application Java sur un groupe identique

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com) est utilisé pour télécharger un exemple Java utilisé dans ce tutoriel.
- **Java SE Development Kit (JDK)** : le [JDK](https://aka.ms/azure-jdks) est utilisé pour générer l’exemple de projet Java.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) est utilisé pour générer l’exemple de projet Java.

## <a name="get-host-information"></a>Obtenir des informations sur l’hôte

Le code de playbook dans cette section récupère des informations sur l’hôte pour un groupe de machines virtuelles. Le code obtient les adresses IP publiques et l’équilibreur de charge au sein d’un groupe de ressources spécifié et crée un groupe hôte nommé `scalesethosts` dans l’inventaire.

Enregistrez l’exemple de playbook suivant en tant que `get-hosts-tasks.yml` :

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Préparer une application pour le déploiement

Le code de playbook dans cette section utilise `git` pour cloner un exemple de projet Java à partir de GitHub et génère le projet. 

Enregistrez le playbook suivant en tant que `app.yml` :

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Exécutez l’exemple de playbook Ansible avec la commande suivante :

  ```bash
  ansible-playbook app.yml
  ```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Déployez l’application sur un groupe identique

Le code de playbook dans cette section est utilisé dans les objectifs suivants :

* Installer l’environnement JRE sur un groupe hôte nommé `saclesethosts`
* Déployer l’application Java sur un groupe hôte nommé `saclesethosts`

Il existe deux façons d’obtenir le playbook de l’exemple :

* [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) et enregistrez-le sous `vmss-setup-deploy.yml`.
* Créez un nouveau fichier nommé `vmss-setup-deploy.yml` et copiez-y le contenu suivant :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Avant d’exécuter le playbook, consultez les notes suivantes :

* Dans la section `vars`, remplacez l’espace réservé `{{ admin_password }}` par votre propre mot de passe.
* Pour utiliser le type de connexion ssh avec des mots de passe, installez le programme sshpass :

    Ubuntu :

    ```bash
    apt-get install sshpass
    ```

    CentOS :

    ```bash
    yum install sshpass
    ```

* Dans certains environnements, vous pouvez voir une erreur relative à l’utilisation d’un mot de passe SSH au lieu d’une clé. Si vous recevez cette erreur, vous pouvez désactiver la vérification de clé d’hôte en ajoutant la ligne suivante à `/etc/ansible/ansible.cfg` ou à `~/.ansible.cfg` :

    ```bash
    [defaults]
    host_key_checking = False
    ```

Exécutez le playbook avec la commande suivante :

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

La sortie de l’exécution de la commande ansible-playbook indique que l’exemple d’application Java a été installé pour le groupe hôte du groupe identique :

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Vérifier les résultats

Vérifiez les résultats de votre travail en accédant à l’URL de l’équilibreur de charge pour votre groupe identique :

![Application Java s’exécutant dans un groupe identique dans Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Mise à l’échelle automatique de groupes de machines virtuelles identiques dans Azure à l’aide d’Ansible](./ansible-auto-scale-vmss.md)