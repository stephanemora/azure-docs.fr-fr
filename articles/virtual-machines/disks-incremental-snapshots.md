---
title: Créer un instantané incrémentiel
description: Découvrez les instantanés incrémentiels pour les disques managés, et notamment comment les créer avec le Portail Azure, le module Azure PowerShell et Azure Resource Manager.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bbd87d8f8e4c140c1ced76a3c60e82d3066d2f5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532474"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Création d’un instantané incrémentiel pour les disques managés

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez utiliser l’interface de ligne de commande Azure pour créer un instantané incrémentiel. Vous devez disposer de la dernière version de l’interface de ligne de commande Azure. Consultez les articles suivants pour savoir comment [installer](/cli/azure/install-azure-cli) ou [mettre à jour](/cli/azure/update-azure-cli) l’interface de ligne de commande Azure.

Le script suivant crée un instantané incrémentiel d’un disque particulier :

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

Vous pouvez identifier des instantanés incrémentiels à partir du même disque avec la propriété `SourceResourceId` des instantanés. `SourceResourceId` est l’ID de ressource Azure Resource Manager du disque parent.

Vous pouvez utiliser `SourceResourceId` pour créer une liste de tous les instantanés associés à un disque particulier. Remplacez `yourResourceGroupNameHere` par votre valeur, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants :


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Vous pouvez utiliser le module Azure PowerShell pour créer un instantané incrémentiel. Vous devez utiliser la dernière version du module Azure PowerShell. La commande suivante vous permet de l’installer ou de mettre à jour votre installation existante avec la dernière version :

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

Vous pouvez utiliser `SourceResourceId` et `SourceUniqueId` pour créer une liste de tous les instantanés associés à un disque particulier. Remplacez `yourResourceGroupNameHere` par votre valeur, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants :

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
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
