---
title: Gérer une machine virtuelle Linux dans Azure à l’aide d’Ansible
description: Découvrez comment utiliser Ansible pour gérer une machine virtuelle Linux dans Azure.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: ef19043465cb67401786cddec4972f67231995bb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077808"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Gérer une machine virtuelle Linux dans Azure à l’aide d’Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer vos machines virtuelles Azure comme vous le feriez pour toute autre ressource. Cet article vous explique comment utiliser un playbook Ansible pour démarrer et arrêter une machine virtuelle Linux. 

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Utiliser Ansible pour libérer (arrêter) une machine virtuelle Azure
Cette section vous explique comment utiliser Ansible pour libérer (arrêter) une machine virtuelle Azure.

1.  Connectez-vous au [Portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1.  Créez un fichier (pour héberger votre playbook) nommé `azure-vm-stop.yml`, puis ouvrez-le dans l’éditeur VI, comme suit :

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  Passez en mode insertion en sélectionnant la touche **I**.

1.  Collez l’exemple de code ci-après dans l’éditeur :

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

1.  Quittez le mode insertion en sélectionnant la touche **Échap**.

1.  Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1.  Exécutez l’exemple de playbook Ansible.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  Vous devriez obtenir une sortie semblable à celle qui suit, indiquant que la machine virtuelle a été libérée (arrêtée) :

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

1.  Connectez-vous au [Portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Ouvrez [Cloud Shell](/azure/cloud-shell/overview).

1.  Créez un fichier (pour héberger votre playbook) nommé `azure-vm-start.yml`, puis ouvrez-le dans l’éditeur VI, comme suit :

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  Passez en mode insertion en sélectionnant la touche **I**.

1.  Collez l’exemple de code ci-après dans l’éditeur :

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

1.  Quittez le mode insertion en sélectionnant la touche **Échap**.

1.  Enregistrez le fichier et quittez l’éditeur vi en entrant la commande suivante :

    ```bash
    :wq
    ```

1.  Exécutez l’exemple de playbook Ansible.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  Vous devriez obtenir une sortie semblable à celle qui suit, indiquant que la machine virtuelle a été démarrée :

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
> [Utiliser Ansible pour gérer vos inventaires dynamiques Azure](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)