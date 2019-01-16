---
title: Déployer des applications sur des groupes de machines virtuelles identiques dans Azure avec Ansible
description: Découvrez comment utiliser Ansible pour configurer un groupe de machines virtuelles identiques et déployer des applications sur ce groupe dans Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, machine virtuelle, groupe de machines virtuelles identiques, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: 4f3712a45fdb2474eedeb8d4eac034060723010d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156542"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Déployer des applications sur des groupes de machines virtuelles identiques dans Azure avec Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour déployer vos applications sur Azure. Cet article vous montre comment déployer une application Java sur un groupe de machines virtuelles identiques Azure.  

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Groupe de machines virtuelles identiques** : si vous ne disposez pas déjà d’un groupe de machines virtuelles identiques, vous pouvez [créer un groupe de machines virtuelles identiques avec Ansible](ansible-create-configure-vmss.md). 
- **git** - [git](https://git-scm.com) est utilisé pour télécharger un exemple Java utilisé dans ce tutoriel.
- **Java SE Development Kit (JDK)** : le [JDK](https://aka.ms/azure-jdks) est utilisé pour générer l’exemple de projet Java.
- **Outils de génération Apache Maven** : les [outils de génération Apache Maven](https://maven.apache.org/download.cgi) sont utilisés pour générer l’exemple de projet Java.

> [!Note]
> Ansible 2.6 est nécessaire pour exécuter les exemples de playbooks suivants dans ce tutoriel. 

## <a name="get-host-information"></a>Obtenir des informations sur l’hôte

Cette section montre comment utiliser Ansible pour récupérer des informations sur l’hôte pour un groupe de machines virtuelles Azure. Voici un exemple de playbook Ansible. Le code récupère les adresses IP publiques et l’équilibreur de charge dans le groupe de ressources spécifié, et crée un groupe hôte nommé **saclesethosts** dans l’inventaire. 

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
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Préparer une application pour le déploiement  

Dans cette section, vous utilisez git pour cloner un exemple de projet Java à partir de GitHub et vous générez le projet. Enregistrez le playbook suivant en tant que `app.yml` :

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

La sortie de la commande ansible-playbook est similaire à celle-ci, où vous voyez qu’elle a généré l’exemple d’application cloné à partir de GitHub :

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Déployer l’application sur un groupe de machines virtuelles identiques

La section suivante d’un playbook Ansible installe l’environnement JRE (Java Runtime Environment) sur un groupe hôte nommé **saclesethosts** et déploie l’application Java sur un groupe hôte nommé **saclesethosts** : 

(Changez le `admin_password` et remplacez-le par votre propre mot de passe.)

  ```yml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
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

Vous pouvez enregistrer l’exemple de playbook Ansible précédent en tant que `vmss-setup-deploy.yml` ou [télécharger la totalité de l’exemple de playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Pour utiliser le type de connexion SSH avec des mots de passe, vous devez installer le programme sshpass. 
  - Pour Ubuntu 16.04, exécutez la commande `apt-get install sshpass`.
  - Pour CentOS 7.4, exécutez la commande `yum install sshpass`.

Vous pouvez voir une erreur comme **Using an SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this. (L’utilisation d’un mot de passe SSH au lieu d’une clé n’est pas possible, car la vérification de la clé d’hôte est activée et sshpass ne la prend pas en charge.) Add this host’s fingerprint to your known_hosts file to manage this host.** (Ajoutez l’empreinte numérique de cet hôte à votre fichier known_hosts pour gérer cet hôte.) Si vous voyez cette erreur, vous pouvez désactiver la vérification de la clé d’hôte en ajoutant la ligne suivante au fichier `/etc/ansible/ansible.cfg` ou au fichier `~/.ansible.cfg` :
  ```bash
  [defaults]
  host_key_checking = False
  ```

Exécutez le playbook avec la commande suivante :

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

La sortie de l’exécution de la commande ansible-playbook indique que l’exemple d’application Java a été installé sur le groupe hôte du groupe de machines virtuelles identiques :

  ```Output
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Félicitations ! Votre application s’exécute maintenant dans Azure. Vous pouvez maintenant accéder à l’URL de l’équilibreur de charge pour votre groupe de machines virtuelles identiques :

![Application Java s’exécutant dans un groupe de machines virtuelles identiques dans Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Automatically scale a virtual machine scale set in Azure using Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss) (Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques avec Ansible)