---
title: 'Tutoriel : Configurer les réseaux Kubenet dans Azure Kubernetes Service (AKS) avec Ansible'
description: Découvrez comment utiliser Ansible pour configurer la mise en réseau kubenet dans un cluster Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, manuel, aks, conteneur, aks, kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156882"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutoriel : Configurer la mise en réseau kubenet dans Azure Kubernetes Service (AKS) à l’aide d’Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

À l’aide d’AKS, vous pouvez déployer un cluster à l’aide des modèles de réseau suivants :

- [Mise en réseau Kubenet](/azure/aks/configure-kubenet) : les ressources réseau sont généralement créées et configurées lors du déploiement du cluster AKS.
- [Interface de mise en réseau de conteneur (CNI) Azure](/azure/aks/configure-azure-cni) : le cluster AKS est connecté à des ressources de réseau virtuel et à des configurations existantes.

Pour plus d’informations sur la mise en réseau avec vos applications dans AKS, consultez [Concepts réseau pour les applications dans AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer un cluster AKS
> * Configurer la mise en réseau Azure kubenet

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau

Le code de playbook dans cette section crée les ressources Azure suivantes :

- Réseau virtuel
- Sous-réseau au sein du réseau virtuel

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

Le code de playbook dans cette section crée un cluster AKS au sein d’un réseau virtuel. 

Enregistrez le playbook suivant en tant que `aks.yml` :

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Utilisez le module `azure_rm_aks_version` pour rechercher la version prise en charge.
- `vnet_subnet_id` est le sous-réseau créé dans la section précédente.
- Le `network_profile` définit les propriétés du plug-in de réseau kubenet.
- Le `service_cidr` est utilisé pour affecter des services internes dans le cluster AKS à une adresse IP. Cette plage d’adresses IP doit être un espace d’adresses qui n’est pas utilisé ailleurs dans votre réseau. 
- L’adresse `dns_service_ip` doit être l’adresse « . 10 » de la plage d’adresses IP de votre service.
- Le `pod_cidr` doit être un vaste espace d’adresses qui n’est pas utilisé ailleurs dans votre environnement réseau. La plage d’adresses doit être suffisamment grande pour contenir le nombre de nœuds que vous prévoyez pour une montée en puissance. Vous ne pouvez pas modifier cette plage d’adresses une fois que le cluster est déployé.
- La plage d’adresses IP pod est utilisée pour affecter un espace d’adresses /24 à chaque nœud du cluster. Dans l’exemple suivant, le `pod_cidr` de 192.168.0.0/16 attribue le premier nœud 192.168.0.0/24, le deuxième nœud 192.168.1.0/24 et le troisième nœud 192.168.2.0/24.
- À mesure que le cluster est mis à l’échelle ou évolue, Azure continue à attribuer une plage d’adresses IP pod à chaque nouveau nœud.
- Le playbook charge `ssh_key` à partir de `~/.ssh/id_rsa.pub`. Si vous le modifiez, utilisez le format single-line en commençant par « ssh-rsa » (sans les guillemets).
- Les valeurs `client_id` et `client_secret` sont chargées à partir de `~/.azure/credentials`, qui est le fichier d’informations d’identification par défaut. Vous pouvez définir ces valeurs pour votre service principal ou les charger à partir de variables d’environnement :

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associer les ressources réseau

Quand vous créez un cluster AKS, un groupe de sécurité réseau et une table de routage sont créés. Ces ressources sont gérées par AKS et mises à jour lorsque vous créez et exposez des services. Associez le groupe de sécurité réseau et la table d’itinéraire à votre sous-réseau virtuel comme suit. 

Enregistrez le playbook suivant en tant que `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Le `node_resource_group` est le nom du groupe de ressources dans lequel les nœuds AKS sont créés.
- Le `vnet_subnet_id` est le sous-réseau créé dans la section précédente.


## <a name="run-the-sample-playbook"></a>Exécutez l’exemple de playbook

Cette section répertorie l’exemple de playbook complet qui appelle les tâches de création dans cet article. 

Enregistrez le playbook suivant en tant que `aks-kubenet.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

Dans la section `vars`, apportez les modifications suivantes :

- Pour la clé `resource_group`, modifiez la valeur `aksansibletest` et donnez-lui le nom de votre groupe de ressources.
- Pour la clé `name`, modifiez la valeur `aksansibletest` et donnez-lui le nom de votre AKS.
- Pour la clé `Location`, modifiez la valeur `eastus` et donnez-lui le nom de l’emplacement de votre groupe de ressources.

Exécutez le playbook complet en utilisant la commande `ansible-playbook` :

```bash
ansible-playbook aks-kubenet.yml
```

L’exécution du playbook donne des résultats similaires à la sortie suivante :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le code suivant en tant que `cleanup.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Dans la section `vars`, remplacez l’espace réservé `{{ resource_group_name }}` par le nom de votre groupe de ressources.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Configurer la mise en réseau de l’Interface de mise en réseau des conteneur (CNI) Azure dans AKS à l’aide d’Ansible](./ansible-aks-configure-cni-networking.md)