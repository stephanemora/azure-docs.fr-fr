---
title: Créer et configurer des clusters Azure Kubernetes Service dans Azure avec Ansible
description: Découvrez comment utiliser Ansible pour créer et gérer un cluster Azure Kubernetes Service dans Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, conteneur, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: c4f78d8bb43b26814dc3a4b94109dfd8719cb48f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258830"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Créer et configurer des clusters Azure Kubernetes Service dans Azure avec Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer votre service Azure Kubernetes Service (AKS). Cet article vous montre comment utiliser Ansible pour créer et configurer un cluster Azure Kubernetes Service.

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- **Principal du service Azure** : lors de la [création du principal du service](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), notez les valeurs suivantes : **appId**, **displayName**, **mot de passe** et **locataire**.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.6 est nécessaire pour exécuter les exemples de playbooks suivants dans ce tutoriel. 

## <a name="create-a-managed-aks-cluster"></a>Créer un cluster AKS managé
Le code indiqué dans cette section présente un exemple de playbook Ansible pour créer un groupe de ressources, ainsi qu’un cluster AKS qui réside dans ce groupe de ressources.

> [!Tip]
> Pour l’espace réservé `your_ssh_key`, entrez votre clé publique RSA dans un format de ligne unique, commençant par « ssh-rsa » (sans les guillemets). 

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

Les éléments suivant vous aident à comprendre le code du playbook Ansible précédent :
- La première section de **tasks** définit un groupe de ressources nommé **myResourceGroup** à l’emplacement **eastus**. 
- La deuxième section de **tasks** définit un cluster AKS nommé **myAKSCluster** au sein du groupe de ressources **myResourceGroup**. 

Pour créer le cluster AKS avec Ansible, enregistrez l’exemple de playbook précédent en tant que `azure_create_aks.yml`, puis exécutez le playbook avec la commande suivante :

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

La sortie de la commande **ansible-playbook* est similaire à ceci, qui montre que le cluster AKS a été correctement créé :

  ```Output
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Mettre à l’échelle nœuds ACS

L’exemple de playbook de la section précédente définit deux nœuds. Si vous avez besoin de plus ou de moins de charges de travail de conteneur sur votre cluster, vous pouvez facilement ajuster le nombre de nœuds. L’exemple de playbook de cette section fait passer le nombre de nœuds de deux à trois. La modification du nombre de nœuds est effectuée en changeant la valeur de **count** dans le bloc **agent_pool_profiles**. 

> [!Tip]
> Pour l’espace réservé `your_ssh_key`, entrez votre clé publique RSA dans un format de ligne unique, commençant par « ssh-rsa » (sans les guillemets). 

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Pour mettre à l’échelle le cluster Azure Kubernetes Service avec Ansible, enregistrez le playbook précédent en tant que *azure_configure_aks.yml*, puis exécutez le playbook comme suit :

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

La sortie suivante montre que le cluster AKS a été correctement créé :

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Supprimer un cluster AKS managé

La section suivante avec l’exemple de playbook Ansible illustre comment supprimer un cluster AKS :

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name: 
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Pour supprimer le cluster Azure Kubernetes Service avec Ansible, enregistrez le playbook précédent en tant que *azure_delete_aks.yml*, puis exécutez le playbook comme suit :

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

La sortie suivante montre que le cluster AKS a été correctement supprimé :
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Tutoriel : Mettre à l’échelle une application dans Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
