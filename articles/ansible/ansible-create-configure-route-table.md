---
title: 'Tutoriel : Configurer des tables de routage Azure avec Ansible'
description: Découvrez comment créer, gérer et supprimer des tables de routage Azure avec Ansible. Découvrez également comment créer et supprimer des itinéraires.
keywords: ansible, azure, devops, bash, playbook, réseaux, routes, table de routage
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659795"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Tutoriel : Configurer des tables de routage Azure avec Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure achemine automatiquement le trafic entre les sous-réseaux, les réseaux virtuels et les réseaux locaux Azure. Si vous avez besoin d’une maîtrise plus stricte du routage de votre environnement, vous pouvez créer une [table de routage](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Créer une table de routage
> Créer un réseau virtuel et un sous-réseau
> Associer une table de routage à un sous-réseau
> Dissocier une table de routage d’un sous-réseau
> Créer et supprimer des itinéraires
> Interroger une table de routage
> Supprimer une table de routage

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Créer une table de routage

Le code du playbook de cette section crée une table de routage. Pour plus d’informations sur les limites des tables de routage, voir [Limites Azure](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Enregistrez le playbook suivant en tant que `route_table_create.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Le code du playbook de cette section :

* Crée un réseau virtuel
* Crée un sous-réseau au sein du réseau virtuel
* Associe une table de routage au sous-réseau

Les tables de routage ne sont pas associées à des réseaux virtuels, mais au sous-réseau d’un réseau virtuel.

Le réseau virtuel et la table de routage doivent se trouver dans le même abonnement et au même emplacement Azure.

Les sous-réseaux et les tables de routage entretiennent une relation un-à-plusieurs. Il est possible de définir un sous-réseau en y associant zéro ou une seule table de routage. Les tables de routage peuvent être associés à zéro, un ou plusieurs sous-réseaux. 

Le trafic provenant du sous-réseau est acheminé selon :

- les itinéraires définis dans les tables de routage ;
- les [itinéraires](/azure/virtual-network/virtual-networks-udr-overview#default) par défaut ;
- les itinéraires propagés à partir d’un réseau local.

Le réseau virtuel doit être connecté à une passerelle de réseau virtuel Azure, qui peut être ExpressRoute ou un VPN si vous utilisez BGP avec une passerelle VPN.

Enregistrez le playbook suivant en tant que `route_table_associate.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissocier une table de routage d’un sous-réseau

Le code du playbook de cette section dissocie une table de routage d’un sous-réseau.

Pour dissocier une table de routage d’un sous-réseau, définissez `route_table` sur `None`. 

Enregistrez le playbook suivant en tant que `route_table_dissociate.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Créer un itinéraire

Le code du playbook de cette section crée un itinéraire dans une table de routage. 

Enregistrez le playbook suivant en tant que `route_create.yml` :

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

Avant d’exécuter le playbook, consultez les notes suivantes :

* `virtual_network_gateway` est défini comme `next_hop_type`. Pour plus d’informations sur la façon dont Azure sélectionne les itinéraires, voir [Vue d’ensemble du routage](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` est défini comme `10.1.0.0/16`. Le préfixe ne peut pas être dupliqué dans la table de routage.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Supprimer un itinéraire

Le code du playbook de cette section supprime un itinéraire d’une table de routage.

Enregistrez le playbook suivant en tant que `route_delete.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Récupérer les informations de la table de routage

Le code du playbook de cette section utilise le module Ansible `azure_rm_routetable_facts` pour récupérer les informations de la table de routage.

Enregistrez le playbook suivant en tant que `route_table_facts.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Supprimer une table de routage

Le code du playbook de cette section supprime une table de routage.

Lorsqu’une table de routage est supprimée, tous ses itinéraires le sont également.

Il n’est pas possible de supprimer une table de routage associée à un sous-réseau. [Dissociez la table de routage de tous les sous-réseaux](#dissociate-a-route-table-from-a-subnet) avant de tenter de la supprimer. 

Enregistrez le playbook suivant en tant que `route_table_delete.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Ansible sur Azure](/azure/ansible/)