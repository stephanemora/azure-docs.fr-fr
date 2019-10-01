---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224577"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Créer un instantané incrémentiel (préversion) pour les disques managés

Les instantanés incrémentiels (préversion) sont des sauvegardes des disques managés exécutées à un moment donné et contenant uniquement les modifications apportées depuis la dernière capture instantanée. Lorsque vous essayez de télécharger ou d'utiliser un instantané incrémentiel, tout le disque dur virtuel est utilisé. Cette nouvelle fonctionnalité de capture instantanée des disques managés peut améliorer leur rentabilité car il n'est plus nécessaire de stocker l'intégralité du disque avec chaque instantané, sauf si vous y tenez. Comme pour les instantanés classiques, les instantanés incrémentiels peuvent être utilisés pour créer un disque managé complet ou pour créer un instantané classique.

Il existe un certain nombre de différences entre un instantané incrémentiel et un instantané classique. Les instantanés incrémentiels utilisent toujours le stockage HDD Standard, quel que soit le type de stockage du disque, alors que les instantanés classiques peuvent utiliser des disques SSD premium. Si vous utilisez des instantanés classiques sur le Stockage Premium pour la montée en puissance des déploiements de machines virtuelles, nous vous recommandons d'utiliser des images personnalisées sur le stockage standard du service [Shared Image Gallery](../articles/virtual-machines/linux/shared-image-galleries.md). Cela vous aidera à atteindre une plus grande échelle à moindre coût. En outre, les instantanés incrémentiels offrent potentiellement une meilleure fiabilité avec un [stockage redondant interzone](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Si le stockage ZRS est disponible dans la région sélectionnée, l'instantané incrémentiel l'utilisera automatiquement. Si le stockage ZRS n'est pas disponible dans la région, l'instantané utilisera par défaut le [stockage localement redondant](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Vous pouvez remplacer ce comportement et sélectionner un autre type de stockage manuellement, mais nous vous le déconseillons.

Les instantanés incrémentiels offrent également une capacité différentielle, qui est uniquement disponible pour les disques managés. Ils vous permettent d'obtenir les modifications apportées entre deux instantanés incrémentiels des mêmes disques managés, jusqu'au niveau bloc. Vous pouvez utiliser cette fonctionnalité pour réduire l'empreinte de vos données lors de la copie d'instantanés d'une région à une autre.

Si vous ne vous êtes pas encore inscrit pour bénéficier de la préversion et que vous souhaitez commencer à utiliser les instantanés incrémentiels, envoyez-nous un e-mail à l'adresse AzureDisks@microsoft.com pour avoir accès à la préversion publique.

## <a name="restrictions"></a>Restrictions

- Il est actuellement impossible de créer des instantanés incrémentiels après avoir modifié la taille d'un disque.
- Les instantanés incrémentiels ne peuvent actuellement pas être transférés d'un abonnement à un autre.
- À un moment donné, vous ne pouvez actuellement générer que des URI SAS comprenant jusqu'à cinq instantanés d'une famille d'instantanés particulière.
- Vous ne pouvez pas créer d'instantané incrémentiel pour un disque particulier en dehors de l'abonnement associé à ce disque.
- Vous pouvez créer jusqu'à sept instantanés incrémentiels par disque toutes les cinq minutes.
- Au total, 200 instantanés incrémentiels peuvent être créés pour un même disque.

## <a name="powershell"></a>PowerShell

Vous pouvez utiliser Azure PowerShell pour créer un instantané incrémentiel. Vous pouvez installer la version la plus récente de PowerShell localement. Vous devez disposer de la dernière version d'Azure PowerShell. La commande suivante vous permettra de l'installer ou de mettre à jour votre installation existante :

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Une fois la dernière version installée, connectez-vous à votre session PowerShell à l'aide de la commande `az login`.

Remplacez `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` et `<yourDesiredSnapShotNameHere>` par vos valeurs. Vous pouvez ensuite utiliser le script suivant pour créer un instantané incrémentiel :

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Modèle Resource Manager

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

## <a name="cli"></a>Interface de ligne de commande

Vous pouvez créer un instantané incrémentiel à l'aide de l'interface de ligne de commande Azure (Azure CLI) en utilisant la commande [z snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Voici un exemple de commande :

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Vous pouvez également identifier les instantanés incrémentiels dans l'interface CLI en utilisant le paramètre `--query` dans la commande [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). Vous pouvez utiliser ce paramètre pour interroger directement les propriétés **SourceResourceId** et **SourceUniqueId** des instantanés. SourceResourceId est l'ID de ressource Azure Resource Manager du disque parent. **SourceUniqueId** est la valeur héritée de la propriété **UniqueId** du disque. Si vous supprimez un disque, puis créez un disque du même nom, la valeur de la propriété **UniqueId** change.

Voici quelques exemples de ces requêtes :

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne vous êtes pas encore inscrit pour bénéficier de la préversion et que vous souhaitez commencer à utiliser les instantanés incrémentiels, envoyez-nous un e-mail à l'adresse AzureDisks@microsoft.com pour avoir accès à la préversion publique.
