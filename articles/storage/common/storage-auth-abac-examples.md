---
title: Exemples de conditions d’attribution de rôle Azure (préversion) – Azure RBAC
titleSuffix: Azure Storage
description: Exemple de conditions d’attribution de rôle Azure pour le contrôle d’accès en fonction des attributs Azure (Azure ABAC).
services: storage
author: rolyon
ms.service: storage
ms.topic: conceptual
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 062be43bda26169d27740777bdb60d335c9923e6
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489679"
---
# <a name="example-azure-role-assignment-conditions-preview"></a>Exemples de conditions d’attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article répertorie quelques exemples de conditions d’attribution de rôle.

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](../../role-based-access-control/conditions-prerequisites.md).

## <a name="example-1-read-access-to-blobs-with-a-tag"></a>Exemple 1 : Accès en lecture aux blobs avec une balise

Cette condition permet aux utilisateurs de lire des blobs dont la clé de balise d’index est Project et la valeur de balise est Cascade. Les tentatives d’accès aux blobs sans cette balise clé-valeur ne sont pas autorisées.

> [!TIP]
> Les blobs prennent également en charge la possibilité de stocker des métadonnées clé-valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux balises d’index de blob, vous devez utiliser des balises d’index de blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données Blob Azure avec des balises d’index de blob (préversion)](../blobs/storage-manage-find-blobs.md).

![Diagramme de la condition de l’exemple 1 montrant un accès en lecture à un blob avec une balise.](./media/storage-auth-abac-examples/example-1.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
    )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Lire du contenu à partir d’un blob avec des conditions de balise |
| Source de l’attribut | Ressource |
| Attribut | Balises d’index de blob [Valeurs dans la clé] |
| Clé : | {keyName} |
| Opérateur | StringEquals |
| Valeur | {keyValue} |

![Capture d’écran de l’éditeur de conditions de l’exemple 1 dans Portail Azure.](./media/storage-auth-abac-examples/example-1-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
Get-AzStorageBlob -Container <containerName> -Blob <blobName> -Context $bearerCtx 
```

## <a name="example-2-new-blobs-must-include-a-tag"></a>Exemple 2 : Les nouveaux blobs doivent inclure une balise

Cette condition implique que tout nouveau blob doit inclure la clé de balise d’index de blob Project et la valeur de balise Cascade.

> [!TIP]
> Les blobs prennent également en charge la possibilité de stocker des métadonnées clé-valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux balises d’index de blob, vous devez utiliser des balises d’index de blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données Blob Azure avec des balises d’index de blob (préversion)](../blobs/storage-manage-find-blobs.md).

Deux autorisations vous permettent de créer de nouveaux blobs, vous devez donc cibler les deux. Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent l’une des autorisations suivantes.

- /blobs/write (créer ou mettre à jour) 
- /blobs/add/action (créer)

![Diagramme de la condition de l’exemple 2 montrant les nouveaux blobs qui doivent inclure une balise.](./media/storage-auth-abac-examples/example-2.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'
 )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Écrire dans un blob avec des balises d’index de blob<br/>Écrire du contenu dans un blob avec des balises d’index de blob |
| Source de l’attribut | Requête |
| Attribut | Balises d’index de blob [Valeurs dans la clé] |
| Clé : | {keyName} |
| Opérateur | StringEquals |
| Valeur | {keyValue} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 2 dans Portail Azure.](./media/storage-auth-abac-examples/example-2-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Project'='Baker'}
$grantedTag = @{'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example2 -Blob "Example2.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-3-existing-blobs-must-have-tag-keys"></a>Exemple 3 : Les blobs existants doivent avoir des clés de balise

Cette condition exige que tous les blobs existants soient marqués avec au moins l’une des clés de balise d’index de blob autorisées, Project ou Program. Cette condition est utile pour ajouter la gouvernance aux blobs existants.

> [!TIP]
> Les blobs prennent également en charge la possibilité de stocker des métadonnées clé-valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux balises d’index de blob, vous devez utiliser des balises d’index de blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données Blob Azure avec des balises d’index de blob (préversion)](../blobs/storage-manage-find-blobs.md).

Deux autorisations vous permettent de mettre à jour les balises sur les blobs existants, vous devez donc cibler les deux. Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent l’une des autorisations suivantes.

- /blobs/write (mettre à jour ou créer, impossible d’exclure créer)
- /blobs/tags/write

![Diagramme de la condition de l’exemple 3 montrant des blobs existants qui doivent avoir des clés de balise.](./media/storage-auth-abac-examples/example-3.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}
 )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Écrire dans un blob avec des balises d’index de blob<br/>Écrire des balises d’index de blob |
| Source de l’attribut | Requête |
| Attribut | Balises d’index de blob [Clés] |
| Opérateur | ForAllOfAnyValues:StringEquals |
| Valeur | {keyName1}<br/>{keyName2} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 3 dans Portail Azure.](./media/storage-auth-abac-examples/example-3-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$localSrcFile = # path to an example file, can be an empty txt
$ungrantedTag = @{'Mode'='Baker'}
$grantedTag = @{'Program'='Alpine';'Project'='Cascade'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
$content = Set-AzStorageBlobContent -File $localSrcFile -Container example3 -Blob "Example3.txt" -Tag $grantedTag -Context $bearerCtx
```

## <a name="example-4-existing-blobs-must-have-a-tag-key-and-values"></a>Exemple 4 : Les blobs existants doivent avoir une clé et des valeurs de balise

Cette condition exige que tous les blobs existants aient la clé de balise d’index de blob Project et les valeurs de balise Cascade, Baker ou Skagit. Cette condition est utile pour ajouter la gouvernance aux blobs existants.

> [!TIP]
> Les blobs prennent également en charge la possibilité de stocker des métadonnées clé-valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux balises d’index de blob, vous devez utiliser des balises d’index de blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données Blob Azure avec des balises d’index de blob (préversion)](../blobs/storage-manage-find-blobs.md).

