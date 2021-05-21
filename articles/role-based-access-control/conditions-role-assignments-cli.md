---
title: Ajouter ou modifier des conditions d’attribution de rôle Azure avec Azure CLI (préversion) - RBAC Azure
description: Découvrez comment ajouter, modifier, lister ou supprimer des conditions de contrôle d’accès en fonction des attributs (ABAC) dans les attributions de rôle Azure à l’aide d’Azure CLI et du contrôle d’accès en fonction du rôle Azure (RBAC Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: c7baafbd34e9af488fbbee6237625aa469dff12c
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656629"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-cli-preview"></a>Ajouter ou modifier des conditions d’attribution de rôle Azure avec Azure CLI (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une [condition d’attribution de rôle Azure](conditions-overview.md) est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Cet article explique comment ajouter, modifier, lister ou supprimer des conditions pour vos attributions de rôles à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](conditions-prerequisites.md).

## <a name="add-a-condition"></a>Ajouter une condition

Pour ajouter une condition d’attribution de rôle, utilisez [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). La commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) comprend les paramètres suivants liés aux conditions.

| Paramètre | Type | Description |
| --- | --- | --- |
| `condition` | String | Condition sous laquelle l’utilisateur peut se voir accorder une autorisation. |
| `condition-version` | String | Version de la syntaxe de condition. Si `--condition` est spécifié sans `--condition-version`, la version est définie sur la valeur par défaut 2.0. |

L’exemple suivant montre comment attribuer le rôle [Lecteur des données Blob du stockage](built-in-roles.md#storage-blob-data-reader) avec une condition. La condition vérifie si le nom du conteneur correspond à « blobs-example-container ».

```azurecli
az role assignment create --role "Storage Blob Data Reader" --assignee "user1@contoso.com" --resource-group {resourceGroup} \
--description "Read access if container name equals blobs-example-container" \
--condition "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))" \
--condition-version "2.0"
```

Voici un exemple de sortie :

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="edit-a-condition"></a>Modifier une condition

Pour modifier une condition d’attribution de rôle existante, utilisez [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) et un fichier JSON comme entrée. L’exemple suivant montre un exemple de fichier JSON dans lequel la condition et la description sont mises à jour. Seules les propriétés `condition`, `conditionVersion` et `description` peuvent être modifiées. Vous devez spécifier toutes les propriétés pour mettre à jour la condition d’attribution de rôle.

```json
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Utilisez [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) pour mettre à jour la condition de l’attribution de rôle.

```azurecli
az role assignment update --role-assignment "./path/roleassignment.json"
```

Voici un exemple de sortie :

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="list-a-condition"></a>Lister une condition

Pour lister une condition d’attribution de rôle, utilisez [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Pour plus d’informations, consultez [Lister les attributions de rôles Azure à l’aide d’Azure CLI](role-assignments-list-cli.md).

## <a name="delete-a-condition"></a>Supprimer une condition

Pour supprimer une condition d’attribution de rôle, modifiez-la en définissant les deux propriétés `condition` et `condition-version` sur une chaîne vide (`""`) ou `null`.

Si vous voulez supprimer à la fois l’attribution de rôle et la condition, vous pouvez également utiliser la commande [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete). Pour plus d’informations, consultez [Supprimer des attributions de rôles Azure](role-assignments-remove.md).

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant Azure CLI (préversion)](../storage/common/storage-auth-abac-cli.md)
- [Résoudre les problèmes liés aux de conditions d’attribution de rôle Azure (préversion)](conditions-troubleshoot.md)
