---
title: 'Tutoriel : Configurer des clusters Azure Kubernetes Service (AKS) dans Azure à l’aide d’Ansible'
description: Découvrez comment utiliser Ansible pour créer et gérer un cluster Azure Kubernetes Service dans Azure
keywords: ansible, azure, devops, bash, cloudshell, manuel, aks, conteneur, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156680"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Tutoriel : Configurer des clusters Azure Kubernetes Service (AKS) dans Azure à l’aide d’Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS peut être configuré pour utiliser [Azure Active Directory (AD)](/azure/active-directory/) pour l’authentification utilisateur. Une fois configuré, votre jeton d’authentification Azure AD vous permet de vous connecter au cluster AKS. Le RBAC peut être basé sur l’identité d’un utilisateur ou l’appartenance à un groupe de répertoire.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer un cluster AKS
> * Configurer un cluster AKS

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Créer un cluster AKS managé

L’exemple de manuel crée un groupe de ressources et un cluster AKS au sein du groupe de ressources.

Enregistrez le playbook suivant en tant que `azure_create_aks.yml` :

```yml
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
    aks_version: aks_version
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
      kubernetes_version: "{{aks_version}}"
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

Avant d’exécuter le playbook, consultez les notes suivantes :

- La première section au sein de `tasks` définit un groupe de ressources nommé `myResourceGroup` dans l’emplacement `eastus`.
- La deuxième section au sein de `tasks` définit un cluster AKS nommé `myAKSCluster` dans le groupe de ressources `myResourceGroup`.
- Pour l’espace réservé `your_ssh_key`, entrez votre clé publique RSA dans un format de ligne unique, commençant par « ssh-rsa » (sans les guillemets).
- Pour l'espace réservé `aks_version`, utilisez la commande [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions).

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook azure_create_aks.yml
```

L’exécution du playbook donne des résultats similaires à la sortie suivante :

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Mettre à l’échelle nœuds ACS

L’exemple de playbook de la section précédente définit deux nœuds. Vous ajustez le nombre de nœuds en modifiant la valeur `count` dans le bloc `agent_pool_profiles`.

Enregistrez le playbook suivant en tant que `azure_configure_aks.yml` :

```yml
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

Avant d’exécuter le playbook, consultez les notes suivantes :

- Pour l’espace réservé `your_ssh_key`, entrez votre clé publique RSA dans un format de ligne unique, commençant par « ssh-rsa » (sans les guillemets).

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook azure_configure_aks.yml
```

L’exécution du playbook donne des résultats similaires à la sortie suivante :

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Supprimer un cluster AKS managé

L’exemple de manuel supprime un cluster AKS.

Enregistrez le playbook suivant en tant que `azure_delete_aks.yml` :


```yml
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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook azure_delete_aks.yml
```

L’exécution du playbook donne des résultats similaires à la sortie suivante :

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Mettre à l’échelle une application dans Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)