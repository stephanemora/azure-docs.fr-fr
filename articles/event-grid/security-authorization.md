---
title: Sécurité et authentification Azure Event Grid
description: Détaille Azure Event Grid et ses concepts.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601038"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorisation de l’accès aux ressources Event Grid
Azure Event Grid vous permet de contrôler le niveau d’accès accordé aux utilisateurs leur permettant d’effectuer différentes **opérations de gestion** telles que répertorier et créer des abonnements aux événements et générer des clés. Event Grid utilise le contrôle d’accès en fonction du rôle Azure (Azure RBAC).

> [!NOTE]
> Azure Event Grid ne prend pas en charge Azure RBAC pour la publication d’événements dans des rubriques ou domaines Event Grid. Utilisez une clé ou un jeton de signature d’accès partagé (SAP) pour authentifier les clients qui publient des événements. Pour plus d’informations, consultez [Authentifier les clients de publication](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Types d’opération
Pour obtenir la liste des opérations prises en charge par Azure Event Grid, exécutez la commande Azure CLI suivante : 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Les opérations suivantes retournent des informations potentiellement confidentielles, qui sont filtrées dans les opérations de lecture normales. Il est recommandé de restreindre l’accès à ces opérations. 

* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action


## <a name="built-in-roles"></a>Rôles intégrés
Event Grid fournit les trois rôles intégrés suivants. 

Les rôles Lecteur de l’abonnement Event Grid et les rôles Contributeur de l’abonnement Event Grid permettent de gérer les abonnements aux événements. Ceux-ci sont importants lors de l’implémentation de [domaines d’événements](event-domains.md) car ils fournissent aux utilisateurs les autorisations nécessaires pour s’abonner aux rubriques de votre domaine d’événements. Ces rôles sont centrés sur les abonnements aux événements et ne donnent pas accès aux actions telles que la création de rubriques.

Le rôle Contributeur Event Grid vous permet de créer et de gérer des ressources Event Grid. 


| Role | Description |
| ---- | ----------- | 
| [Lecteur de l’abonnement Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Vous permet de gérer les opérations d’abonnement aux événements Event Grid. |
| [Contributeur de l’abonnement Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Vous permet de lire les abonnements aux événements Event Grid. |
| [Contributeur Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Vous permet de créer et de gérer des ressources Event Grid. |


> [!NOTE]
> Sélectionnez des liens dans la première colonne pour accéder à un article qui fournit plus de détails sur le rôle. Pour obtenir des instructions sur l’affectation d’utilisateurs ou de groupes à des rôles RBAC, consultez [cet article](../role-based-access-control/quickstart-assign-role-user-portal.md).


## <a name="custom-roles"></a>Rôles personnalisés

Si vous avez besoin de spécifier des autorisations autres que les rôles intégrés, vous pouvez créer des rôles personnalisés.

Voici des exemples de définitions de rôle dans Event Grid permettant aux utilisateurs d’effectuer différentes actions. Ces rôles personnalisés sont différents des rôles intégrés car ils confèrent un accès qui va au-delà des abonnements aux événements.

**EventGridReadOnlyRole.json** : autorise uniquement les opérations en lecture seule.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json** : autorise des actions de publication limitées, et interdit les actions de suppression.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json** : autorise toutes les actions Event Grid.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Vous pouvez créer des rôles personnalisés avec [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md) et [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Chiffrement au repos

Tous les événements ou données écrits sur le disque par le service Event Grid sont chiffrés à l’aide d’une clé managée par Microsoft, ce qui garantit un chiffrement au repos. En outre, la durée maximale de conservation des événements ou données est de 24 heures, conformément à la [stratégie de nouvelles tentatives Event Grid](delivery-and-retry.md). Event Grid supprime automatiquement tous les événements ou données après 24 heures, ou la durée de vie de l’événement, selon la valeur la plus faible.

## <a name="permissions-for-event-subscriptions"></a>Autorisations pour les abonnements aux événements
Si vous utilisez un gestionnaire d’événements qui n’est pas un WebHook (par exemple, un Event Hub ou un stockage File d’attente), vous avez besoin d’un accès en écriture à cette ressource. Cette vérification des autorisations empêche qu’un utilisateur non autorisé envoie des événements à votre ressource.

Vous devez disposer de l’autorisation **Microsoft.EventGrid/EventSubscriptions/Write** sur la ressource correspondant à la source de l’événement. Vous avez besoin de cette autorisation, car vous rédigez un nouvel abonnement dans la portée de la ressource. La ressource nécessaire diffère si vous vous abonnez à une rubrique du système ou à une rubrique personnalisée. Les deux types sont décrits dans cette section.

### <a name="system-topics-azure-service-publishers"></a>Rubriques du système (éditeurs du service Azure)
Pour les rubriques système, si vous n’êtes pas le propriétaire ou le contributeur de la ressource source, vous devez avoir l’autorisation d’écrire un nouvel abonnement d’événement au niveau de l’étendue de la ressource qui publie l’événement. La ressource est au format suivant : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Par exemple, pour vous abonner à un événement sur un compte de stockage nommé **myacct**, il vous faut l’autorisation Microsoft.EventGrid/EventSubscriptions/Write sur :`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Rubriques personnalisées
Dans les rubriques personnalisées, vous avez besoin de l’autorisation de rédiger un nouvel abonnement à un événement dans la portée de la rubrique Event Grid. La ressource est au format suivant : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Par exemple, pour vous abonner à une rubrique personnalisée nommée **mytopic**, il vous faut l’autorisation Microsoft.EventGrid/EventSubscriptions/Write sur :`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [Event Grid](overview.md)
