---
title: Créer, changer ou supprimer une table de routage Azure à l’aide d’Ansible
description: Découvrir comment utiliser Ansible pour créer, modifier ou supprimer une table de routage
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, réseaux, routes, table de routage
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 025a8182d32a7d0d00a48795c848d356eb1c3d4e
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792444"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Créer, changer ou supprimer une table de routage Azure à l’aide d’Ansible
Azure achemine automatiquement le trafic entre les sous-réseaux, les réseaux virtuels et les réseaux locaux Azure. Si vous voulez changer un routage par défaut sur Azure, vous devez créer une [table de routage](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Cet article vous montre comment créer, changer ou supprimer une table de routage Azure, ainsi qu’attacher la table de routage à un sous-réseau. 

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 est nécessaire pour exécuter les exemples de playbooks suivants dans ce tutoriel.

## <a name="create-a-route-table"></a>Créer une table de routage
Cette section présente un exemple de playbook Ansible qui crée une table de routage. Le nombre de tables de routage que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Enregistrez ce playbook en tant que `route_table_create.yml`. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau
Un sous-réseau peut avoir zéro ou une table de routage associée. Une table de routage peut être associée à plusieurs ou aucun sous-réseau. Étant donné que les tables de routage ne sont pas associées à des réseaux virtuels, vous devez associer une table de routage à chaque sous-réseau auquel vous souhaitez associer la table. Tout le trafic sortant du sous-réseau est basé sur les itinéraires que vous avez créés dans les tables de routage, les [itinéraires par défaut](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default) et les itinéraires propagés à partir d’un réseau local, si le réseau virtuel est connecté à une passerelle de réseau virtuel Azure (ExpressRoute, ou VPN si vous utilisez le protocole BGP avec une passerelle VPN). Vous pouvez uniquement associer une table de routage à des sous-réseaux de réseaux virtuels qui se trouvent au même emplacement et dans le même abonnement Azure que la table de routage.

Cette section présente un exemple de playbook Ansible qui crée un réseau virtuel et un sous-réseau, puis associe une table de routage au sous-réseau.

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Enregistrez ce playbook en tant que `route_table_associate.yml`. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissocier une table de routage d’un sous-réseau
Quand vous dissociez une table de routage d’un sous-réseau, il vous suffit de définir `route_table` sur `None` dans un sous-réseau. Voici un exemple de playbook Ansible. 

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Enregistrez ce playbook en tant que `route_table_dissociate.yml`. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Créer un itinéraire
Cette section présente un exemple de playbook Ansible qui crée une route sous la table de routage. Elle définit `virtual_network_gateway` comme `next_hop_type` et `10.1.0.0/16` comme `address_prefix`. Le préfixe ne peut pas être dupliqué dans plusieurs itinéraires de la table de routage, bien qu’il puisse se trouver dans un autre préfixe. Pour en savoir plus sur la façon dont Azure sélectionne les routes et obtenir une description détaillée de tous les types de tronçons suivants, consultez [Vue d’ensemble du routage](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```
Enregistrez ce playbook en tant que `route_create.yml`. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Supprimer un itinéraire
Cette section présente un exemple de playbook Ansible qui supprime une route d’une table de routage.

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Enregistrez ce playbook en tant que `route_delete.yml`. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Obtenir des informations sur une table de routage
Vous pouvez afficher les détails d’une table de routage (route_table) par le biais du module Ansible nommé `azure_rm_routetable_facts`. Le module de faits retourne les informations de la table de routage avec toutes les routes qui lui sont attachées.
Voici un exemple de playbook Ansible. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Enregistrez ce playbook en tant que `route_table_facts.yml`. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Supprimer une table de routage
Une table de routage associée à un sous-réseau ne peut pas être supprimée. [Dissociez](#dissociate-a-route-table-from-a-subnet) la table de routage de tous les sous-réseaux avant de tenter de la supprimer.

Vous pouvez supprimer la table de routage ainsi que toutes les routes. Voici un exemple de playbook Ansible. 

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Enregistrez ce playbook en tant que `route_table_delete.yml`. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Ansible sur Azure](https://docs.microsoft.com/azure/ansible/)