Deux autorisations vous permettent de mettre à jour les balises sur les blobs existants, vous devez donc cibler les deux. Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent l’une des autorisations suivantes.

- /blobs/write (mettre à jour ou créer, impossible d’exclure créer)
- /blobs/tags/write

![Diagramme de la condition de l’exemple 4 montrant des blobs existants qui doivent avoir une clé et des valeurs de balise.](./media/storage-auth-abac-examples/example-4.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})
 )
 OR 
 (
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAnyOfAnyValues:StringEquals {'Project'}
  AND
  @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}
 )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Écrire dans un blob avec des balises d’index de blob<br/>Écrire des balises d’index de blob |
| Source de l’attribut | Requête |
| Attribut | Balises d’index de blob [Clés] |
| Opérateur | ForAnyOfAnyValues:StringEquals |
| Valeur | {keyName} |
| Opérateur | And |
| **Expression 2** |  |
| Source de l’attribut | Requête |
| Attribut | Balises d’index de blob [Valeurs dans la clé] |
| Clé : | {keyName} |
| Opérateur | ForAllOfAnyValues:StringEquals |
| Valeur | {keyValue1}<br/>{keyValue2}<br/>{keyValue3} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 4 dans Portail Azure.](./media/storage-auth-abac-examples/example-4-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Write.WithTagHeaders'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})) OR (@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&`$keys`$&] ForAnyOfAnyValues:StringEquals {'Project'} AND @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] ForAllOfAnyValues:StringEquals {'Cascade', 'Baker', 'Skagit'}))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$localSrcFile = <pathToLocalFile>
$ungrantedTag = @{'Project'='Alpine'}
$grantedTag1 = @{'Project'='Cascade'}
$grantedTag2 = @{'Project'='Baker'}
$grantedTag3 = @{'Project'='Skagit'}
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# try ungranted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $ungrantedTag -Context $bearerCtx
# try granted tags
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag1 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag2 -Context $bearerCtx
Set-AzStorageBlobTag -Container example4 -Blob "Example4.txt" -Tag $grantedTag3 -Context $bearerCtx
```

## <a name="example-5-read-write-or-delete-blobs-in-named-containers"></a>Exemple 5 : Lire, écrire ou supprimer des blobs dans des conteneurs nommés

Cette condition permet aux utilisateurs de lire, d’écrire ou de supprimer des blobs dans des conteneurs de stockage nommés blobs-example-container. Cette condition est utile pour partager des conteneurs de stockage spécifiques avec d’autres utilisateurs dans un abonnement.

Quatre autorisations vous permettent de lire, d’écrire et de supprimer des blobs existants, vous devez donc cibler toutes les autorisations. Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent l’une des autorisations suivantes.

- /blobs/delete
- /blobs/read
- /blobs/write (mettre à jour ou créer)
- /blobs/add/action (créer)

Les sous-opérations ne sont pas utilisées dans cette condition, car la sous-opération est nécessaire uniquement lorsque des conditions sont créées en fonction de balises.

![Diagramme de la condition de l’exemple 5 montrant la lecture, l’écriture ou la suppression de blobs dans les conteneurs nommés.](./media/storage-auth-abac-examples/example-5.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
 )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Supprimer un objet blob<br/>Lire un blob<br/>Écrire dans un blob<br/>Créer un blob ou un instantané, ou ajouter des données |
| Source de l’attribut | Ressource |
| Attribut | Nom du conteneur |
| Opérateur | StringEquals |
| Valeur | {containerName} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 5 dans Portail Azure.](./media/storage-auth-abac-examples/example-5-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$localSrcFile = <pathToLocalFile>
$grantedContainer = "blobs-example-container"
$ungrantedContainer = "ungranted"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Ungranted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $ungrantedContainer -Blob "Example5.txt" -Context $bearerCtx
# Granted Container actions
$content = Set-AzStorageBlobContent -File $localSrcFile -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
$content = Remove-AzStorageBlob -Container $grantedContainer -Blob "Example5.txt" -Context $bearerCtx
```

## <a name="example-6-read-access-to-blobs-in-named-containers-with-a-path"></a>Exemple 6 : Accès en lecture aux blobs dans les conteneurs nommés avec un chemin d’accès

Cette condition autorise l’accès en lecture aux conteneurs de stockage nommés blobs-example-container avec le chemin d’accès au blob readonly/*. Cette condition est utile pour partager des parties spécifiques de conteneurs de stockage pour l’accès en lecture avec d’autres utilisateurs de l’abonnement.

Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent l’autorisation suivante.

- /blobs/read

![Diagramme de la condition de l’exemple 6 montrant l’accès en lecture aux blobs dans des conteneurs nommés avec un chemin d’accès.](./media/storage-auth-abac-examples/example-6.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'
        AND
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'
    )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Lire un blob |
| Source de l’attribut | Ressource |
| Attribut | Nom du conteneur |
| Opérateur | StringEquals |
| Valeur | {containerName} |
| **Expression 2** |  |
| Opérateur | And |
| Source de l’attribut | Ressource |
| Attribut | Chemin d’accès d’objet blob |
| Opérateur | StringLike |
| Valeur | {pathString} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 6 dans Portail Azure.](./media/storage-auth-abac-examples/example-6-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$grantedContainer = "blobs-example-container"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "Ungranted.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "readonly/Example6.txt" -Context $bearerCtx
```

## <a name="example-7-write-access-to-blobs-in-named-containers-with-a-path"></a>Exemple 7 : Accès en écriture aux blobs dans les conteneurs nommés avec un chemin d’accès

Cette condition permet à un partenaire (un utilisateur invité Azure AD) de déposer des fichiers dans des conteneurs de stockage nommés Contosocorp avec le chemin d’accès uploads/contoso/*. Cette condition est utile pour permettre à d’autres utilisateurs de déposer des données dans des conteneurs de stockage.

Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent les autorisations suivantes.

- /blobs/write (créer ou mettre à jour)
- /blobs/add/action (créer)

![Diagramme de la condition de l’exemple 7 montrant l’accès en écriture aux blobs dans des conteneurs nommés avec un chemin d’accès.](./media/storage-auth-abac-examples/example-7.png)

```
(
 (
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})
  AND
  !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})
 )
 OR 
 (
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp'
  AND
  @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'
 )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Écrire dans un blob<br/>Créer un blob ou un instantané, ou ajouter des données |
| Source de l’attribut | Ressource |
| Attribut | Nom du conteneur |
| Opérateur | StringEquals |
| Valeur | {containerName} |
| **Expression 2** |  |
| Opérateur | And |
| Source de l’attribut | Ressource |
| Attribut | Chemin d’accès d’objet blob |
| Opérateur | StringLike |
| Valeur | {pathString} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 7 dans Portail Azure.](./media/storage-auth-abac-examples/example-7-condition-1-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'}) AND !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'contosocorp' AND @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'uploads/contoso/*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$grantedContainer = "contosocorp"
$localSrcFile = <pathToLocalFile>
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to set ungranted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "Example7.txt" -Context $bearerCtx -File $localSrcFile
# Try to set granted blob
$content = Set-AzStorageBlobContent -Container $grantedContainer -Blob "uploads/contoso/Example7.txt" -Context $bearerCtx -File $localSrcFile
```

## <a name="example-8-read-access-to-blobs-with-a-tag-and-a-path"></a>Exemple 8 : Accès en lecture aux blobs avec une balise et un chemin d’accès

Cette condition permet à un utilisateur de lire des blobs dont la clé de balise d’index de blob est Program, dont la valeur de balise est Alpine et dont le chemin d’accès au blob est logs *. Le chemin d’accès au blob logs* comprend également le nom du blob.

> [!TIP]
> Les blobs prennent également en charge la possibilité de stocker des métadonnées clé-valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux balises d’index de blob, vous devez utiliser des balises d’index de blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données Blob Azure avec des balises d’index de blob (préversion)](../blobs/storage-manage-find-blobs.md).

Vous devez ajouter cette condition à toutes les attributions de rôles qui incluent l’autorisation suivante.

- /blobs/read

![Diagramme de la condition de l’exemple 8 montrant un accès en lecture aux blobs avec une balise et un chemin d’accès.](./media/storage-auth-abac-examples/example-8.png)

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<$key_case_sensitive$>] StringEquals 'Alpine'
    )
)
AND
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'
    )
)
```

#### <a name="azure-portal"></a>Portail Azure

Voici les paramètres pour ajouter cette condition en utilisant le portail Azure.

| Condition no 1 | Paramètre |
| --- | --- |
| Actions | Lire du contenu à partir d’un blob avec des conditions de balise |
| Source de l’attribut | Ressource |
| Attribut | Balises d’index de blob [Valeurs dans la clé] |
| Clé : | {keyName} |
| Opérateur | StringEquals |
| Valeur | {keyValue} |

![Capture d’écran de l’éditeur de la condition 1 de l’exemple 8 dans Portail Azure.](./media/storage-auth-abac-examples/example-8-condition-1-portal.png)

| Condition no 2 | Paramètre |
| --- | --- |
| Actions | Lire un blob |
| Source de l’attribut | Ressource |
| Attribut | Chemin d’accès d’objet blob |
| Opérateur | StringLike |
| Valeur | {pathString} |

![Capture d’écran de l’éditeur de la condition 2 de l’exemple 8 dans Portail Azure.](./media/storage-auth-abac-examples/example-8-condition-2-portal.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Voici comment ajouter cette condition à l’aide d’Azure PowerShell.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Program<`$key_case_sensitive`$>] StringEquals 'Alpine')) AND ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'logs*'))"
$testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectID
$testRa.Condition = $condition
$testRa.ConditionVersion = "2.0"
Set-AzRoleAssignment -InputObject $testRa -PassThru
```

Voici comment tester cette condition.

```azurepowershell
$grantedContainer = "contosocorp"
# Get new context for request
$bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
# Try to get ungranted blobs
# Wrong name but right tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "AlpineFile.txt" -Context $bearerCtx
# Right name but wrong tags
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logsAlpine.txt" -Context $bearerCtx
# Try to get granted blob
$content = Get-AzStorageBlobContent -Container $grantedContainer -Blob "logs/AlpineFile.txt" -Context $bearerCtx
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux blobs à l’aide du portail Azure (préversion)](storage-auth-abac-portal.md)
- [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Format et syntaxe des conditions d’attribution de rôle Azure (préversion)](../../role-based-access-control/conditions-format.md)
