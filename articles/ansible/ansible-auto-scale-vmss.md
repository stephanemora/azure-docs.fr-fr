---
title: 'Tutoriel : Mettre à l’échelle automatiquement des groupes identiques de machines virtuelles dans Azure à l’aide d’Ansible'
description: Découvrez comment utiliser Ansible pour mettre automatiquement à l’échelle des groupes de machines virtuelles identiques dans Azure
keywords: ansible, azure, devops, bash, playbook, mise à l’échelle, mise à l’échelle automatique, machine virtuelle, groupe de machines virtuelles identiques, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156818"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutoriel : Mise à l’échelle automatique de groupes de machines virtuelles identiques dans Azure à l’aide d’Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

La fonctionnalité d’ajustage automatique du nombre d’instances de machine virtuelle est appelée [mise à l’échelle automatique](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). L’avantage de la mise à l’échelle automatique est qu’elle réduit la surcharge de gestion pour surveiller et optimiser les performances de votre application. La mise à l’échelle automatique peut être configurée en réponse à la demande ou selon une planification définie. Avec Ansible, vous pouvez spécifier les règles de mise à l’échelle automatique qui définissent les performances acceptables pour une expérience utilisateur positive.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Définir un profil de mise à l’échelle automatique
> * Mise à l’échelle automatique selon une planification périodique
> * Mise à l’échelle automatique selon les performances des applications
> * Récupérer des informations sur les paramètres de mise à l’échelle automatique 
> * Désactiver un paramètre de mise à l’échelle automatique

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Mise à l’échelle automatique en fonction d’une planification

Pour activer la mise à l’échelle automatique sur un groupe identique, vous devez d’abord définir un profil de mise à l’échelle automatique. Ce profil définit les capacités par défaut, minimale et maximale du groupe identique. Ces limites vous permettent de contrôler le coût en évitant de créer continuellement des instances de machine virtuelle et d’équilibrer les performances acceptables sur un nombre minimal d’instances qui restent dans un événement de diminution du nombre d’instances. 

Ansible vous permet de mettre à l’échelle vos groupes identiques à une date spécifique ou selon une planification périodique.

Le code de playbook dans cette section augmente le nombre d’instances de machine virtuelle à trois à 10 h 00 tous les lundis.

Enregistrez le playbook suivant en tant que `vmss-auto-scale.yml` :

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Mise à l’échelle automatique selon les données de performances

Si la demande de votre application augmente, la charge sur les instances de machine virtuelle dans vos groupes identiques augmente. Si cette augmentation de la charge est cohérente, au lieu d’une brève demande, vous pouvez configurer des règles de mise à l’échelle automatique pour augmenter le nombre d’instances de machine virtuelle dans le groupe identique. Lorsque ces instances de machine virtuelle sont créées et que vos applications sont déployées, le groupe identique commence à distribuer le trafic vers les instances via l’équilibreur de charge. Ansible vous permet de contrôler les métriques à surveiller, telles que l’utilisation de l’UC, l’utilisation du disque et temps de chargement de l’application. Vous pouvez effectuer un scale-in et un scale-out dans des groupes identiques selon des seuils de métriques des performances, sur la base d’une planification périodique ou à une date particulière. 

Le code de playbook dans cette section vérifie la charge de travail de l’UC pendant les 10 minutes précédentes à 18 h 00 tous les lundis. 

Selon les métriques en pourcentage de l’UC, le playbook effectue l’une des actions suivantes :

- Montée en charge du nombre d’instances de machine virtuelle à quatre
- Diminution de la taille des instances de machine virtuelle à une

Enregistrez le playbook suivant en tant que `vmss-auto-scale-metrics.yml` :

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
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

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Obtenir des informations sur les paramètres de mise à l’échelle automatique 

Le code de playbook dans cette section utilise le module `azure_rm_autoscale_facts` pour récupérer les détails du paramètre de mise à l’échelle automatique.

Enregistrez le playbook suivant en tant que `vmss-auto-scale-get-settings.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Désactivez les paramètres de mise à l’échelle automatique

Il existe deux façons de désactiver les paramètres de mise à l’échelle automatique. L’une consiste à modifier la clé `enabled` clés de `true` à `false`. La seconde consiste à supprimer le paramètre.

Le code de playbook dans cette section supprime le paramètre de mise à l’échelle automatique. 

Enregistrez le playbook suivant en tant que `vmss-auto-scale-delete-setting.yml` :

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Tutoriel : Mettre à jour une image personnalisée de groupes de machines virtuelles identiques Azure à l’aide d’Ansible](./ansible-vmss-update-image.md)