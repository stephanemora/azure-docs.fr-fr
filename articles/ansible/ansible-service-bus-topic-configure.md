---
title: 'Tutoriel : Configurer des rubriques dans Azure Service Bus avec Ansible'
description: Découvrez comment utiliser Ansible pour créer une rubrique Azure Service Bus.
keywords: Ansible, Azure, DevOps, Bash, playbook, Service Bus, rubriques, abonnements
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155795"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Tutoriel : Configurer des rubriques dans Azure Service Bus avec Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Création d'une rubrique
> * Création d’un abonnement
> * Créer une stratégie SAP
> * Récupérer les informations de l’espace de noms
> * Récupérer les informations des rubriques et des abonnements
> * Révoquer une stratégie SAP

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Créer la rubrique Service Bus

Le code de l’exemple de playbook crée les ressources suivantes :
- Groupe de ressources Azure
- Espace de noms Service Bus dans le groupe de ressources
- Rubrique Service Bus avec l’espace de noms

Enregistrez le playbook suivant en tant que `servicebus_topic.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Créer l’abonnement

Le code de l’exemple de playbook crée l’abonnement sous une rubrique Service Bus. Les rubriques Azure Service Bus peuvent comporter plusieurs abonnements. Un utilisateur abonné à une rubrique peut recevoir une copie de tous les messages envoyés à cette rubrique. Les abonnements sont des entités nommées créées pour durer, mais qui peuvent expirer sur option.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Enregistrez le playbook suivant en tant que `servicebus_subscription.yml` :

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Créer la stratégie SAP

Une [signature d’accès partagé (SAP)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) est un mécanisme d’autorisation basée sur les revendications qui utilise des jetons. 

Le code de l’exemple de playbook crée deux stratégies SAP pour une file d’attente Service Bus avec différents privilèges.

Enregistrez le playbook suivant en tant que `servicebus_topic_policy.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Récupérer les informations des rubriques et des abonnements

Le code de l’exemple de playbook demande les informations suivantes :
- Informations sur la rubrique Service Bus
- Liste des détails de l’abonnement de la rubrique
 
Enregistrez le playbook suivant en tant que `servicebus_list.yml` :

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Avant d’exécuter le playbook, consultez les notes suivantes :
- La valeur `show_sas_policies` indique s’il faut afficher les stratégies SAP sous la file d’attente spécifiée. Par défaut, cette valeur est `False` afin d’éviter toute surcharge réseau supplémentaire.

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Exécutez le playbook à l’aide de la commande `ansible-playbook` :

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Ansible sur Azure](/azure/ansible/)