---
title: Mettre à l’échelle des applications web Azure App Service avec Ansible
description: Découvrez comment utiliser Ansible pour créer une application web avec Java 8 et le runtime du conteneur Tomcat dans App Service sur Linux
ms.service: ansible
keywords: ansible, azur, devops, bash, playbook, Azure App Service, Web App, scale, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 4ef8320d3eba841ee64557e31e63b4e79ee3aa92
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159891"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Mettre à l’échelle des applications web Azure App Service avec Ansible
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (ou simplement Web Apps) héberge des applications web, des API REST et le backend mobile. Vous pouvez développer dans votre langage préféré&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Cet article explique comment utiliser Ansible pour mettre à l’échelle une application dans Azure App Service.

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service Web Apps** - Si vous ne disposez pas encore d'application web Azure App Service, vous pouvez [Créer des applications web Azure avec Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Montée en puissance d’une application dans App Service
Vous pouvez monter en puissance en modifiant le niveau tarifaire du plan App Service auquel appartient votre application. Cette section présente un exemple de playbook Ansible qui définit l'opération suivante :
- Obtenir les faits d'un plan App Service existant
- Mettre à jour le plan App Service vers S2 avec trois rôles de travail

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

Enregistrez ce playbook en tant que fichier *webapp_scaleup.yml*.

Pour exécuter le playbook, utilisez la commande **ansible-playbook** comme suit :
```bash
ansible-playbook webapp_scaleup.yml
```

Une fois le playbook exécuté, une sortie similaire à l’exemple suivant montre que le plan App Service a été correctement mis à jour vers S2 avec trois rôles de travail :
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App service plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Ansible sur Azure](https://docs.microsoft.com/azure/ansible/)