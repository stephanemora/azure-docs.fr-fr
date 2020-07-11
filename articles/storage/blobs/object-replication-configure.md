---
title: Configurer la réplication d’objets (préversion)
titleSuffix: Azure Storage
description: Découvrez comment configurer la réplication d’objets pour copier de façon asynchrone des objets blob de blocs du conteneur d’un compte de stockage vers un autre.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9cb9f1a33c37487f4bfb1419d45d4e42a862d815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888112"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Configurer la réplication d’objets pour des objets blob de blocs (préversion)

La réplication d’objets (préversion) copie de façon asynchrone des objets blob de blocs entre un compte de stockage source et un compte de destination. Pour en savoir plus sur le sujet, consultez [Réplication d’objets (préversion)](object-replication-overview.md).

Lorsque vous configurez la réplication d’objets, vous créez une stratégie de réplication qui spécifie le compte de stockage source et le compte de destination. Une stratégie de réplication comprend une ou plusieurs règles qui spécifient un conteneur source et un conteneur de destination et indiquent quels objets blob de blocs du conteneur source seront répliqués.

Cet article explique comment configurer la réplication d’objets pour votre compte de stockage à l’aide de Portail Azure, de PowerShell ou d’Azure CLI. Vous pouvez également utiliser l’une des bibliothèques clientes du fournisseur de ressources Stockage Azure pour configurer la réplication d’objets.

## <a name="create-a-replication-policy-and-rules"></a>Créer une stratégie et des règles de réplication

Avant de configurer la réplication d’objets, créez les comptes de stockage source et de destination, s’ils n’existent pas déjà. Les deux comptes doivent être des comptes de stockage v2 universels. Pour plus d’informations, consultez la rubrique [Créer un compte Stockage Azure](../common/storage-account-create.md).

Un compte de stockage peut servir de compte source pour un maximum de deux comptes de destination. Et un compte de destination ne peut pas avoir plus de deux comptes sources. Les comptes source et de destination peuvent tous se trouver dans des régions différentes. Vous pouvez configurer des stratégies de réplication distinctes pour répliquer des données sur chacun des comptes de destination.

Avant de commencer, veillez à vous inscrire aux préversions des fonctionnalités suivantes :

- [Réplication d’objets (préversion)](object-replication-overview.md)
- [Contrôle de version des blobs (préversion)](versioning-overview.md)
- [Prise en charge du flux de modification dans Stockage Blob Azure (préversion)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Avant de configurer la réplication d’objets dans le portail Azure, créez les conteneurs source et de destination dans leurs comptes de stockage respectifs, s’ils n’existent pas déjà. En outre, activez le contrôle de version des blobs et le flux de modification sur le compte source, puis activez le contrôle de version des blobs sur le compte de destination.

Pour créer une stratégie de réplication dans le portail Azure, procédez comme suit :

1. Accédez au compte de stockage source dans le portail Azure.
1. Sous **Service BLOB**, sélectionnez **Réplication d’objet**.
1. Sélectionnez **Configurer la réplication**.
1. Sélectionnez le compte de stockage et l’abonnement de destination.
1. Dans la section **Paires de conteneurs**, sélectionnez un conteneur source dans le compte source et un conteneur de destination dans le compte de destination. Vous pouvez créer jusqu’à 10 paires de conteneurs par stratégie de réplication.

    L’illustration suivante montre un ensemble de règles de réplication.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Capture d’écran montrant des règles de réplication dans Portail Azure":::

1. Si vous le souhaitez, spécifiez un ou plusieurs filtres pour copier uniquement les blobs qui correspondent à un modèle de préfixe. Par exemple, si vous spécifiez le préfixe `b`, seuls les blobs dont le nom commence par cette lettre sont répliqués. Vous pouvez spécifier un répertoire virtuel comme élément du préfixe.

    L’illustration suivante montre des filtres qui limitent les blobs qui sont copiés dans le cadre d’une règle de réplication.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Capture d’écran montrant des filtres pour une règle de réplication":::

1. Par défaut, l’étendue de copie est définie pour copier uniquement les nouveaux objets. Pour copier tous les objets dans le conteneur ou pour copier les objets à partir d’une date et d’une heure personnalisées, sélectionnez le lien **Modifier** et configurez l’étendue de copie pour la paire de conteneurs.

    L’illustration suivante montre une étendue de copie personnalisée.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Capture d’écran montrant l’étendue de copie personnalisée pour la réplication d’objets":::

1. Sélectionnez **Enregistrer et appliquer** pour créer la stratégie de réplication et démarrer la réplication des données.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour créer une stratégie de réplication à l’aide de PowerShell, commencez par installer la version [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) du module PowerShell Az.Storage. Pour installer le module en préversion, procédez comme suit :

1. Désinstallez toutes les anciennes installations d’Azure PowerShell de Windows à l’aide du paramètre **Applications et fonctionnalités** sous **Paramètres**.

1. Vérifiez que la dernière version de PowerShellGet est installée. Ouvrez une fenêtre Windows PowerShell et exécutez la commande suivante pour installer la dernière version :

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Fermez, puis rouvrez la fenêtre PowerShell après l'installation de PowerShellGet.

1. Installez la dernière version d'Azure PowerShell :

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installez le module Az.Storage en préversion :

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Pour en savoir plus sur l’installation d’Azure PowerShell, voir [Installer Azure PowerShell avec PowerShellGet](/powershell/azure/install-az-ps).

L’exemple suivant montre comment créer une stratégie de réplication sur les comptes source et de destination. N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer une stratégie de réplication à l’aide d’Azure CLI, commencez par installer l’extension en préversion pour Stockage Azure :

```azurecli
az extension add -n storage-or-preview
```

Ensuite, connectez-vous avec vos informations d’identification Azure :

```azurecli
az login
```

Activez le contrôle de version des blobs sur les comptes de stockage source et de destination, puis activez le flux de modification sur le compte source. N’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Créez les conteneurs source et de destination dans leurs comptes de stockage respectifs.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Créez une nouvelle stratégie de réplication et les règles associées sur le compte de destination.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Créez la stratégie sur le compte source à l’aide de l’ID de la stratégie.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Supprimer une stratégie de réplication

Pour supprimer une stratégie de réplication et ses règles associées, utilisez Portail Azure, PowerShell ou CLI.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour supprimer une stratégie de réplication dans le portail Azure, procédez comme suit :

1. Accédez au compte de stockage source dans le portail Azure.
1. Sous **Paramètres**, sélectionnez **Réplication d’objet**.
1. Cliquez sur le bouton **Plus** à côté du nom de la stratégie.
1. Sélectionnez **Supprimer les règles**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour supprimer une stratégie de réplication, supprimez la stratégie du compte source et du compte de destination. La suppression de la stratégie supprime également toutes les règles qui lui sont associées.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour supprimer une stratégie de réplication, supprimez la stratégie du compte source et du compte de destination. La suppression de la stratégie supprime également toutes les règles qui lui sont associées.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la réplication d’objets (préversion)](object-replication-overview.md)
