---
title: Création d’applications Web Azure avec Ansible (préversion)
description: Découvrez comment utiliser Ansible pour créer une application Web avec Java 8 et le runtime du conteneur Tomcat dans App Service sur Linux
ms.service: ansible
keywords: ansible, azur, devops, bash, playbook, Azure App Service, Web App, Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 1899b1fc1e0a38d859fb3a7ce2153585579650f3
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586671"
---
# <a name="create-azure-app-service-web-apps-using-ansible-preview"></a>Création d’applications Web App Service d’Azure avec Ansible (préversion)
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (ou simplement Web Apps) est un service pour l’hébergement d’applications web, d’API REST et de backends mobiles. Vous pouvez développer dans votre langage préféré, par exemple .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Cet article vous explique comment utiliser Ansible pour créer une application Web avec Java Runtime. 

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 est nécessaire pour exécuter les exemples de playbooks suivants dans ce didacticiel. Vous pouvez installer la version 2. 7 d’Ansible RC en exécutant `sudo pip install ansible[azure]==2.7.0rc2`. La version 2.7 d’Ansible sera disponible à partir d’octobre 2018. Ensuite, vous n’aurez plus besoin de préciser la version ici, car la version 2.7 sera celle par défaut. 

## <a name="create-a-simple-app-service"></a>Création d’un service d’application simple
Cette section présente un exemple de playbook Ansible qui définit les ressources suivantes :
- Groupe de ressources, dans lequel votre plan de service d’application et votre application Web seront déployés
- Web App, application Web avec Java 8 et runtime du conteneur Tomcat dans App Service sur Linux

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myfirstResourceGroup
    webapp_name: myfirstWebApp
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: myappplan
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```
Enregistrez le playbook ci-dessus avec le nom firstwebapp.yml.

Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook firstwebapp.yml
```

La sortie de l’exécution du playbook Ansible indique que l’application Web a été créée avec succès :

```
TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ******************************
 [WARNING]: Azure API profile latest does not define an entry for
WebSiteManagementClient
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   
```

## <a name="create-app-service-with-traffic-manager"></a>Création d’un App Service avec Traffic Manager
Vous pouvez utiliser [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) pour contrôler la façon dont les requêtes des clients web sont distribuées aux applications dans Azure App Service. Lorsque des points de terminaison App Service sont ajoutés à un profil Azure Traffic Manager, ce dernier conserve une trace du statut de vos applications App Service (en cours d’exécution, arrêtées ou supprimées) pour pouvoir décider du point de terminaison qui doit recevoir le trafic.

Dans App Service, une application s’exécute dans un [plan App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
). Un plan App Service définit un ensemble de ressources de calcul nécessaires à l’exécution d’une application web. Vous pouvez gérer votre plan App Service et Web App dans différents groupes.

Cette section présente un exemple de playbook Ansible qui définit les ressources suivantes :
- Groupe de ressources, dans lequel votre plan de service d’application sera déployé
- Plan App Service
- Groupe de ressources secondaires, dans lequel votre application Web sera déployée
- Web App, application Web avec Java 8 et runtime du conteneur Tomcat dans App Service sur Linux
- Profil Traffic Manager
- Point de terminaison de Traffic Manager, en utilisant le site Web créé

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_resource_group: planResourceGroup
    app_name: myLinuxWebApp
    location: eastus
    linux_plan_name: myAppServicePlan
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ plan_resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ plan_resource_group }}"
      name: "{{ linux_plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ app_name }}"
        plan:
          resource_group: "{{ plan_resource_group }}"
          name: "{{ linux_plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ app_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"

```
Enregistrez le playbook ci-dessus sous webapp.yml, ou [téléchargez le playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook webapp.yml
```

La sortie de l’exécution du playbook Ansible indique que le plan de service de l’application, l’application Web, le profil de Traffic Manager et le point de terminaison ont été créés avec succès :
```
TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Ansible sur Azure](https://docs.microsoft.com/azure/ansible/)