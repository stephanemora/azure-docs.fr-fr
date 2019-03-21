---
title: Gérer le trafic web avec Azure Application Gateway à l’aide d’Ansible
description: Découvrez comment utiliser Ansible pour créer et configurer une passerelle Azure Application Gateway pour gérer le trafic Web
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, application gateway, load balancer, trafic web
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 83f21573af7ec523acc376c4b3364cdcfb47f96f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792138"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Gérer le trafic web avec Azure Application Gateway à l’aide d’Ansible

[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) est un équilibreur de charge du trafic web qui vous permet de gérer le trafic vers vos applications web.

Ansible vous aide à automatiser le déploiement et la configuration de ressources dans votre environnement. Cet article explique comment utiliser Ansible pour créer une passerelle d’application. Il explique également comment utiliser la passerelle pour gérer le trafic vers les deux serveurs Web exécutés dans Azure Container Instances.

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer deux instances de conteneurs Azure avec des images HTTPD
> * Créer une passerelle d’application qui fonctionne avec les instances de conteneurs Azure dans le pool de serveurs

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 est nécessaire pour exécuter les exemples de playbooks suivants dans ce didacticiel. 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.  

L’exemple suivant crée un groupe de ressources nommé **myResourceGroup** à l’emplacement **eastus**.

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

Enregistrez ce playbook sous *rg.yml*. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Tout d’abord, créez un réseau virtuel pour que la passerelle d’application puisse communiquer avec d’autres ressources.

L’exemple suivant crée un réseau virtuel nommé **myVNet**, un sous-réseau nommé **myAGSubnet** et une adresse IP publique nommée **myAGPublicIPAddress** avec un domaine nommé **mydomain**.

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

Enregistrez ce playbook sous *vnet_create.yml*. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Créer des serveurs

L’exemple suivant vous explique comment créer deux instances de conteneurs Azure avec des images HTTPD, à utiliser en tant que serveurs Web pour la passerelle d’application.  

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

Enregistrez ce playbook sous *aci_create.yml*. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

L’exemple suivant crée une passerelle d’application nommée **myAppGateway** avec les configurations back end, front end et HTTP.  

* **appGatewayIP** est défini dans le bloc **gateway_ip_configurations**. Une référence de sous-réseau est requise pour la configuration IP de la passerelle.
* **appGatewayBackendPool** est défini dans le bloc **backend_address_pools**. Une passerelle d’application doit avoir au moins un pool d’adresses principal.
* **appGatewayBackendHttpSettings** est défini dans le bloc **backend_http_settings_collection**. Il spécifie que le port 80 et un protocole HTTP sont utilisés pour la communication.
* **appGatewayHttpListener** est défini dans le bloc **backend_http_settings_collection**. Il s’agit de l’écouteur par défaut associé à appGatewayBackendPool.
* **appGatewayFrontendIP** est défini dans le bloc **frontend_ip_configurations**. Il assigne myAGPublicIPAddress à appGatewayHttpListener.
* **rule1** est défini dans le bloc **request_routing_rules**. Il s’agit de la règle de routage par défaut associée à appGatewayHttpListener.

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

Enregistrez ce playbook sous *appgw_create.yml*. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook appgw_create.yml
```

La création de la passerelle d’application peut prendre plusieurs minutes.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Dans l’exemple de playbook pour les ressources réseau, vous créez le domaine **mydomain** dans **eastus**. Accédez à `http://mydomain.eastus.cloudapp.azure.com` dans votre navigateur. Si vous voyez la page suivante, la passerelle d’application fonctionne comme prévu.

![Test réussi d’une passerelle d’application opérationnelle](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources, vous pouvez les supprimer en exécutant le code ci-dessous. Il supprime un groupe de ressources appelé **myResourceGroup**.

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

Enregistrez ce playbook sous *rg_delete*.yml. Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ansible sur Azure](https://docs.microsoft.com/azure/ansible/)
