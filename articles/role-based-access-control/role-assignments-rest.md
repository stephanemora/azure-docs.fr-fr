---
title: Gérer l’accès aux ressources Azure à l’aide de RBAC et l’API REST - Azure | Microsoft Docs
description: Apprenez à gérer la façon dont les utilisateurs, les groupes et les applications accèdent aux ressources Azure à l'aide du contrôle d'accès en fonction du rôle (RBAC) et de l'API REST. Apprenez notamment à lister, à accorder et à supprimer des accès.
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
ms.openlocfilehash: e9c307b2b2e720881acb983a2a1b00ac8e03dac4
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357075"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Gérer l'accès aux ressources Azure à l'aide du contrôle d'accès en fonction du rôle (RBAC) et de l'API REST

Le [contrôle d'accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l'accès aux ressources Azure. Cet article décrit comment gérer l’accès des utilisateurs, groupes et applications à l’aide du contrôle d’accès en fonction du rôle et de l’API REST.

## <a name="list-access"></a>Répertorier les accès

Dans le contrôle d’accès en fonction du rôle, vous répertoriez les attributions de rôles pour énumérer les accès. Pour répertorier les attributions de rôles, utilisez l’une des API REST de la [liste d’attributions de rôles](/rest/api/authorization/roleassignments/list). Pour affiner vos résultats, vous spécifiez une étendue et un filtre facultatif.

1. Commencez par la requête suivante :

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue dont vous souhaitez lister les attributions de rôle.

    | `Scope` | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |
    
       
     > [!NOTE]
     > Dans l’exemple ci-dessus que Microsoft.Web est que le fournisseur de ressources est utilisé, qui fait référence à l’instance de service d’application. De même, vous pouvez utiliser n’importe quel autre fournisseur de ressources et générer l’URI de portée. Pour comprendre plus d’informations, consultez [des fournisseurs de ressources Azure et types](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) et prise en charge [opérations du fournisseur de ressources Azure RM](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations).  
     
1. Remplacez *{filter}* par la condition que vous voulez appliquer pour filtrer la liste des attributions de rôle.

    | Filtrer | Description |
    | --- | --- |
    | `$filter=atScope()` | Répertorier les affectations de rôle pour l’étendue spécifiée seulement, sans y inclure les affectations de rôles à des étendues secondaires. |
    | `$filter=principalId%20eq%20'{objectId}'` | Répertorier les affectations de rôle pour un utilisateur, un groupe ou un principal de service spécifié. |
    | `$filter=assignedTo('{objectId}')` | Répertorier les affectations de rôle pour un utilisateur spécifié, y compris celles héritées de groupes. |

## <a name="grant-access"></a>Accorder l'accès

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès. Pour créer une attribution de rôle, utilisez l’API REST [Créer des attributions de rôles](/rest/api/authorization/roleassignments/create) et spécifiez le principal de sécurité, la définition de rôle et l’étendue. Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/write`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération.

1. Utilisez l’API REST [Liste de définitions de rôles](/rest/api/authorization/roledefinitions/list) ou consultez [Rôles intégrés](built-in-roles.md) pour obtenir l’identificateur de la définition de rôle que vous souhaitez assigner.

1. Utilisez un outil GUID pour générer un identificateur unique qui servira d’identificateur d’attribution de rôle. Il est au format : `00000000-0000-0000-0000-000000000000`

1. Commencez par la requête et le corps suivants :

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. Dans l’URI, remplacez *{scope}* par l’étendue de l’attribution de rôle.

    | `Scope` | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{roleAssignmentName}* par l’identificateur GUID de l’attribution de rôle.

1. Au sein du corps de la requête *{subscriptionId}* , par votre identificateur d’abonnement.

1. Remplacez *{roleDefinitionId}* par l’identificateur de définition de rôle.

1. Remplacez *{principalId}* par l’identificateur d’objet de l’utilisateur, du groupe ou du principal du service auquel le rôle sera attribué.

## <a name="remove-access"></a>Suppression d'accès

Dans le RBAC, vous supprimez une attribution de rôle pour supprimer un accès. Pour supprimer une attribution de rôle, utilisez l’API REST [Supprimer une attribution de rôle](/rest/api/authorization/roleassignments/delete). Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/delete`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération.

1. Obtenir l’identificateur de l’attribution de rôle (GUID). Cet identificateur est renvoyé lorsque vous créez l’attribution de rôle, ou vous pouvez l’obtenir en répertoriant les attributions de rôles.

1. Commencez par la requête suivante :

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue de suppression de l’attribution de rôle.

    | `Scope` | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Groupe de ressources |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ressource |

1. Remplacez *{roleAssignmentName}* par l’identificateur GUID de l’attribution de rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Référence de l'API REST Azure](/rest/api/azure/)
- [Créer des rôles personnalisés pour les ressources Azure à l'aide de l'API REST](custom-roles-rest.md)
