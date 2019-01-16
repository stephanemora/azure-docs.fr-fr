---
title: Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques dans Azure avec Ansible
description: Découvrez comment utiliser Ansible pour mettre à l’échelle un groupe de machines virtuelles identiques avec la mise à l’échelle automatique dans Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, mise à l’échelle, mise à l’échelle automatique, machine virtuelle, groupe de machines virtuelles identiques, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 7721dba37131616122f8a5a902e3c63de5c7157f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157052"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Mettre à l’échelle automatiquement un groupe de machines virtuelles identiques dans Azure avec Ansible
Ansible vous permet d’automatiser le déploiement et la configuration de ressources dans votre environnement. Vous pouvez utiliser Ansible pour gérer votre groupe de machines virtuelles identiques dans Azure comme vous le feriez pour toute autre ressource Azure. 

Lorsque vous créez un groupe identique, vous définissez le nombre d’instances de machine virtuelle que vous souhaitez exécuter. À mesure que la demande de votre application change, vous pouvez augmenter ou diminuer automatiquement le nombre d’instances de machine virtuelle. La capacité de mise à l’échelle automatique vous permet de suivre la demande du client ou de répondre aux changements de performances de votre application tout au long de son cycle de vie. Dans cet article, vous allez créer un paramètre de mise à l’échelle et l’associer à un groupe existant de machines virtuelles identiques. Dans le paramètre de mise à l’échelle, vous pouvez configurer une règle pour augmenter ou diminuer la taille des instances comme vous le désirez.

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)][!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Un groupe existant de machines virtuelles identiques Azure. - Si vous n’en avez pas, [Créer des groupes de machines virtuelles identiques dans Azure avec Ansible](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Ansible 2.7 est nécessaire pour exécuter les exemples de playbooks suivants dans ce didacticiel. 

## <a name="auto-scale-based-on-a-schedule"></a>Mise à l’échelle automatique en fonction d’une planification   
Pour activer la mise à l’échelle automatique sur un groupe identique, vous devez d’abord définir un profil de mise à l’échelle automatique. Ce profil définit les capacités par défaut, minimale et maximale du groupe identique. Ces limites vous permettent de contrôler le coût en évitant de créer continuellement des instances de machine virtuelle et d’équilibrer les performances acceptables sur un nombre minimal d’instances qui restent dans un événement de diminution du nombre d’instances. 

Vous pouvez diminuer ou augmenter la taille des instances dans VM Scale Sets selon une planification périodique ou à une date donnée. Cette section présente un exemple de playbook Ansible qui crée un paramètre de mise à l’échelle automatique portant le nombre d’instances de machine virtuelle à trois dans vos groupes identiques à 10h00 tous les lundis (fuseau horaire du Pacifique). 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Enregistrez ce playbook sous le nom *vmss-auto-scale.yml*. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Mise à l’échelle automatique en fonction des données de performances
Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans vos groupes identiques augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Vous contrôlez les métriques à surveiller, telles que l’usage du processeur ou du disque, la durée pendant laquelle la charge de l’application doit respecter un seuil donné, et le nombre d’instances de machine virtuelle à ajouter au groupe identique.

Vous pouvez diminuer ou augmenter la taille des instances dans VM Scale Sets en fonction de certains seuils de métriques de performances, selon une planification périodique ou à une date donnée. Cette section présente un exemple de playbook Ansible qui vérifie la charge de travail des 10 dernières minutes à 18h00 tous les lundis (fuseau horaire du Pacifique), augmente le nombre d’instances de machine virtuelle dans vos groupes identiques en le portant à quatre ou le diminue en l’amenant à un, en fonction des mesures d’utilisation de l’UC. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Enregistrez ce playbook sous le nom *vmss-auto-scale-metrics.yml*. Pour exécuter le playbook Ansible, utilisez la commande **ansible-playbook** comme suit :

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Obtenir des informations pour les paramètres existants de mise à l’échelle automatique
Vous pouvez obtenir les détails d’un paramètre de mise à l’échelle via le module *azure_rm_autoscale_facts* avec le playbook comme suit :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Désactiver les paramètres de mise à l’échelle automatique
Vous pouvez désactiver le paramètre de mise à l’échelle automatique en passant de `enabled: true` à `enabled: false`, en supprimant les paramètres de mise à l’échelle automatique avec le playbook comme suit :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Exemple de playbook Ansible pour des groupes de machines virtuelles identiques](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)