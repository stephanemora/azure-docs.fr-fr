---
title: 'Tutoriel : Gérer le trafic web avec Azure Application Gateway à l’aide d’Ansible'
description: Découvrez comment utiliser Ansible pour créer et configurer une passerelle Azure Application Gateway pour gérer le trafic Web
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, trafic web
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 07f75e39b8c6f592ecd4c48697527493b1109bb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156615"
---
# <a name="tutorial-manage-web-traffic-with-azure-application-gateway-using-ansible"></a>Tutoriel : Gérer le trafic web avec Azure Application Gateway à l’aide d’Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Application Gateway](/azure/application-gateway/overview) est un équilibreur de charge du trafic web qui vous permet de gérer le trafic vers vos applications web. En fonction de l’adresse IP et du port sources, les équilibreurs de charge traditionnels acheminent le trafic vers une adresse IP de et un port de destination. Application Gateway vous offre un niveau plus précis du contrôle de l’acheminement du trafic en fonction de l’URL. Par exemple, vous pouvez définir que si `images` est le chemin de l’URL, le trafic est acheminé vers un ensemble spécifique de serveurs (appelé pool) configuré pour des images.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurer un réseau
> * Créer deux instances de conteneurs Azure avec des images HTTPD
> * Créer une passerelle d’application qui fonctionne avec les instances de conteneurs Azure dans le pool de serveurs

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Le code de playbook dans cette section crée un groupe de ressources Azure. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont configurées.  

Enregistrez le playbook suivant en tant que `rg.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Avant d’exécuter le playbook, consultez les notes suivantes :

- Le nom du groupe de ressources est `myResourceGroup`. Cette valeur est utilisée dans tout le tutoriel.
- Le groupe de ressources est créé à l’emplacement `eastus`.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Le code de playbook dans cette section crée un réseau virtuel pour permettre à la passerelle d’application de communiquer avec d’autres ressources.

Enregistrez le playbook suivant en tant que `vnet_create.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Avant d’exécuter le playbook, consultez les notes suivantes :

* La section `vars` contient les valeurs qui sont utilisées pour créer les ressources réseau. 
* Vous devrez modifier ces valeurs pour votre environnement spécifique.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Créer des serveurs

Le code de playbook dans cette section crée deux instances de conteneur Azure avec des images HTTPD à utiliser en tant que serveurs web pour la passerelle d’application.  

Enregistrez le playbook suivant en tant que `aci_create.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Le code de playbook dans cette section crée une passerelle d’application nommée `myAppGateway`.  

Enregistrez le playbook suivant en tant que `appgw_create.yml` :

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Avant d’exécuter le playbook, consultez les notes suivantes :

* `appGatewayIP` est définie dans le bloc `gateway_ip_configurations`. Une référence de sous-réseau est requise pour la configuration IP de la passerelle.
* `appGatewayBackendPool` est définie dans le bloc `backend_address_pools`. Une passerelle d’application doit avoir au moins un pool d’adresses principal.
* `appGatewayBackendHttpSettings` est définie dans le bloc `backend_http_settings_collection`. Ils spécifient que le port 80 et le protocole HTTP sont utilisés pour la communication.
* `appGatewayHttpListener` est définie dans le bloc `backend_http_settings_collection`. Il s’agit de l’écouteur par défaut associé à appGatewayBackendPool.
* `appGatewayFrontendIP` est définie dans le bloc `frontend_ip_configurations`. Il assigne myAGPublicIPAddress à appGatewayHttpListener.
* `rule1` est définie dans le bloc `request_routing_rules`. Il s’agit de la règle de routage par défaut associée à appGatewayHttpListener.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook appgw_create.yml
```

La création de la passerelle d’application peut prendre plusieurs minutes.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

1. Dans la section [Créer un groupe de ressources](#create-a-resource-group), vous spécifiez un emplacement. Notez sa valeur.

1. Dans la section [Créer des ressources réseau](#create-network-resources), vous spécifiez le domaine. Notez sa valeur.

1. Pour l’URL de test en remplaçant le modèle suivant par l’emplacement et le domaine : `http://<domain>.<location>.cloudapp.azure.com`.

1. Accédez à l’URL de test.

1. Si vous voyez la page suivante, la passerelle d’application fonctionne comme prévu.

    ![Test réussi d’une passerelle d’application opérationnelle](media/ansible-application-gateway-configure/application-gateway.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le code suivant en tant que `cleanup.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ansible sur Azure](/azure/ansible/)