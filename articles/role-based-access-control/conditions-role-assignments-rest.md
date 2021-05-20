---
title: Ajouter ou modifier des conditions d’attribution de rôle Azure à l’aide de l’API REST (préversion) – Azure RBAC
description: Découvrez comment ajouter, modifier, lister ou supprimer des conditions de contrôle d’accès en fonction des attributs (ABAC) dans les attributions de rôles Azure à l’aide de l’API REST et du contrôle d’accès en fonction du rôle Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 4a929fd99f10e66ea07dacc0302dd6d0bba83a96
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656557"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-rest-api-preview"></a>Ajouter ou modifier des conditions d’attribution de rôle Azure à l’aide de l’API REST (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une [condition d’attribution de rôle Azure](conditions-overview.md) est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Cet article explique comment ajouter, modifier, lister ou supprimer des conditions pour vos attributions de rôles à l’aide de l’API REST.

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](conditions-prerequisites.md).

## <a name="add-a-condition"></a>Ajouter une condition

Pour ajouter une condition d’attribution de rôle, utilisez l’API REST [Attributions de rôles – Créer](/rest/api/authorization/roleassignments/create). Définissez `api-version` sur `2020-03-01-preview` ou une version ultérieure. Si vous souhaitez utiliser la propriété `description` pour les attributions de rôles, utilisez `2020-04-01-preview` ou une version ultérieure.  [Attributions de rôles – Créer](/rest/api/authorization/roleassignments/create) comprend les paramètres suivants associés aux conditions.

| Paramètre | Type | Description |
| --- | --- | --- |
| `condition` | String | Condition sous laquelle l’utilisateur peut se voir accorder une autorisation. |
| `conditionVersion` | String | Version de la syntaxe de condition. Si `condition` est spécifié sans `conditionVersion`, la version est définie sur la valeur par défaut 2.0. |

Utilisez la requête et le corps suivants :

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}",
        "condition": "{condition}",
        "conditionVersion": "2.0",
        "description": "{description}"
    }
}
```

L’exemple suivant montre comment attribuer le rôle [Lecteur des données Blob du stockage](built-in-roles.md#storage-blob-data-reader) avec une condition. La condition vérifie si le nom du conteneur correspond à « blobs-example-container ».

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container"
    }
}
```

Voici un exemple de sortie :

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "principalType": "User",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "createdOn": "2021-04-20T06:20:44.0205560Z",
        "updatedOn": "2021-04-20T06:20:44.2955371Z",
        "createdBy": null,
        "updatedBy": "{updatedById}",
        "delegatedManagedIdentityResourceId": null,
        "description": "Read access if container name equals blobs-example-container"
    },
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId}"
}
```

## <a name="edit-a-condition"></a>Modifier une condition

Pour modifier une condition d’attribution de rôle existante, utilisez la même API REST [Attributions de rôles – Créer](/rest/api/authorization/roleassignments/create) que celle utilisée pour ajouter la condition d’attribution de rôle. Ce qui suit montre un exemple de JSON où `condition` et `description` sont mis à jour. Seules les propriétés `condition`, `conditionVersion` et `description` peuvent être modifiées. Vous devez spécifier les autres propriétés pour qu’elles correspondent à l’attribution de rôle existante.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container or blobs-example-container2"
    }
}
```


## <a name="list-a-condition"></a>Lister une condition

Pour lister une condition d’attribution de rôle, utilisez l’API [Attributions de rôles – Lister](/rest/api/authorization/roleassignments/list). Définissez `api-version` sur `2020-03-01-preview` ou une version ultérieure. Si vous souhaitez utiliser la propriété `description` pour les attributions de rôles, utilisez `2020-04-01-preview` ou une version ultérieure. Pour plus d’informations, consultez [Lister les attributions de rôles Azure à l’aide de l’API REST](role-assignments-list-rest.md).

## <a name="delete-a-condition"></a>Supprimer une condition

Pour supprimer une condition d’attribution de rôle, modifiez-la en définissant la condition et la version de condition sur une chaîne vide ou sur Null.

Si vous voulez supprimer à la fois l’attribution de rôle et la condition, vous pouvez également utiliser l’API [Attributions de rôles – Supprimer](/rest/api/authorization/roleassignments/delete). Pour plus d’informations, consultez [Supprimer des attributions de rôles Azure](role-assignments-remove.md).

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux blobs à l’aide du portail Azure (préversion)](../storage/common/storage-auth-abac-portal.md)
- [Résoudre les problèmes liés aux conditions d’attribution de rôle Azure (préversion)](conditions-troubleshoot.md)
