---
title: 'Tutoriel : Configurer la mise en réseau Azure CNI dans Azure Kubernetes Service (AKS) à l’aide d’Ansible'
description: Découvrez comment utiliser Ansible pour configurer la mise en réseau kubenet dans un cluster Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, manuel, aks, conteneur, aks, kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156900"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutoriel : Configurer la mise en réseau Azure CNI dans Azure Kubernetes Service (AKS) à l’aide d’Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

À l’aide d’AKS, vous pouvez déployer un cluster à l’aide des modèles de réseau suivants :

- [Mise en réseau Kubenet](/azure/aks/configure-kubenet) : les ressources réseau sont généralement créées et configurées lors du déploiement du cluster AKS.
- [Mise en réseau de Azure CNI](/azure/aks/configure-azure-cni): le cluster AKS est connecté à des ressources de réseau virtuel (VNET) et à des configurations existantes.

Pour plus d’informations sur la mise en réseau avec vos applications dans AKS, consultez [Concepts réseau pour les applications dans AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer un cluster AKS
> * Configurer le réseau Azure CNI

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau

L’exemple de code de playbook dans cette section est utilisé pour :

- Créez un réseau virtuel
- créer un sous-réseau au sein du réseau virtuel

Enregistrez le playbook suivant en tant que `vnet.yml` :

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Créer un cluster AKS dans le réseau virtuel

L’exemple de code de playbook dans cette section est utilisé pour :

- créer un cluster AKS au sein d’un réseau virtuel.

Enregistrez le playbook suivant en tant que `aks.yml` :

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Utilisez le module `azure_rm_aks_version` pour rechercher la version prise en charge.
- `vnet_subnet_id` est le sous-réseau créé dans la section précédente.
- Le playbook charge `ssh_key` à partir de `~/.ssh/id_rsa.pub`. Si vous le modifiez, utilisez le format single-line en commençant par « ssh-rsa » (sans les guillemets).
- Les valeurs `client_id` et `client_secret` sont chargées à partir de `~/.azure/credentials`, qui est le fichier d’informations d’identification par défaut. Vous pouvez définir ces valeurs pour votre service principal ou les charger à partir de variables d’environnement :

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Exécutez l’exemple de playbook

L’exemple de code de playbook dans cette section est utilisé pour tester les différentes fonctionnalités présentées dans ce tutoriel.

Enregistrez le playbook suivant en tant que `aks-azure-cni.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Modifiez la valeur `aksansibletest` et donnez-lui le nom de votre groupe de ressources.
- Modifiez la valeur `aksansibletest` et donnez-lui le nom de votre AKS.
- Modifiez la valeur `eastus` et donnez-lui le nom de l’emplacement de votre groupe de ressources.

Exécutez le playbook à l’aide de la commande ansible-playbook :

```bash
ansible-playbook aks-azure-cni.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

L’exemple de code de playbook dans cette section est utilisé pour :

- supprimer un groupe de ressources mentionné dans la section `vars`.

Enregistrez le playbook suivant en tant que `cleanup.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Remplacez l’espace réservé `{{ resource_group_name }}` par le nom de votre groupe de ressources.
- Toutes les ressources au sein du groupe de ressources spécifié sont supprimées.

Exécutez le playbook à l’aide de la commande ansible-playbook :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Configurer Azure Active Directory dans AKS à l’aide d’Ansible](./ansible-aks-configure-rbac.md)