---
title: Gérer une machine virtuelle Linux dans Azure à l’aide d’Ansible
description: Découvrez comment utiliser Ansible pour gérer une machine virtuelle Linux dans Azure.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250647"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Gérer une machine virtuelle Linux dans Azure à l’aide d’Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer vos machines virtuelles Azure comme vous le feriez pour toute autre ressource. Cet article vous explique comment utiliser un playbook Ansible pour démarrer et arrêter une machine virtuelle Linux. 

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Configurez Azure Cloud Shell** ou **installez et configurez Ansible sur une machine virtuelle Linux**.

  **Configurez Azure Cloud Shell**.

  1. **Configurez Azure Cloud Shell** : si vous ne connaissez pas encore Azure Cloud Shell, l’article [Démarrage rapide de Bash dans Azure Cloud Shell](/azure/cloud-shell/quickstart) vous indique comment démarrer et configurer Cloud Shell. 

  1. **Machine virtuelle Linux** : si vous n’avez pas accès à une machine virtuelle Linux, vous pouvez créer une [machine virtuelle avec Ansible](ansible-create-vm.md).

  **--OU--**

  **Installez et configurez Ansible sur une machine virtuelle Linux**.

  1. **Installez Ansible** : installez Ansible sur une [plateforme Linux prise en charge](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Configurer Ansible** - [Créer des informations d’identification Azure et configurer Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Utiliser Ansible pour libérer (arrêter) une machine virtuelle Azure
Cette section vous explique comment utiliser Ansible pour libérer (arrêter) une machine virtuelle Azure.

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1. Créez un fichier (pour héberger votre playbook) nommé `azure_vm_stop.yml`, puis ouvrez-le dans l’éditeur VI, comme suit :

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Passez en mode insertion en sélectionnant la touche **I**.

1. Collez l’exemple de code ci-après dans l’éditeur :

    ```yaml
    - name: Stop Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
            allocated: no 
    ```

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Exécutez l’exemple de playbook Ansible.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. Vous devriez obtenir une sortie semblable à celle qui suit, indiquant que la machine virtuelle a été libérée (arrêtée) :

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Utiliser Ansible pour démarrer une machine virtuelle Azure libérée (arrêtée)
Cette section vous explique comment utiliser Ansible pour démarrer une machine virtuelle Azure libérée (arrêtée).

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1. Créez un fichier (pour héberger votre playbook) nommé `azure_vm_start.yml`, puis ouvrez-le dans l’éditeur VI, comme suit :

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Passez en mode insertion en sélectionnant la touche **I**.

1. Collez l’exemple de code ci-après dans l’éditeur :

    ```yaml
    - name: Start Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Start the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
    ```

1. Quittez le mode insertion en sélectionnant la touche **Échap**.

1. Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1. Exécutez l’exemple de playbook Ansible.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. Vous devriez obtenir une sortie semblable à celle qui suit, indiquant que la machine virtuelle a été démarrée :

    Vous devriez obtenir une sortie semblable à celle qui suit, indiquant que la machine virtuelle a été démarrée :

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Utiliser Ansible pour gérer vos inventaires dynamiques Azure](../../ansible/ansible-manage-azure-dynamic-inventories.md)