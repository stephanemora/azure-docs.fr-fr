---
title: 'Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant Azure CLI (préversion) - Azure ABAC'
titleSuffix: Azure Storage
description: Ajoutez une condition d’attribution de rôle pour restreindre l’accès aux objets blob avec Azure CLI et le contrôle d’accès en fonction de l’attribut Azure (Azure ABAC).
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: a2cf4c19e21ad13a2a79343713e9a0494eff0704
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489363"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-azure-cli-preview"></a>Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant Azure CLI (préversion)

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

## <a name="step-1-sign-in-to-azure"></a>Étape 1 : Se connecter à Azure

1. Utilisez la commande [az login](/cli/azure/reference-index#az_login) et suivez les instructions qui s’affichent pour vous connecter à votre annuaire en tant qu’[Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Propriétaire](../../role-based-access-control/built-in-roles.md#owner).

    ```azurecli
    az login
    ```

1. Utilisez [az account show](/cli/azure/account#az_account_show) pour obtenir l’ID de vos abonnements.

    ```azurecli
    az account show
    ```

1. Déterminez l’ID d’abonnement et initialisez la variable.

    ```azurecli
    subscriptionId="<subscriptionId>"
    ```

1. Si ce n’est déjà fait, inscrivez votre abonnement pour utiliser des étiquettes d’index d’objets blob. Pour plus d’informations, consultez [Inscrire votre abonnement (préversion)](../blobs/storage-manage-find-blobs.md#register-your-subscription-preview).

## <a name="step-2-create-a-user"></a>Étape 2 : Créer un utilisateur

1. Utilisez [az ad user create](/cli/azure/ad/user#az_ad_user_create) pour créer un utilisateur ou recherchez un utilisateur existant. Ce tutoriel utilise Chandra comme exemple.

1. Initialisez la variable pour l’ID d’objet de l’utilisateur.

    ```azurecli
    userObjectId="<userObjectId>"
    ```

## <a name="step-3-set-up-storage"></a>Étape 3 : Configurer le stockage

Vous pouvez autoriser l’accès au stockage Blob à partir d’Azure CLI avec des informations d’identification Azure AD ou en utilisant la clé d’accès au compte de stockage. Cet article explique comment autoriser les opérations de stockage Blob en utilisant Azure AD. Pour plus d’informations, consultez [Démarrage rapide : Créer, télécharger et lister des objets blob avec Azure CLI](../blobs/storage-quickstart-blobs-cli.md).

1. Utilisez [az storage account](/cli/azure/storage/account) pour créer un compte de stockage compatible avec la fonctionnalité d’index d’objets blob. Pour plus d’informations, consultez [Gérer et rechercher des données d’objets blob Azure avec des étiquettes d’index d’objets blob (préversion)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support).

1. Utilisez [az storage container](/cli/azure/storage/container) pour créer un conteneur d’objets blob dans le compte de stockage et définir le niveau d’accès public sur **Privé (pas d’accès anonyme)** .

1. Utilisez [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) pour charger un fichier texte sur le conteneur.

1. Ajoutez l’étiquette d’index d’objets blob suivante au fichier texte. Pour plus d’informations, consultez [Utiliser des balises d’index de blob (préversion) pour gérer et rechercher des données sur Stockage Blob Azure](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > Les objets blob prennent également en charge la possibilité de stocker des métadonnées clé/valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux étiquettes d’index d’objets blob, vous devez utiliser des étiquettes d’index d’objets blob avec les conditions. 

    | Clé | Valeur |
    | --- | --- |
    | Project  | Cascade |

1. Chargez un second fichier texte sur le conteneur.

1. Ajoutez l’étiquette d’index d’objets blob suivante au second fichier texte.

    | Clé | Valeur |
    | --- | --- |
    | Project  | Baker |

1. Initialisez les variables suivantes avec les noms que vous avez utilisés.

    ```azurecli
    resourceGroup="<resourceGroup>"
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameCascade="<blobNameCascade>"
    blobNameBaker="<blobNameBaker>"
    ```

## <a name="step-4-assign-a-role-with-a-condition"></a>Étape 4 : Attribuer un rôle avec une condition

1. Initialisez les variables du rôle [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

    ```azurecli
    roleDefinitionName="Storage Blob Data Reader"
    roleDefinitionId="2a2b9908-6ea1-4ae2-8e65-a410df84e7d1"
    ```

1. Initialisez l’étendue pour le groupe de ressources.

    ```azurecli
    scope="/subscriptions/$subscriptionId/resourceGroups/$resourceGroup"
    ```

1. Initialisez la condition.

    ```azurecli
    condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
    ```

    Dans Bash, si le développement de l’historique est activé, vous pouvez voir le message `bash: !: event not found` en raison du point d’exclamation ( !). Dans ce cas, vous pouvez désactiver le développement de l’historique avec la commande `set +H`. Pour réactiver le développement de l’historique, utilisez `set -H`.

    Dans Bash, un signe dollar ($) a une signification spéciale pour le développement. Si votre condition comprend un signe dollar ($), vous devrez peut-être le faire précéder d’une barre oblique inverse (\\). Par exemple, cette condition utilise des signes dollar pour délimiter le nom de la clé d’étiquette. Pour plus d’informations sur les règles de guillemets dans Bash, consultez [Double Quotes](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html) (Guillemets doubles).

1. Initialisez la version de la condition et la description.

    ```azurecli
    conditionVersion="2.0"
    description="Read access to blobs with the tag Project=Cascade"
    ```

1. Utilisez [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour attribuer le rôle [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) avec une condition à l’utilisateur dans une étendue de groupe de ressources.

    ```azurecli
    az role assignment create --assignee-object-id $userObjectId --scope $scope --role $roleDefinitionId --description "$description" --condition "$condition" --condition-version $conditionVersion
    ```

    Voici un exemple de sortie :

    ```azurecli
    {
      "canDelegate": null,
      "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
      "conditionVersion": "2.0",
      "description": "Read access to blobs with the tag Project=Cascade",
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

## <a name="step-5-optional-view-the-condition-in-the-azure-portal"></a>Étape 5 : (facultatif) Afficher la condition dans le portail Azure

1. Dans le portail Azure, ouvrez le groupe de ressources.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Sous l’onglet Attributions de rôles, recherchez l’attribution de rôle.

1. Dans la colonne **Condition**, cliquez sur **Afficher/Modifier** pour afficher la condition.

    ![Capture d’écran de la condition Ajouter une attribution de rôle dans le portail Azure.](./media/shared/condition-view.png)

## <a name="step-6-test-the-condition"></a>Étape 6 : Tester la condition

1. Ouvrez une nouvelle fenêtre de commande.

1. Exécutez la commande [az login](/cli/azure/reference-index#az_login) pour vous connecter en tant que Chandra.

    ```azurecli
    az login
    ```

1. Initialisez les variables suivantes avec les noms que vous avez utilisés.

    ```azurecli
    storageAccountName="<storageAccountName>"
    containerName="<containerName>"
    blobNameBaker="<blobNameBaker>"
    blobNameCascade="<blobNameCascade>"
    ```

1. Utilisez [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) pour essayer de lire les propriétés du fichier pour le projet Baker.

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameBaker --auth-mode login
    ```

    Voici un exemple de sortie. Notez que vous **ne pouvez pas** lire le fichier en raison de la condition que vous avez ajoutée.
    
    ```azurecli
    You do not have the required permissions needed to perform this operation.
    Depending on your operation, you may need to be assigned one of the following roles:
        "Storage Blob Data Contributor"
        "Storage Blob Data Reader"
        "Storage Queue Data Contributor"
        "Storage Queue Data Reader"
    
    If you want to use the old authentication method and allow querying for the right account key, please use the "--auth-mode" parameter and "key" value.
    ```
    
1. Lisez les propriétés du fichier pour le projet Cascade.

    ```azurecli
    az storage blob show --account-name $storageAccountName --container-name $containerName --name $blobNameCascade --auth-mode login 
    ```

    Voici un exemple de sortie. Notez que vous pouvez lire les propriétés du fichier, car il a l’étiquette Project=Cascade.
    
    ```azurecli
    {
      "container": "<containerName>",
      "content": "",
      "deleted": false,
      "encryptedMetadata": null,
      "encryptionKeySha256": null,
      "encryptionScope": null,
      "isAppendBlobSealed": null,
      "isCurrentVersion": null,
      "lastAccessedOn": null,
      "metadata": {},
      "name": "<blobNameCascade>",
      "objectReplicationDestinationPolicy": null,
      "objectReplicationSourceProperties": [],
      "properties": {
        "appendBlobCommittedBlockCount": null,
        "blobTier": "Hot",
        "blobTierChangeTime": null,
        "blobTierInferred": true,
        "blobType": "BlockBlob",
        "contentLength": 7,
        "contentRange": null,

      ...

    }
    ```

## <a name="step-7-optional-edit-the-condition"></a>Étape 7 : (facultatif) Modifier la condition

1. Dans l’autre fenêtre de commande, utilisez [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) pour récupérer l’attribution de rôle que vous avez ajoutée.

    ```azurecli
    az role assignment list --assignee $userObjectId --resource-group $resourceGroup
    ```

    Vous devez obtenir un résultat semblable à ce qui suit :
    
    ```azurecli
    [
      {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ]
    ```

1. Créez un fichier JSON au format suivant et mettez à jour les propriétés `condition` et `description`.

    ```json
    {
        "canDelegate": null,
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Baker'))",
        "conditionVersion": "2.0",
        "description": "Read access to blobs with the tag Project=Cascade or Project=Baker",
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{userObjectId}",
        "principalName": "chandra@contoso.com",
        "principalType": "User",
        "resourceGroup": "{resourceGroup}",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
        "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

1. Utilisez [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) pour mettre à jour la condition de l’attribution de rôle.

    ```azurecli
    az role assignment update --role-assignment "./path/roleassignment.json"
    ```
    
## <a name="step-8-clean-up-resources"></a>Étape 8 : Nettoyer les ressources

1. Utilisez [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) pour supprimer l’attribution de rôle et la condition que vous avez ajoutés.

    ```azurecli
    az role assignment delete --assignee $userObjectId --role "$roleDefinitionName" --resource-group $resourceGroup
    ```

1. Supprimez le compte de stockage que vous avez créé.

1. Supprimez l’utilisateur que vous avez créé.

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure](storage-auth-abac-examples.md)
- [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Format et syntaxe des conditions d’attribution de rôle Azure](../../role-based-access-control/conditions-format.md)
