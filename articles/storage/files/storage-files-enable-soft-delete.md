---
title: Activer la suppression réversible - Partages de fichiers Azure
description: Découvrez comment activer la suppression réversible (préversion) sur des partages de fichiers Azure pour récupérer des données et empêcher la suppression accidentelle.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 2d2a000879a95f86a6cdda3324add5b692476eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88590113"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Activer la suppression réversible sur les partages de fichiers Azure

Le Stockage Azure offre une fonctionnalité de suppression réversible pour les partages de fichiers (préversion), ce qui vous permet de récupérer plus facilement vos données en cas de suppressions accidentelles par une application ou un autre utilisateur du compte de stockage. Pour en savoir plus sur la suppression réversible, consultez [Empêcher la suppression accidentelle de partages de fichiers Azure](storage-files-prevent-file-share-deletion.md).

Les sections suivantes montrent comment activer et utiliser la suppression réversible pour les partages de fichiers Azure sur un compte de stockage existant :

# <a name="portal"></a>[Portail](#tab/azure-portal)

## <a name="getting-started"></a>Prise en main

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Accédez à votre compte de stockage et sélectionnez **Suppression réversible** sous **Service de fichiers**.
1. Sélectionnez **Activé** pour la **suppression réversible de partage de fichiers**.
1. Sélectionnez **Période de rétention du partage de fichiers en jours** et entrez un nombre de votre choix.
1. Sélectionnez **Enregistrer** pour confirmer vos paramètres de conservation des données.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Capture d’écran du volet des paramètres de suppression réversible du compte de stockage. Mise en surbrillance de la section des partages de fichiers, du commutateur activer/désactiver, de la définition d’une période de rétention et de l’option Enregistrer. Cela active la suppression réversible pour tous les partages de fichiers dans votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Prérequis

Les cmdlets de suppression réversible sont actuellement disponibles uniquement dans les versions [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) et [2.3.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.3.1-preview) du module Az.Storage. 

## <a name="getting-started"></a>Prise en main

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

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Capture d’écran du volet des paramètres de suppression réversible du compte de stockage. Mise en surbrillance de la section des partages de fichiers, du commutateur activer/désactiver, de la définition d’une période de rétention et de l’option Enregistrer. Cela active la suppression réversible pour tous les partages de fichiers dans votre compte de stockage.":::

1. Sélectionnez le partage et **Annuler la suppression**. Le partage sera restauré.

    Vous pouvez vérifier que le partage est restauré, car son état passe à **Actif**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Capture d’écran du volet des paramètres de suppression réversible du compte de stockage. Mise en surbrillance de la section des partages de fichiers, du commutateur activer/désactiver, de la définition d’une période de rétention et de l’option Enregistrer. Cela active la suppression réversible pour tous les partages de fichiers dans votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Les cmdlets de suppression réversible sont disponibles dans la version 2.1.1-preview du module Az.Storage. Pour restaurer un partage de fichiers supprimé de manière réversible, utilisez la commande suivante :

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

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Capture d’écran du volet des paramètres de suppression réversible du compte de stockage. Mise en surbrillance de la section des partages de fichiers, du commutateur activer/désactiver, de la définition d’une période de rétention et de l’option Enregistrer. Cela active la suppression réversible pour tous les partages de fichiers dans votre compte de stockage.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Les cmdlets de suppression réversible sont disponibles dans la version 2.1.1-preview du module Az.Storage. Vous pouvez utiliser la commande suivante pour désactiver la suppression réversible sur votre compte de stockage :

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur une autre forme de protection et de récupération des données, consultez notre article [vue d’ensemble des instantanés de partage pour Azure Files](storage-snapshots-files.md).
