---
title: Ajouter ou supprimer des attributions de rôle avec le contrôle d’accès en fonction du rôle et l’API REST
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure et de l’API REST.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063013"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et de l’API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide de l’API REST.

## <a name="prerequisites"></a>Conditions préalables requises

Pour ajouter ou supprimer des attributions de rôles, vous devez disposer :

- d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Dans le contrôle RBAC, vous ajoutez une attribution de rôle pour accorder l’accès. Pour ajouter une attribution de rôle, utilisez l’API REST [Créer des attributions de rôles](/rest/api/authorization/roleassignments/create) et spécifiez le principal de sécurité, la définition de rôle et l’étendue. Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/write`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération.

1. Utilisez l’API REST [Liste de définitions de rôles](/rest/api/authorization/roledefinitions/list) ou consultez [Rôles intégrés](built-in-roles.md) pour obtenir l’identificateur de la définition de rôle que vous souhaitez assigner.

1. Utilisez un outil GUID pour générer un identificateur unique qui servira d’identificateur d’attribution de rôle. Cet identificateur est au format : `00000000-0000-0000-0000-000000000000`.

1. Commencez par la requête et le corps suivants :

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue de l’attribution de rôle.

    > [!div class="mx-tableFixed"]
    > | Étendue | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Groupe d’administration |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ressource |

    Dans l’exemple précédent, microsoft.web est un fournisseur de ressources qui fait référence à une instance App Service. De la même façon, vous pouvez utiliser tout autre fournisseur de ressources et spécifier l’étendue. Pour plus d’informations, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md) et [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md) prises en charge.  

1. Remplacez *{roleAssignmentName}* par l’identificateur GUID de l’attribution de rôle.

1. Dans le corps de la requête, remplacez *{scope}* par l’étendue de l’attribution de rôle.

    > [!div class="mx-tableFixed"]
    > | Étendue | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Groupe d’administration |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ressource |

1. Remplacez *{roleDefinitionId}* par l’identificateur de définition de rôle.

1. Remplacez *{principalId}* par l’identificateur d’objet de l’utilisateur, du groupe ou du principal du service auquel le rôle sera attribué.

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Dans le RBAC, vous supprimez une attribution de rôle pour supprimer un accès. Pour supprimer une attribution de rôle, utilisez l’API REST [Supprimer une attribution de rôle](/rest/api/authorization/roleassignments/delete). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/delete`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération.

1. Obtenir l’identificateur de l’attribution de rôle (GUID). Cet identificateur est renvoyé lorsque vous créez l’attribution de rôle, ou vous pouvez l’obtenir en répertoriant les attributions de rôles.

1. Commencez par la requête suivante :

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue de suppression de l’attribution de rôle.

    > [!div class="mx-tableFixed"]
    > | Étendue | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Groupe d’administration |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ressource |

1. Remplacez *{roleAssignmentName}* par l’identificateur GUID de l’attribution de rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Lister les attributions de rôles à l’aide du RBAC Azure et de l’API REST](role-assignments-list-rest.md)
- [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Référence de l'API REST Azure](/rest/api/azure/)
- [Créer des rôles personnalisés pour les ressources Azure à l'aide de l'API REST](custom-roles-rest.md)
