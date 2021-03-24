---
title: Créer un instantané incrémentiel
description: Découvrez les instantanés incrémentiels pour les disques managés, et notamment comment les créer avec le Portail Azure, le module Azure PowerShell et Azure Resource Manager.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 52e491c88d3483f21aa74f1a9f176246033bee3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735790"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Création d’un instantané incrémentiel pour les disques managés

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vous pouvez utiliser Azure PowerShell pour créer un instantané incrémentiel. Vous devez disposer de la dernière version d'Azure PowerShell. La commande suivante vous permettra de l'installer ou de mettre à jour votre installation existante :

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Une fois la dernière version installée, connectez-vous à votre session PowerShell à l'aide de la commande `Connect-AzAccount`.

Pour créer un instantané incrémentiel avec Azure PowerShell, définissez la configuration avec [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) avec le paramètre `-Incremental`, puis passez-la en tant que variable à [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) par le biais du paramètre `-Snapshot`.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Vous pouvez identifier des instantanés incrémentiels à partir du même disque avec les propriétés `SourceResourceId` et `SourceUniqueId` des instantanés. `SourceResourceId` est l’ID de ressource Azure Resource Manager du disque parent. `SourceUniqueId` est la valeur héritée de la propriété `UniqueId` du disque. Si vous supprimez un disque et que vous créez ensuite un disque portant le même nom, la valeur de la propriété `UniqueId` change.

Vous pouvez utiliser `SourceResourceId` et `SourceUniqueId` pour créer une liste de tous les instantanés associés à un disque particulier. Remplacez `<yourResourceGroupNameHere>` par votre valeur, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants :

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portail](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Vous pouvez également utiliser des modèles Azure Resource Manager pour créer un instantané incrémentiel. Vous devez vous assurer que le paramètre apiVersion est défini sur **2019-03-01** et que la propriété incremental est définie sur true. L'extrait de code suivant montre comment créer un instantané incrémentiel à l'aide de modèles Resource Manager :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez voir un exemple de code illustrant la capacité différentielle des instantanés incrémentiels à l’aide de .NET, consultez [Copy Azure Managed Disks backups to another region with differential capability of incremental snapshots](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots) (Copier des sauvegardes de disques managés Azure dans une autre région avec la fonctionnalité différentielle des instantanés incrémentiels).
