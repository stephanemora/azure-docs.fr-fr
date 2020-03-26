---
title: 'Tutoriel : Configurer le peering de réseaux virtuels Azure avec Ansible'
description: Découvrez comment utiliser Ansible pour connecter des réseaux virtuels par peering.
keywords: Ansible, Azure, DevOps, Bash, playbook, réseaux, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155731"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Tutoriel : Configurer le peering de réseaux virtuels Azure avec Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

Le [peering de réseaux virtuels](/azure/virtual-network/virtual-network-peering-overview) permet de connecter deux réseaux virtuels Azure de sorte qu’ils apparaissent comme un seul réseau du point de vue de la connectivité. 

Le trafic est acheminé entre les machines virtuelles d’un même réseau virtuel au moyen d’adresses IP privées. De même, le trafic entre les machines virtuelles d’un réseau virtuel avec peering est acheminé par le biais de l’infrastructure principale de Microsoft. Les machines virtuelles de différents réseaux virtuels peuvent ainsi communiquer les unes avec les autres.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer deux réseaux virtuels
> * Interconnecter les deux réseaux virtuels
> * Supprimer le peering entre les deux réseaux

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Créer deux groupes de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple de code de playbook dans cette section est utilisé pour :

- Créer deux groupes de ressources 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Créer le premier réseau virtuel

L’exemple de code de playbook dans cette section est utilisé pour :

- Créez un réseau virtuel
- créer un sous-réseau au sein du réseau virtuel

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Créer le deuxième réseau virtuel

L’exemple de code de playbook dans cette section est utilisé pour :

- Créez un réseau virtuel
- créer un sous-réseau au sein du réseau virtuel

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Interconnecter les deux réseaux virtuels

L’exemple de code de playbook dans cette section est utilisé pour :

- Initialiser le peering de réseaux virtuels
- Interconnecter les deux réseaux virtuels créés

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Supprimer le peering de réseaux virtuels

L’exemple de code de playbook dans cette section est utilisé pour :

- Supprimer le peering entre les deux réseaux virtuels créés

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Obtenir l’exemple de playbook

Il existe deux façons d’obtenir l’exemple de playbook complet :

- [Téléchargez le manifeste](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) et enregistrez-le sous `vnet_peering.yml`.
- Créez un nouveau fichier nommé `vnet_peering.yml` et copiez-y le contenu suivant :

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Exécutez l’exemple de playbook

L’exemple de code de playbook dans cette section est utilisé pour tester les différentes fonctionnalités présentées dans ce tutoriel.

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Dans la section `vars`, remplacez l’espace réservé `{{ resource_group_name }}` par le nom de votre groupe de ressources.

Exécutez le playbook à l’aide de la commande ansible-playbook :

```bash
ansible-playbook vnet_peering.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

L’exemple de code de playbook dans cette section est utilisé pour :

- Supprimer les deux groupes de ressources créés

Enregistrez le playbook suivant en tant que `cleanup.yml` :

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Voici quelques remarques importantes à prendre en compte lorsque vous travaillez avec l’exemple de playbook :

- Remplacez l’espace réservé `{{ resource_group_name-1 }}` par le nom du premier groupe de ressources créé.
- Remplacez l’espace réservé `{{ resource_group_name-2 }}` par le nom du deuxième groupe de ressources créé.
- Toutes les ressources des deux groupes de ressources spécifiés sont supprimées.

Exécutez le playbook à l’aide de la commande ansible-playbook :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Ansible sur Azure](/azure/ansible/)