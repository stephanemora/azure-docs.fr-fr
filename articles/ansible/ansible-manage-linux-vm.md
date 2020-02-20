---
title: 'Démarrage rapide : Gérer des machines virtuelles Linux dans Azure à l’aide d’Ansible'
description: Guide de démarrage rapide expliquant comment gérer une machine virtuelle Linux dans Azure à l’aide d’Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 90113373535c835e5cddf707bcb520789e596aec
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202431"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Démarrage rapide : Gérer des machines virtuelles Linux dans Azure à l’aide d’Ansible

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Dans cet article, vous utilisez un playbook Ansible pour démarrer et arrêter une machine virtuelle Linux. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Arrêt d’une machine virtuelle

Dans cette section, vous utilisez Ansible pour libérer (arrêter) une machine virtuelle Azure.

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1. Créez un fichier nommé `azure-vm-stop.yml` et ouvrez-le dans l’éditeur :

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. Collez l’exemple de code ci-après dans l’éditeur :

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Remplacez les espaces réservés `{{ resource_group_name }}` et `{{ vm_name }}` par vos valeurs.

1. Enregistrez le fichier et quittez l’éditeur.

1. Exécutez le playbook à l’aide de la commande `ansible-playbook` :

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Démarrage d'une machine virtuelle

Dans cette section, vous utilisez Ansible pour démarrer une machine virtuelle Azure libérée (arrêtée).

1. Connectez-vous au [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1. Créez un fichier nommé `azure-vm-start.yml` et ouvrez-le dans l’éditeur :

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. Collez l’exemple de code ci-après dans l’éditeur :

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Remplacez les espaces réservés `{{ resource_group_name }}` et `{{ vm_name }}` par vos valeurs.

1. Enregistrez le fichier et quittez l’éditeur.

1. Exécutez le playbook à l’aide de la commande `ansible-playbook` :

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Tutoriel : Gérer des inventaires dynamiques Azure à l’aide d’Ansible](./ansible-manage-azure-dynamic-inventories.md)