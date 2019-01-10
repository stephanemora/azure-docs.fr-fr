---
title: Créer des applications web Azure avec Ansible
description: Découvrez comment utiliser Ansible pour créer une application web avec Java 8 et le runtime du conteneur Tomcat dans App Service sur Linux
ms.service: ansible
keywords: ansible, azur, devops, bash, playbook, Azure App Service, Web App, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 106fe11355d629e57f71b12d1125d188b98c2236
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051343"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Créer des applications web Azure App Service avec Ansible
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (ou simplement Web Apps) héberge des applications web, des API REST et des backends mobiles. Vous pouvez développer dans votre langage préféré&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Cet article vous explique comment utiliser Ansible pour créer une application web à l’aide du runtime Java. 

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.7 est nécessaire pour exécuter les exemples de playbooks suivants dans ce didacticiel.

## <a name="create-a-simple-app-service"></a>Création d’un service d’application simple
Cette section présente un exemple de playbook Ansible qui définit les ressources suivantes :
- Groupe de ressources, dans lequel votre plan App Service et votre application web seront déployés
- Web App avec Java 8 et le runtime du conteneur Tomcat dans App Service sur Linux

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
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
          name: "{{ plan_name }}"
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
Enregistrez le playbook précédent en tant que **firstwebapp.yml**.

Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook firstwebapp.yml
```

La sortie de l’exécution du playbook Ansible indique que l’application web a été créée avec succès :

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Créer un service d’application à l’aide de Traffic Manager
Vous pouvez utiliser [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) pour contrôler la façon dont les requêtes des clients web sont distribuées aux applications dans Azure App Service. Lorsque les points de terminaison App Service sont ajoutés à un profil Azure Traffic Manager, Traffic Manager effectue le suivi de l’état de vos applications App Service. Les états peuvent être en cours d’exécution, arrêté et supprimé. Traffic Manager peut alors décider du point de terminaison qui doit recevoir le trafic.

Dans App Service, une application s’exécute dans un [plan App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
). Un plan App Service définit un ensemble de ressources de calcul nécessaires à l’exécution d’une application web. Vous pouvez gérer votre plan App Service et votre application web dans différents groupes.

Cette section présente un exemple de playbook Ansible qui définit les ressources suivantes :
- Groupe de ressources, dans lequel votre plan de service d’application sera déployé
- Plan App Service
- Groupe de ressources secondaire, dans lequel votre application web sera déployée
- Web App avec Java 8 et le runtime du conteneur Tomcat dans App Service sur Linux
- Profil Traffic Manager
- Point de terminaison de Traffic Manager, en utilisant le site web créé

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
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
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
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
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```
Enregistrez le playbook ci-dessus sous **webapp.yml**, ou [téléchargez le playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook webapp.yml
```

La sortie de l’exécution du playbook Ansible indique que le plan App Service, l’application Web, le profil de Traffic Manager et le point de terminaison ont été créés avec succès :
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

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
> [Mettre à l’échelle des applications web Azure App Service avec Ansible](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)