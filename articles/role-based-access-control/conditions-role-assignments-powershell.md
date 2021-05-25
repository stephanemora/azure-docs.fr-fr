---
title: Ajouter ou modifier des conditions d’attribution de rôle Azure à l’aide d’Azure PowerShell (préversion) - RBAC Azure
description: Découvrez comment ajouter, modifier, lister ou supprimer des conditions de contrôle d’accès en fonction des attributs (ABAC) dans les attributions de rôle Azure à l’aide d’Azure PowerShell et du contrôle d’accès en fonction du rôle Azure (RBAC Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 0ecc87a59d6db01c5c5dc4093bb42e4ee9c190d9
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656575"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-powershell-preview"></a>Ajouter ou modifier des conditions d’attribution de rôle Azure à l’aide d’Azure PowerShell (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une [condition d’attribution de rôle Azure](conditions-overview.md) est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Cet article explique comment ajouter, modifier, lister ou supprimer des conditions pour vos attributions de rôles à l’aide d’Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](conditions-prerequisites.md).

## <a name="add-a-condition"></a>Ajouter une condition

Pour ajouter une condition d’attribution de rôle, utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). La commande [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) comprend les paramètres suivants liés aux conditions.

| Paramètre | Type | Description |
| --- | --- | --- |
| `Condition` | String | Condition sous laquelle l’utilisateur peut se voir accorder une autorisation. |
| `ConditionVersion` | String | Version de la syntaxe de condition. Doit être défini sur 2.0. Si `Condition` est spécifié, `ConditionVersion` doit également être spécifié. |

L’exemple suivant montre comment initialiser les variables pour attribuer le rôle [Lecteur des données Blob du stockage](built-in-roles.md#storage-blob-data-reader) avec une condition. La condition vérifie si le nom du conteneur correspond à « blobs-example-container ».

```azurepowershell
$subscriptionId = "<subscriptionId>"
$resourceGroup = "<resourceGroup>"
$roleDefinitionName = "Storage Blob Data Reader"
$roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
$userObjectId = "<userObjectId>"
$scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
$description = "Read access if container name equals blobs-example-container"
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$conditionVersion = "2.0"
```

Utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) pour attribuer le rôle avec une condition.

```azurepowershell
New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
```

Voici un exemple de sortie :

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))
```

Dans PowerShell, si votre condition comprend un signe dollar ($), vous devez le faire précéder d’un guillemet inversé (\`). Par exemple, la condition suivante utilise des signes dollar pour délimiter le nom de clé d’étiquette. Pour plus d’informations sur les règles de liées aux guillemets dans PowerShell, consultez [À propos des règles liées aux guillemets](/powershell/module/microsoft.powershell.core/about/about_quoting_rules).

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="edit-a-condition"></a>Modifier une condition

Pour modifier une condition d’attribution de rôle existante, utilisez [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment). Seules les propriétés `Condition`, `ConditionVersion` et `Description` peuvent être modifiées. Si vous spécifiez le paramètre `-PassThru`, [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) retourne l’attribution de rôle mise à jour, ce qui permet la visualisation ou le stockage dans une variable pour une utilisation ultérieure.

Il existe deux façons de modifier une condition. Vous pouvez utiliser l’objet `PSRoleAssignment` ou un fichier JSON.

### <a name="edit-a-condition-using-the-psroleassignment-object"></a>Modifier une condition à l’aide de l’objet PSRoleAssignment

1. Utilisez [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) pour obtenir l’attribution de rôle existante avec une condition comme objet `PSRoleAssignment`.

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. Modifiez la condition.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))"
    ```

1. Initialisez la condition et la description.

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access if container name equals blobs-example-container or blobs-example-container2"
    ```

1. Utilisez [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) pour mettre à jour la condition de l’attribution de rôle.

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    Voici un exemple de sortie :

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : User1
    SignInName         : user1@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access if container name equals blobs-example-container or blobs-example-container2
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
    ```

### <a name="edit-a-condition-using-a-json-file"></a>Modifier une condition à l’aide d’un fichier JSON

Pour modifier une condition, vous pouvez également fournir un fichier JSON comme entrée. Le code suivant montre un exemple de fichier JSON où `Condition` et `Description` sont mis à jour. Vous devez spécifier toutes les propriétés dans le fichier JSON pour mettre à jour une condition.

```json
{
    "RoleDefinitionId": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "ObjectId": "<userObjectId>",
    "ObjectType": "User",
    "Scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>",
    "Condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "ConditionVersion": "2.0",
    "CanDelegate": false,
    "Description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "RoleAssignmentId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>"
}
```

Utilisez [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) pour mettre à jour la condition de l’attribution de rôle.

```azurepowershell
Set-AzRoleAssignment -InputFile "C:\path\roleassignment.json" -PassThru
```

Voici un exemple de sortie :

```azurepowershell
RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
DisplayName        : User1
SignInName         : user1@contoso.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : <userObjectId>
ObjectType         : User
CanDelegate        : False
Description        : Read access if container name equals blobs-example-container or blobs-example-container2
ConditionVersion   : 2.0
Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))
```

## <a name="list-a-condition"></a>Lister une condition

Pour lister une condition d’attribution de rôle, utilisez [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment). Pour plus d’informations, consultez [Lister les attributions de rôles Azure avec Azure PowerShell](role-assignments-list-powershell.md).

## <a name="delete-a-condition"></a>Supprimer une condition

Pour supprimer une condition d’attribution de rôle, modifiez-la en définissant les deux propriétés `Condition` et `ConditionVersion` sur une chaîne vide (`""`) ou `$null`.

Si vous voulez supprimer à la fois l’attribution de rôle et la condition, vous pouvez également utiliser la commande [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment). Pour plus d’informations, consultez [Supprimer des attributions de rôles Azure](role-assignments-remove.md).

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant Azure PowerShell (préversion)](../storage/common/storage-auth-abac-powershell.md)
- [Résoudre les problèmes liés aux de conditions d’attribution de rôle Azure (préversion)](conditions-troubleshoot.md)
