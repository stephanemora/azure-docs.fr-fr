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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cfcb7ef2a4fc02932f9d03c277b105c4b1e0ff3a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338765"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Créer des rôles personnalisés pour les ressources Azure à l'aide de l'API REST

Si les [rôles intégrés prévus pour les ressources Azure](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Cet article explique comment créer et gérer des rôles personnalisés avec l’API REST.

## <a name="list-roles"></a>Répertorier les rôles

Pour lister tous les rôles ou obtenir des informations sur l’un d’entre eux à partir de son nom d’affichage, utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/read` dans l’étendue. Plusieurs [rôles intégrés](built-in-roles.md) se voient accorder l’accès à cette opération.

1. Commencez par la demande suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les rôles.

    | Étendue | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{filter}* par la condition que vous souhaitez appliquer pour filtrer la liste des rôles.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Lister les rôles pouvant être affectés à l’étendue spécifiée et toutes ses étendues enfants. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Utilisez la forme codée de l’URL du nom d’affichage exact du rôle. Par exemple, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'`. |

### <a name="get-information-about-a-role"></a>Obtenir des informations sur un rôle

Pour obtenir des informations sur un rôle à partir de son identificateur de définition de rôle, utilisez l’API REST [Définitions de rôles – Obtenir](/rest/api/authorization/roledefinitions/get). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/read` dans l’étendue. Plusieurs [rôles intégrés](built-in-roles.md) se voient accorder l’accès à cette opération.

Pour obtenir des informations sur un unique rôle à partir de son nom d’affichage, voir la section précédente [Lister les rôles](custom-roles-rest.md#list-roles).

1. Utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list) pour obtenir l’identificateur GUID du rôle. Dans le cas des rôles intégrés, l’identificateur est également accessible dans [Rôles intégrés](built-in-roles.md).

1. Commencez par la demande suivante :

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

Pour créer un rôle personnalisé, utilisez l’API REST [Définitions de rôles – Créer ou mettre à jour](/rest/api/authorization/roledefinitions/createorupdate). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/write` sur toutes les `assignableScopes`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération. 

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

Pour mettre à jour un rôle personnalisé, utilisez l’API REST [Définitions de rôles – Créer ou mettre à jour](/rest/api/authorization/roledefinitions/createorupdate). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/write` sur toutes les `assignableScopes`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération. 

1. Utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list) ou [Définitions de rôles – Obtenir](/rest/api/authorization/roledefinitions/get) pour obtenir des informations sur le rôle personnalisé. Pour plus d’informations, voir la section précédente [Lister les rôles](custom-roles-rest.md#list-roles).

1. Commencez par la demande suivante :

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

Pour supprimer un rôle personnalisé, utilisez l’API REST [Définitions de rôles – Supprimer](/rest/api/authorization/roledefinitions/delete). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleDefinitions/delete` sur toutes les `assignableScopes`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération. 

1. Utilisez l’API REST [Définitions de rôles – Lister](/rest/api/authorization/roledefinitions/list) ou [Définitions de rôles – Obtenir](/rest/api/authorization/roledefinitions/get) pour obtenir l’identificateur GUID du rôle personnalisé. Pour plus d’informations, voir la section précédente [Lister les rôles](custom-roles-rest.md#list-roles).

1. Commencez par la demande suivante :

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