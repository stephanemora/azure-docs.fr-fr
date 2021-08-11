---
title: 'Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant Azure PowerShell (préversion) - Azure ABAC'
titleSuffix: Azure Storage
description: Ajoutez une condition d’attribution de rôle pour restreindre l’accès aux objets blob avec Azure PowerShell et le contrôle d’accès en fonction de l’attribut Azure (Azure ABAC).
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: d6cb1980c93e5161f02b79b05f1128ba777027c6
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281950"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-powershell-preview"></a>Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant Azure PowerShell (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans la plupart des cas, une attribution de rôle accorde les autorisations dont vous avez besoin sur des ressources Azure. Toutefois, dans certains cas, vous souhaiterez peut-être fournir un contrôle d’accès plus précis en ajoutant une condition d’attribution de rôle.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajouter une condition à une attribution de rôle
> * Restreindre l’accès aux objets blob en fonction d’une étiquette d’index d’objets blob

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](../../role-based-access-control/conditions-prerequisites.md).

## <a name="condition"></a>Condition

Dans ce tutoriel, vous limitez l’accès aux objets blob dotés d’une étiquette spécifique. Par exemple, vous ajoutez une condition à une attribution de rôle afin que Chandra puisse uniquement lire les fichiers auxquels est associée l’étiquette Project=Cascade.

![Diagramme de l’attribution de rôle avec une condition.](./media/shared/condition-role-assignment-rg.png)

Si Chandra tente de lire un objet blob dépourvu de l’étiquette Project=Cascade, l’accès n’est pas autorisé.

![Diagramme montrant l’accès en lecture aux objets blob dotés de l’étiquette Project=Cascade.](./media/shared/condition-access.png)

Voici à quoi ressemble la condition exprimée sous forme de code :

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

## <a name="step-1-install-prerequisites"></a>Étape 1 : Installer les prérequis

1. Ouvrez une fenêtre PowerShell.

1. Utilisez [Get-InstalledModule](/powershell/module/powershellget/get-installedmodule) pour vérifier les versions des modules installés.

    ```azurepowershell
    Get-InstalledModule -Name Az
    Get-InstalledModule -Name Az.Resources
    Get-InstalledModule -Name Az.Storage
    ```

