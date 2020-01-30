---
title: 'Tutoriel : Configurer des files d’attente dans Azure Service Bus à l’aide d’Ansible'
description: Découvrez comment créer une file d’attente Azure Service Bus à l’aide d’Ansible.
keywords: ansible, azure, devops, bash, playbook, service bus, file d’attente
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713223"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutoriel : Configurer des files d’attente dans Azure Service Bus à l’aide d’Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Créer une file d’attente
> * Créer une stratégie SAS
> * Récupérer les informations de l’espace de noms
> * Récupérer des informations de file d’attente
> * Révoquer la stratégie SAP de file d’attente

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Créer la file d’attente Service Bus

Le code de l’exemple de playbook crée les ressources suivantes :
- Groupe de ressources Azure
- Espace de noms Service Bus dans le groupe de ressources
- File d’attente Service Bus avec l’espace de noms

Enregistrez le playbook suivant en tant que `servicebus_queue.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Créer la stratégie SAP

Une [signature d’accès partagé (SAP)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) est un mécanisme d’autorisation basée sur les revendications qui utilise des jetons. 

Le code de l’exemple de playbook crée deux stratégies SAP pour une file d’attente Service Bus avec différents privilèges.

Enregistrez le playbook suivant en tant que `servicebus_queue_policy.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Avant d’exécuter le playbook, consultez les notes suivantes :
- La valeur `rights` représente le privilège dont dispose un utilisateur avec la file d’attente. Spécifiez l’une des valeurs suivantes : `manage`, `listen`, `send` ou `listen_send`.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Récupérer les informations de l’espace de noms

Le code de l’exemple de playbook demande les informations de l’espace de noms.

Enregistrez le playbook suivant en tant que `servicebus_namespace_info.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Avant d’exécuter le playbook, consultez les notes suivantes :
- La valeur `show_sas_policies` indique s’il faut afficher les stratégies SAP sous l’espace de noms spécifié. Par défaut, la valeur est `False` afin d’éviter toute surcharge réseau supplémentaire.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Récupérer des informations de file d’attente

L’exemple de code de playbook interroge les informations de file d’attente. 

Enregistrez le playbook suivant en tant que `servicebus_queue_info.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Avant d’exécuter le playbook, consultez les notes suivantes :
- La valeur `show_sas_policies` indique s’il faut afficher les stratégies SAP sous la file d’attente spécifiée. Par défaut, cette valeur est `False` afin d’éviter toute surcharge réseau supplémentaire.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Révoquer la stratégie SAP de file d’attente

Le code de l’exemple de playbook supprime une stratégie SAP de file d’attente.

Enregistrez le playbook suivant en tant que `servicebus_queue_policy_delete.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez les ressources créées dans cet article. 

Enregistrez le code suivant en tant que `cleanup.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"] 
> [Tutoriel : Configurer une rubrique dans Azure Service Bus à l’aide d’Ansible](ansible-service-bus-topic-configure.md)