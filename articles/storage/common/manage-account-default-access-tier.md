---
title: Gestion du niveau d’accès par défaut d’un compte de stockage Azure
description: Découvrez comment changer le niveau d’accès par défaut d’un compte de stockage GPv2 ou de Stockage Blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653830"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Gestion du niveau d’accès par défaut d’un compte de stockage Azure

Chaque compte de stockage Azure dispose d’un niveau d’accès par défaut, chaud ou froid. attribué lors de la création du compte de stockage. Le niveau d’accès par défaut est chaud.

Il est possible de modifier le niveau du compte par défaut en définissant l’attribut **Niveau d’accès** du compte de stockage. La modification s’applique à tous les objets stockés dans le compte pour lesquels aucun niveau explicite n’est défini. Passer du niveau de compte chaud au niveau froid implique des opérations d’écriture (par 10 000) pour tous les blobs sans niveau défini dans les comptes GPv2 uniquement, tandis que le passage du niveau froid au niveau chaud entraîne des frais d’opérations de lecture (par 10 000) et d’extraction de données (par Go) pour tous les blobs dans des comptes Stockage Blob et GPv2.

Le niveau du compte ne s’applique pas aux objets blob dont le niveau est défini au niveau de l’objet. Le niveau de stockage archive peut être appliqué uniquement au niveau de l’objet. Il est à tout moment possible de passer d’un niveau d’accès à un autre.

Pour modifier le niveau d’accès par défaut après avoir créé un compte de stockage, suivez la procédure ci-dessous.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Changer le niveau d’accès de compte par défaut d’un compte Stockage Blob ou GPv2

Les scénarios suivants utilisent le Portail Azure ou PowerShell :

# <a name="portal"></a>[Portail](#tab/portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la Portail Azure, recherchez et sélectionnez **Toutes les ressources**.

1. Sélectionnez votre compte de stockage.

1. Dans **Paramètres**, cliquez su **Configuration** pour afficher et modifier la configuration du compte.

1. Sélectionnez le niveau d’accès adapté à vos besoins : Affectez la valeur **Froid** ou **Chaud** au **Niveau d’accès**.

1. Cliquez sur **Enregistrer** en haut.

![Changer le niveau de compte par défaut dans le portail Azure](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Le script PowerShell suivant permet de changer le niveau du compte. La variable `$rgName` doit être initialisée avec le nom de votre groupe de ressources. La variable `$accountName` doit être initialisée avec le nom de votre compte de stockage.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour gérer le niveau d’un objet blob dans un compte de stockage Azure](../blobs/manage-access-tier.md)
- [Déterminer si les performances de l’édition Premium peuvent tirer parti de votre application](../blobs/storage-blob-performance-tiers.md)
- [Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](../blobs/monitor-blob-storage.md)
