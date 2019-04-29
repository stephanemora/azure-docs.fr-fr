---
title: Créer des rôles personnalisés pour les ressources Azure à l'aide de l'API REST - Azure | Microsoft Docs
description: Apprenez à créer des rôles personnalisés avec contrôle d'accès en fonction du rôle (RBAC) pour les ressources Azure à l'aide de l'API REST. Cet article indique également comment répertorier, créer, mettre à jour et supprimer des rôles personnalisés.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596691"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Créer des rôles personnalisés pour les ressources Azure à l'aide de l'API REST

Si les [rôles intégrés prévus pour les ressources Azure](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Cet article explique comment créer et gérer des rôles personnalisés avec l’API REST.

## <a name="list-custom-roles"></a>Répertorier les rôles personnalisés

Pour répertorier tous les rôles personnalisés dans un répertoire, utilisez la [définitions de rôles - liste](/rest/api/authorization/roledefinitions/list) API REST.

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Remplacez *{filter}* avec le type de rôle.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrer en fonction du type CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Liste des rôles personnalisés dans une étendue

Pour répertorier les rôles personnalisés dans une étendue, utilisez le [définitions de rôles - liste](/rest/api/authorization/roledefinitions/list) API REST.

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les rôles.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{filter}* avec le type de rôle.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrer en fonction du type CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Une définition de rôle personnalisé de la liste par nom

Pour obtenir des informations sur un rôle personnalisé par son nom d’affichage, utilisez le [obtenir des définitions de rôles -](/rest/api/authorization/roledefinitions/get) API REST.

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les rôles.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{filter}* avec le nom d’affichage pour le rôle.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Utilisez la forme codée de l’URL du nom d’affichage exact du rôle. Par exemple, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'`. |

## <a name="list-a-custom-role-definition-by-id"></a>Liste d’une définition de rôle personnalisé par ID

Pour obtenir des informations sur un rôle personnalisé par son identificateur unique, utilisez le [obtenir des définitions de rôles -](/rest/api/authorization/roledefinitions/get) API REST.

1. Utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list) pour obtenir l’identificateur GUID du rôle.

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les rôles.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{roleDefinitionId}* par l’identificateur GUID de la définition du rôle.

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Pour créer un rôle personnalisé, utilisez l’API REST [Définitions de rôles – Créer ou mettre à jour](/rest/api/authorization/roledefinitions/createorupdate). Pour appeler cette API, vous devez être connecté avec un utilisateur se voit attribuer un rôle qui a le `Microsoft.Authorization/roleDefinitions/write` autorisation sur toutes les `assignableScopes`. Des rôles intégrés, uniquement [propriétaire](built-in-roles.md#owner) et [administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) inclure cette autorisation.

1. Consultez la liste des [opérations de fournisseur de ressources](resource-provider-operations.md) disponibles pour créer les autorisations de votre rôle personnalisé.

1. Utilisez un outil GUID pour générer un identificateur unique qui servira d’identificateur de rôle personnalisé. Cet identificateur est au format : `00000000-0000-0000-0000-000000000000`.

1. Commencez par la demande et le corps suivants :

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Dans l’URI, remplacez *{scope}* par le premier élément `assignableScopes` du rôle personnalisé.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{roleDefinitionId}* par l’identificateur GUID du rôle personnalisé.

1. Dans la propriété `assignableScopes` du corps de la demande, remplacez *{roleDefinitionId}* par l’identificateur GUID.

1. Remplacez *{subscriptionId}* par votre identificateur d’abonnement.

1. Dans la propriété `actions`, ajoutez les opérations autorisées par le rôle.

1. Dans la propriété `notActions`, ajoutez les opérations exclues des `actions` autorisées.

1. Dans les propriétés `roleName` et `description`, spécifiez un nom de rôle unique et une description. Pour plus d’informations sur les propriétés, voir [Rôles personnalisés](custom-roles.md).

    Voici un exemple de corps de demande :

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Mettre à jour un rôle personnalisé

Pour mettre à jour un rôle personnalisé, utilisez l’API REST [Définitions de rôles – Créer ou mettre à jour](/rest/api/authorization/roledefinitions/createorupdate). Pour appeler cette API, vous devez être connecté avec un utilisateur se voit attribuer un rôle qui a le `Microsoft.Authorization/roleDefinitions/write` autorisation sur toutes les `assignableScopes`. Des rôles intégrés, uniquement [propriétaire](built-in-roles.md#owner) et [administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) inclure cette autorisation.

1. Utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list) ou [Définitions de rôles – Obtenir](/rest/api/authorization/roledefinitions/get) pour obtenir des informations sur le rôle personnalisé. Pour plus d’informations, consultez l’ancien [liste des rôles personnalisés](#list-custom-roles) section.

1. Commencez par la requête suivante :

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dans l’URI, remplacez *{scope}* par le premier élément `assignableScopes` du rôle personnalisé.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{roleDefinitionId}* par l’identificateur GUID du rôle personnalisé.

1. En fonction des informations sur le rôle personnalisé, créez un corps de demande au format suivant :

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Mettez à jour le corps de la demande avec les modifications que vous souhaitez apporter au rôle personnalisé.

    Voici un exemple de corps de demande avec ajout d’une nouvelle action de paramètres de diagnostic :

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez l’API REST [Définitions de rôles – Supprimer](/rest/api/authorization/roledefinitions/delete). Pour appeler cette API, vous devez être connecté avec un utilisateur se voit attribuer un rôle qui a le `Microsoft.Authorization/roleDefinitions/delete` autorisation sur toutes les `assignableScopes`. Des rôles intégrés, uniquement [propriétaire](built-in-roles.md#owner) et [administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) inclure cette autorisation.

1. Utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list) ou [Définitions de rôles – Obtenir](/rest/api/authorization/roledefinitions/get) pour obtenir l’identificateur GUID du rôle personnalisé. Pour plus d’informations, consultez l’ancien [liste des rôles personnalisés](#list-custom-roles) section.

1. Commencez par la requête suivante :

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez supprimer le rôle personnalisé.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{roleDefinitionId}* par l’identificateur GUID du rôle personnalisé.

## <a name="next-steps"></a>Étapes suivantes

- [Rôles personnalisés pour les ressources Azure](custom-roles.md)
- [Gérer l'accès aux ressources Azure à l'aide du contrôle RBAC et de l'API REST](role-assignments-rest.md)
- [Référence de l'API REST Azure](/rest/api/azure/)