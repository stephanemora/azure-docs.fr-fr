---
title: Activer la suppression réversible - Partages de fichiers Azure
description: Découvrez comment activer la suppression réversible (préversion) sur des partages de fichiers Azure pour récupérer des données et empêcher la suppression accidentelle.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 582a3603ad80ec1312429ed7cbd140d4310fadcb
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196198"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Activer la suppression réversible sur les partages de fichiers Azure

Le Stockage Azure offre une fonctionnalité de suppression réversible pour les partages de fichiers (préversion), ce qui vous permet de récupérer plus facilement vos données en cas de suppressions accidentelles par une application ou un autre utilisateur du compte de stockage. Pour en savoir plus sur la suppression réversible, consultez [Empêcher la suppression accidentelle de partages de fichiers Azure](storage-files-prevent-file-share-deletion.md).

Les sections suivantes montrent comment activer et utiliser la suppression réversible pour les partages de fichiers Azure sur un compte de stockage existant :

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte de stockage et sélectionnez **Suppression réversible** sous **Service de fichiers**.
1. Sélectionnez **Activé** pour la **suppression réversible de partage de fichiers**.
1. Sélectionnez **Période de rétention du partage de fichiers en jours** et entrez un nombre de votre choix.
1. Sélectionnez **Enregistrer** pour confirmer vos paramètres de conservation des données.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Capture d’écran du volet des paramètres de suppression réversible du compte de stockage. Mise en surbrillance de la section des partages de fichiers, du commutateur activer/désactiver, de la définition d’une période de rétention et de l’option Enregistrer. Cela active la suppression réversible pour tous les partages de fichiers dans votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour activer la suppression réversible, vous devez mettre à jour les propriétés du service du client d’un fichier. L’exemple suivant active la suppression réversible pour tous les partages de fichiers dans un compte de stockage :

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Vous pouvez vérifier si la suppression réversible est activée et afficher sa stratégie de rétention à l’aide de la commande suivante :

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
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

Pour restaurer un partage de fichiers supprimé de manière réversible, utilisez la commande suivante :

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Désactiver la suppression réversible

Si vous souhaitez cesser d’utiliser la suppression réversible ou supprimer définitivement un partage de fichiers, suivez ces instructions :

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez à votre compte de stockage et sélectionnez **Suppression réversible** sous **Paramètres**.
1. Sous **Partages de fichiers**, sélectionnez **Désactivé** pour **Suppression réversible pour les partages de fichiers**.
1. Sélectionnez **Enregistrer** pour confirmer vos paramètres de conservation des données.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="La désactivation de la suppression réversible vous permet de supprimer immédiatement et définitivement tous les partages de fichiers de votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vous pouvez utiliser la commande suivante pour désactiver la suppression réversible sur votre compte de stockage :

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur une autre forme de protection et de récupération des données, consultez notre article [vue d’ensemble des instantanés de partage pour Azure Files](storage-snapshots-files.md).
