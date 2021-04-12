---
title: Gérer le niveau d’accès d’un BLOB dans un compte de stockage Azure
description: Découvrez comment modifier le niveau d’un BLOB dans un compte de stockage GPv2 ou un compte Stockage Blob.
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: cd46f5b5a8847150bd56c1daeaac470f9afd2d19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278539"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Gérer le niveau d’accès d’un BLOB dans un compte de stockage Azure

Chaque BLOB dispose d’un niveau d’accès par défaut : chaud, froid ou archive. Un BLOB prend le niveau d’accès par défaut du compte de stockage Azure où il est créé. Les comptes Stockage Blob et GPv2 exposent l’attribut **Niveau d’accès** au niveau du compte. Cet attribut spécifie le niveau d’accès par défaut pour tous les BLOB ne disposant pas déjà d’un niveau défini explicitement au niveau de l’objet. Le niveau du compte ne s’applique pas aux objets disposant d’un niveau établi au niveau de l’objet. Le niveau d’accès archive ne peut être appliqué qu’au niveau de l’objet. Vous pouvez passer d’un niveau d’accès à un autre à tout moment en suivant les étapes ci-dessous.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’un blob dans un compte Stockage Blob ou GPv2

Les scénarios suivants utilisent le portail Azure ou PowerShell :

# <a name="portal"></a>[Portail](#tab/portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la Portail Azure, recherchez et sélectionnez **Toutes les ressources**.

1. Sélectionnez votre compte de stockage.

1. Sélectionnez votre conteneur, puis sélectionnez votre objet blob.

1. Dans **Propriétés de l’objet blob**, sélectionnez **Modifiez le niveau**.

1. Sélectionnez le niveau d’accès **Chaud**, **Froid** ou **Archive**. Si votre objet blob est actuellement dans le niveau archive et que vous souhaitez le réalimenter dans un niveau en ligne, vous pouvez également sélectionner une priorité de réalimentation **Standard** ou **Élevée**.

1. Sélectionnez **Enregistrer** en bas.

![Changer le niveau d’objet blob par défaut dans le portail Azure](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Le script PowerShell suivant permet de changer le niveau de l’objet blob. La variable `$rgName` doit être initialisée avec le nom de votre groupe de ressources. La variable `$accountName` doit être initialisée avec le nom de votre compte de stockage. La variable `$containerName` doit être initialisée avec le nom de votre conteneur. La variable `$blobName` doit être initialisée avec le nom de votre objet blob.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Comment gérer le niveau d’accès par défaut d’un compte de stockage Azure](../common/manage-account-default-access-tier.md)
- [Découvrir comment réalimenter les données d’objets blob à partir du niveau Archive](storage-blob-rehydration.md)
- [Vérifier la tarification du niveau chaud, froid et archive dans les comptes de stockage d’objets blob et GPv2 par région](https://azure.microsoft.com/pricing/details/storage/)
