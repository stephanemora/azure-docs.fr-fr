---
title: Attribuer des rôles Azure à l’aide de l’API REST - Azure RBAC
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide de l’API REST et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363714"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Attribuer des rôles Azure à l’aide de l’API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide de l’API REST.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Affecter un rôle Azure

Pour affecter un rôle, utilisez l’API REST [Créer des attributions de rôles](/rest/api/authorization/roleassignments/create) et spécifiez le principal de sécurité, la définition de rôle et l’étendue. Pour appeler cette API, vous devez avoir accès à l’opération `Microsoft.Authorization/roleAssignments/write`. Parmi les rôles intégrés, seuls ceux du [propriétaire](built-in-roles.md#owner) et de [l’administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) se voient accorder l’accès à cette opération.

1. Utilisez l’API REST [Liste de définitions de rôles](/rest/api/authorization/roledefinitions/list) ou consultez [Rôles intégrés](built-in-roles.md) pour obtenir l’identificateur de la définition de rôle que vous souhaitez assigner.

1. Utilisez un outil GUID pour générer un identificateur unique qui servira d’identificateur d’attribution de rôle. Cet identificateur est au format : `00000000-0000-0000-0000-000000000000`.

1. Commencez par la requête et le corps suivants :

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
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
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ressource |

    Dans l’exemple précédent, microsoft.web est un fournisseur de ressources qui fait référence à une instance App Service. De la même façon, vous pouvez utiliser tout autre fournisseur de ressources et spécifier l’étendue. Pour plus d’informations, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md) et [Opérations du fournisseur de ressources Azure](resource-provider-operations.md) prises en charge.  

1. Remplacez *{roleAssignmentId}* par l’identificateur GUID de l’attribution de rôle.

1. Dans le corps de la requête, remplacez *{scope}* par l’étendue de l’attribution de rôle.

    > [!div class="mx-tableFixed"]
    > | Étendue | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Groupe d’administration |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ressource |

1. Remplacez *{roleDefinitionId}* par l’identificateur de définition de rôle.

1. Remplacez *{principalId}* par l’identificateur d’objet de l’utilisateur, du groupe ou du principal du service auquel le rôle sera attribué.

La requête et le corps suivants attribuent le rôle [Lecteur de sauvegarde](built-in-roles.md#backup-reader) à un utilisateur dans l’étendue de l’abonnement :

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Voici un exemple de sortie :

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>Nouveau principal de service

Dans certains cas, si vous créez un principal de service et que vous tentez immédiatement de lui attribuer un rôle, cette attribution peut échouer. Par exemple, si vous créez une identité managée et que vous tentez d’attribuer un rôle à ce principal de service, l’attribution de rôle peut échouer. Cet échec est souvent lié au délai de réplication. Le principal du service est créé dans une région. Toutefois, l’attribution de rôle peut s’effectuer dans une autre région, qui n’a pas encore répliqué le principal de service.

Pour résoudre ce scénario, utilisez l’API REST [Role Assignments - Create](/rest/api/authorization/roleassignments/create) et affectez la valeur `ServicePrincipal` à la propriété `principalType`. Vous devez également définir `apiVersion` sur `2018-09-01-preview` ou une version ultérieure.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Répertorier les attributions de rôle Azure à l’aide de l’API REST](role-assignments-list-rest.md)
- [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Référence de l'API REST Azure](/rest/api/azure/)
- [Créer ou mettre à jour des rôles personnalisés Azure à l’aide de l’API REST](custom-roles-rest.md)