1. Si nécessaire, utilisez [Install-Module](/powershell/module/powershellget/install-module) afin d’installer les versions requises pour les modules [Az](https://www.powershellgallery.com/packages/Az/), [Az.Resources](https://www.powershellgallery.com/packages/Az.Resources/) et [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview).

    ```azurepowershell
    Install-Module -Name Az -RequiredVersion 5.5.0
    Install-Module -Name Az.Resources -RequiredVersion 3.2.1
    Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
    ```

1. Fermez et rouvrez PowerShell pour actualiser la session.

## <a name="step-2-sign-in-to-azure"></a>Étape 2 : Se connecter à Azure

1. Utilisez la commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) et suivez les instructions qui s’affichent pour vous connecter à votre annuaire en tant qu’[Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Propriétaire](../../role-based-access-control/built-in-roles.md#owner).

    ```azurepowershell
    Connect-AzAccount
    ```

1. Utilisez [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) pour lister tous vos abonnements.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Déterminez l’ID d’abonnement et initialisez la variable.

    ```azurepowershell
    $subscriptionId = "<subscriptionId>"
    ```

1. Définissez l’abonnement en tant qu’abonnement actif.

    ```azurepowershell
    $context = Get-AzSubscription -SubscriptionId $subscriptionId
    Set-AzContext $context
    ```

## <a name="step-3-create-a-user"></a>Étape 3 : Créer un utilisateur

1. Utilisez [New-AzureADUser](/powershell/module/azuread/new-azureaduser) pour créer un utilisateur ou recherchez un utilisateur existant. Ce tutoriel utilise Chandra comme exemple.

1. Initialisez la variable pour l’ID d’objet de l’utilisateur.

    ```azurepowershell
    $userObjectId = "<userObjectId>"
    ```

## <a name="step-4-set-up-storage"></a>Étape 4 : Configurer le stockage

1. Utilisez [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) pour créer un compte de stockage compatible avec la fonctionnalité d’index d’objets blob. Pour plus d’informations, consultez [Gérer et rechercher des données d’objets blob Azure avec des étiquettes d’index d’objets blob (préversion)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support).

1. Utilisez [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) pour créer un conteneur d’objets blob dans le compte de stockage et définir le niveau d’accès public sur **Privé (pas d’accès anonyme)** .

1. Utilisez [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) pour charger un fichier texte sur le conteneur.

1. Ajoutez l’étiquette d’index d’objets blob suivante au fichier texte. Pour plus d’informations, consultez [Utiliser des balises d’index de blob (préversion) pour gérer et rechercher des données sur Stockage Blob Azure](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > Les objets blob prennent également en charge la possibilité de stocker des métadonnées clé/valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux étiquettes d’index d’objet blob, vous devez utiliser des étiquettes d’index d’objet blob avec les conditions. 

    | Clé | Valeur |
    | --- | --- |
    | Project  | Cascade |

1. Chargez un second fichier texte sur le conteneur.

1. Ajoutez l’étiquette d’index d’objets blob suivante au second fichier texte.

    | Clé | Valeur |
    | --- | --- |
    | Project  | Baker |

1. Initialisez les variables suivantes avec les noms que vous avez utilisés.

    ```azurepowershell
    $resourceGroup = "<resourceGroup>"
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameCascade = "<blobNameCascade>"
    $blobNameBaker = "<blobNameBaker>"
    ```

## <a name="step-5-assign-a-role-with-a-condition"></a>Étape 5 : Attribuer un rôle avec une condition

1. Initialisez les variables du rôle [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

    ```azurepowershell
    $roleDefinitionName = "Storage Blob Data Reader"
    $roleDefinitionId = "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. Initialisez l’étendue pour le groupe de ressources.

    ```azurepowershell
    $scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. Initialisez la condition.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
    ```

    Dans PowerShell, si votre condition comprend un signe dollar ($), vous devez le faire précéder d’un guillemet inversé (\`). Par exemple, cette condition utilise des signes dollar pour délimiter le nom de la clé d’étiquette.

1. Initialisez la version de la condition et la description.

    ```azurepowershell
    $conditionVersion = "2.0"
    $description = "Read access to blobs with the tag Project=Cascade"
    ```

1. Utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) pour attribuer le rôle [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) avec une condition à l’utilisateur dans une étendue de groupe de ressources.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $userObjectId -Scope $scope -RoleDefinitionId $roleDefinitionId -Description $description -Condition $condition -ConditionVersion $conditionVersion
    ```

    Voici un exemple de sortie :
    
    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))
    ```

## <a name="step-6-optional-view-the-condition-in-the-azure-portal"></a>Étape 6 : (facultatif) Afficher la condition dans le portail Azure

1. Dans le portail Azure, ouvrez le groupe de ressources.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Sous l’onglet Attributions de rôles, recherchez l’attribution de rôle.

1. Dans la colonne **Condition**, cliquez sur **Afficher/Modifier** pour afficher la condition.

    ![Capture d’écran de la condition Ajouter une attribution de rôle dans le portail Azure.](./media/shared/condition-view.png)

## <a name="step-7-test-the-condition"></a>Étape 7 : Tester la condition

1. Ouvrez une nouvelle fenêtre PowerShell.

1. Utilisez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter en tant que Chandra.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Initialisez les variables suivantes avec les noms que vous avez utilisés.

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. Utilisez [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) afin de créer un contexte spécifique pour accéder à votre compte de stockage plus facilement.

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. Utilisez [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) pour essayer de lire le fichier du projet Baker.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    Voici un exemple de sortie. Notez que vous **ne pouvez pas** lire le fichier en raison de la condition que vous avez ajoutée.
    
    ```azurepowershell
    Get-AzStorageBlob : This request is not authorized to perform this operation using this permission. HTTP Status Code:
    403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sat, 24 Apr 2021 13:26:25 GMT
    At line:1 char:1
    + Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Con ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzStorageBlob], StorageException
        + FullyQualifiedErrorId : StorageException,Microsoft.WindowsAzure.Commands.Storage.Blob.Cmdlet.GetAzureStorageBlob
       Command
    ```
    
1. Lisez le fichier du projet Cascade.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    Voici un exemple de sortie. Notez que vous pouvez lire le fichier, car il a l’étiquette Project=Cascade.
    
    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-8-optional-edit-the-condition"></a>Étape 8 : (facultatif) Modifier la condition

1. Dans l’autre fenêtre PowerShell, utilisez [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) pour récupérer l’attribution de rôle que vous avez ajoutée.

    ```azurepowershell
    $testRa = Get-AzRoleAssignment -Scope $scope -RoleDefinitionName $roleDefinitionName -ObjectId $userObjectId
    ```

1. Modifiez la condition.

    ```azurepowershell
    $condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Baker'))"
    ```

1. Initialisez la condition et la description.

    ```azurepowershell
    $testRa.Condition = $condition
    $testRa.Description = "Read access to blobs with the tag Project=Cascade or Project=Baker"
    ```

1. Utilisez [Set-AzRoleAssignment](/powershell/module/az.resources/set-azroleassignment) pour mettre à jour la condition de l’attribution de rôle.

    ```azurepowershell
    Set-AzRoleAssignment -InputObject $testRa -PassThru
    ```

    Voici un exemple de sortie :

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microso
                         ft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    DisplayName        : Chandra
    SignInName         : chandra@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <userObjectId>
    ObjectType         : User
    CanDelegate        : False
    Description        : Read access to blobs with the tag Project=Cascade or Project=Baker
    ConditionVersion   : 2.0
    Condition          : ((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND
                         @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR
                         (@Resource[Microsoft.Storage/storageAccounts/blobServices/co
                         ntainers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.S
                         torage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>]
                         StringEquals 'Baker'))
    ```
    
## <a name="step-9-clean-up-resources"></a>Étape 9 : Nettoyer les ressources

1. Utilisez [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) pour supprimer l’attribution de rôle et la condition que vous avez ajoutés.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $userObjectId -RoleDefinitionName $roleDefinitionName -ResourceGroupName $resourceGroup
    ```

1. Supprimez le compte de stockage que vous avez créé.

1. Supprimez l’utilisateur que vous avez créé.

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure](storage-auth-abac-examples.md)
- [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Format et syntaxe des conditions d’attribution de rôle Azure](../../role-based-access-control/conditions-format.md)
