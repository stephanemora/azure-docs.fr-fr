---
title: 'Tutoriel : Mettre des applications à l’échelle dans Azure App Service avec Ansible'
description: Découvrez comment monter en puissance une application dans Azure App Service.
keywords: ansible, azur, devops, bash, playbook, Azure App Service, Web App, scale, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155917"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Tutoriel : Mettre des applications à l’échelle dans Azure App Service avec Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Obtenir les faits d'un plan App Service existant
> * Monter en puissance le plan App Service en passant à S2 avec trois rôles de travail

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Application Azure App Service** : si vous n’avez pas d’application Azure App Service, [configurez une application dans Azure App Service avec Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Monter en puissance une application

Il existe deux workflows de mise à l'échelle : *scale-up* et *scale-out*.

**Scale up :** acquérir davantage de ressources (processeur, mémoire, espace disque, machines virtuelles, etc.). Il s’agit de modifier le niveau tarifaire du plan App Service de l’application. 
**Effectuer un scale-out :** effectuer un scale-out signifie augmenter le nombre d’instances de machines virtuelles qui exécutent l’application (jusqu’à 20 instances selon le niveau tarifaire du plan App Service). La [mise à l'échelle automatique](/azure/azure-monitor/platform/autoscale-get-started) permet de mettre automatiquement à l’échelle le nombre d’instances en fonction des planifications et des règles prédéfinies.

Le code du playbook de cette section définit l’opération suivante :

* Obtenir les faits d'un plan App Service existant
* Mettre à jour le plan App Service vers S2 avec trois rôles de travail

Enregistrez le playbook suivant en tant que `webapp_scaleup.yml` :

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook webapp_scaleup.yml
```

Après avoir exécuté le playbook, vous voyez une sortie similaire aux résultats suivants :

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Ansible sur Azure](/azure/ansible/)