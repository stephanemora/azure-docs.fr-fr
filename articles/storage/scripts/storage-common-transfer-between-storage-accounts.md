---
title: Effectuer la migration d’objets blob entre des comptes de stockage à l’aide d’AzCopy sur Windows
description: Exemple de script Azure PowerShell - Utiliser AzCopy pour copier des objets blob d’un compte de stockage Azure à un autre
services: storage
documentationcenter: na
author: normesta
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: normesta
ms.openlocfilehash: 559b8b2875b789034ae07901f668f241505073b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465065"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrer des objets blob entre des comptes de stockage à l’aide d’AzCopy sur Windows

Cet exemple copie tous les objets blob d’un compte de stockage source fourni par l’utilisateur dans un compte de stockage cible fourni par l’utilisateur. 

Cette opération est effectuée avec la commande `Get-AzStorageContainer`, qui répertorie tous les conteneurs d’un compte de stockage. L’exemple émet ensuite les commandes AzCopy qui copient chaque conteneur du compte de stockage source dans le compte de stockage de destination. En cas d’échec, l’exemple effectue une ou plusieurs nouvelles tentatives en fonction de la valeur $retryTimes définie (la valeur par défaut est trois, mais vous pouvez la changer à l’aide du paramètre `-RetryTimes`). Si chaque nouvelle tentative échoue, l’utilisateur peut réexécuter le script en utilisant l’exemple avec le dernier conteneur copié avec succès à l’aide du paramètre `-LastSuccessContainerName`. L’exemple poursuit alors la copie des conteneurs à partir de ce point.

Cet exemple nécessite le module Stockage Azure PowerShell version **0.7** ou ultérieure. Vous pouvez utiliser `Get-Module -ListAvailable Az.storage` pour vérifier quelle version est installée. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Cet exemple nécessite également la dernière version [d’AzCopy sur Windows](https://aka.ms/downloadazcopy). Le répertoire d’installation par défaut est `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Cet exemple spécifie le nom et la clé d’un compte de stockage source, le nom et la clé d’un compte de stockage cible, et le chemin complet d’AzCopy.exe (s’il n’est pas installé dans le répertoire par défaut).

Voici quelques extraits de code correspondant à cet exemple :

Si AzCopy est installé dans le répertoire par défaut :
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Si AzCopy n’est pas installé dans le répertoire par défaut :

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Si l’exemple échoue et qu’il doit être réexécuté à partir d’un conteneur spécifique : 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour copier des données d’un compte de stockage à un autre. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Retourne les conteneurs associés à ce compte de stockage. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Crée un contexte de stockage Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-powershell.md).
