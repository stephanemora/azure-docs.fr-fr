---
title: 'Tutoriel : Configurer des applications dans Azure App Service à l’aide d’Ansible'
description: Découvrez comment créer une application dans Azure App Service avec Java 8 et le runtime du conteneur Tomcat
keywords: ansible, azur, devops, bash, playbook, Azure App Service, Web App, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 2891ff47b17900c4c1c8e1c21f22495b65108fd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156557"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Tutoriel : Configurer des applications dans Azure App Service à l’aide d’Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer une application dans Azure App Service avec Java 8 et le runtime du conteneur Tomcat
> * Créer un profil Azure Traffic Manager
> * Définir un point de terminaison Traffic Manager à l’aide de l’application créée

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Créer un service d’application de base

Le code de playbook dans cette section définit les ressources suivantes :

* Groupe de ressources Azure dans lequel le plan App Service et l’application sont déployés
* Service d’application sur Linux avec Java 8 et le runtime du conteneur Tomcat

Enregistrez le playbook suivant en tant que `firstwebapp.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook firstwebapp.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Créez une application et utilisez Azure Traffic Manager

[Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) vous permet de contrôler comment les demandes des clients web sont distribuées aux applications dans Azure App Service. Lorsque les points de terminaison App Service sont ajoutés à un profil Azure Traffic Manager, Traffic Manager effectue le suivi de l’état de vos applications App Service. Les états peuvent être en cours d’exécution, arrêté et supprimé. Traffic Manager est utilisé pour décider quels points de terminaison doivent recevoir le trafic.

Dans App Service, une application s’exécute dans un [plan App Service](/azure/app-service/overview-hosting-plans). Un plan App Service définit un ensemble de ressources de calcul pour l’exécution d’une application. Vous pouvez gérer votre plan App Service et votre application web dans différents groupes.

Le code de playbook dans cette section définit les ressources suivantes :

* Groupe de ressources Azure au sein duquel le plan App Service est déployé
* Plan App Service
* Groupe de ressources Azure au sein duquel l’application est déployée
* Service d’application sur Linux avec Java 8 et le runtime du conteneur Tomcat
* Profil Traffic Manager
* Point de terminaison Traffic Manager utilisant l’application créée

Enregistrez le playbook suivant en tant que `webapp.yml` :

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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook webapp.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Tutoriel : Mettre à l’échelle des applications dans Azure App Service à l’aide d’Ansible](/azure/ansible/ansible-scale-azure-web-apps)