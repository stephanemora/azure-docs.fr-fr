---
title: Activer la suppression réversible - Partages de fichiers Azure
description: Découvrez comment activer la suppression réversible sur des partages de fichiers Azure pour récupérer des données et empêcher la suppression accidentelle.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
services: storage
ms.openlocfilehash: 314d1ee8ee957b21679735594ad2f7d7f50f4d38
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118318"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Activer la suppression réversible sur les partages de fichiers Azure
Azure Files offre une fonctionnalité de suppression réversible pour les partages de fichiers, ce qui vous permet de récupérer plus facilement vos données en cas de suppressions malencontreuses par une application ou un autre utilisateur du compte de stockage. Pour en savoir plus sur la suppression réversible, consultez [Empêcher la suppression accidentelle de partages de fichiers Azure](storage-files-prevent-file-share-deletion.md).

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Oui](../media/icons/yes-icon.png) | ![Non](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Configuration requise
- Si vous envisagez d’utiliser Azure PowerShell, [installez-en la dernière version](/powershell/azure/install-az-ps).
- Si vous envisagez d’utiliser Azure CLI, [installez-en la dernière version](/cli/azure/install-azure-cli).

## <a name="getting-started"></a>Bien démarrer
Les sections suivantes montrent comment activer et utiliser la suppression réversible pour les partages de fichiers Azure sur un compte de stockage existant :

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte de stockage, puis sélectionnez **Partages de fichiers** sous **Stockage de données**.
1. Sélectionnez **Activé** en regard de **Suppression réversible**.
1. Sélectionnez **Activé** pour **Suppression réversible pour tous les partages de fichiers**.
1. Sélectionnez **Période de rétention du partage de fichiers en jours** et entrez un nombre de votre choix.
1. Sélectionnez **Enregistrer** pour confirmer vos paramètres de conservation des données.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Capture d’écran du volet des paramètres de suppression réversible du compte de stockage. Mise en surbrillance de la section de suppression réversible des partages de fichiers, du commutateur d’activation/de désactivation, de la définition d’une période de conservation et de l’option d’enregistrement. Cela entraîne l’activation de la suppression réversible pour tous les partages de fichiers dans votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour activer la suppression réversible, vous devez mettre à jour les paramètres de tous les partages de fichiers Azure, également appelés les propriétés `FileService`. L’exemple suivant active la suppression réversible pour tous les partages de fichiers dans un compte de stockage. N’oubliez pas de remplacer `<resource-group>` et `<storage-account>` par les valeurs correspondant à votre environnement.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $true `
    -ShareRetentionDays 7
```

Vous pouvez vérifier si la suppression réversible est activée et afficher sa stratégie de rétention à l’aide de la commande suivante :

```PowerShell
Get-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour activer la suppression réversible, vous devez mettre à jour les propriétés du service du client d’un fichier. L’exemple suivant active la suppression réversible pour tous les partages de fichiers dans un compte de stockage. N’oubliez pas de remplacer `<resource-group>` et `<storage-account>` par les valeurs correspondant à votre environnement.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Vous pouvez vérifier si la suppression réversible est activée et afficher sa stratégie de rétention à l’aide de la commande suivante :

```bash
az storage account file-service-properties show \
    -resource-group $resourceGroupName \
    -account-name $storageAccountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Restaurer le partage de fichiers supprimé de manière réversible

# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour restaurer un partage de fichiers supprimé de manière réversible :

1. Accédez à votre compte de stockage et sélectionnez **Partages de fichiers**.
1. Dans le panneau du partage de fichiers, activez **Afficher les partages supprimés** pour afficher tous les partages qui ont été supprimés de manière réversible.

    Cela affiche tous les partages actuellement dans un état **Supprimé**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Si la colonne État, la colonne à côté de la colonne de nom, est définie sur Supprimé, votre partage de fichiers est dans un état supprimé de manière réversible. Et il sera définitivement supprimé après la période de rétention spécifiée.":::

1. Sélectionnez le partage et **Annuler la suppression**. Le partage sera restauré.

    Vous pouvez vérifier que le partage est restauré, car son état passe à **Actif**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Si la colonne État, la colonne à côté de la colonne de nom, est définie sur Actif, votre partage de fichiers a été restauré.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour restaurer un partage de fichiers supprimé de manière réversible, vous devez d’abord obtenir la valeur `-DeletedShareVersion` du partage. Pour obtenir cette valeur, utilisez la commande suivante pour répertorier tous les partages supprimés pour votre compte de stockage.

```PowerShell
Get-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -IncludeDeleted
```

Une fois que vous avez identifié le partage que vous souhaitez restaurer, vous pouvez l’utiliser avec la commande suivante pour le restaurer :

```PowerShell
Restore-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -DeletedShareVersion 01D5E2783BDCDA97 # replace with your deleted version number
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour restaurer un partage de fichiers supprimé de manière réversible, vous devez d’abord obtenir la valeur `--deleted-version` du partage. Pour obtenir cette valeur, utilisez la commande suivante pour répertorier tous les partages supprimés pour votre compte de stockage.

```bash
az storage share-rm list \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --include-deleted
```

Une fois que vous avez identifié le partage que vous souhaitez restaurer, vous pouvez l’utiliser avec la commande suivante pour le restaurer :

```bash
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

---

## <a name="disable-soft-delete"></a>Désactiver la suppression réversible
Pour cesser d’utiliser la suppression réversible, suivez ces instructions. Pour supprimer définitivement un partage de fichiers qui a été supprimé de manière réversible, vous devez le restaurer, désactiver la suppression réversible, puis le supprimer à nouveau. 

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à votre compte de stockage, puis sélectionnez **Partages de fichiers** sous **Stockage de données**.
1. Sélectionnez le lien en regard de **Suppression réversible**.
1. Sélectionnez **Désactivé** pour **Suppression réversible pour tous les partages de fichiers**.
1. Sélectionnez **Enregistrer** pour confirmer vos paramètres de conservation des données.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="La désactivation de la suppression réversible vous permet de supprimer immédiatement et définitivement tous les partages de fichiers de votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vous pouvez utiliser la commande suivante pour désactiver la suppression réversible sur votre compte de stockage.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Vous pouvez utiliser la commande suivante pour désactiver la suppression réversible sur votre compte de stockage.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --enable-delete-retention false
```

---

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur une autre forme de protection et de récupération des données, consultez notre article [vue d’ensemble des instantanés de partage pour Azure Files](storage-snapshots-files.md).
