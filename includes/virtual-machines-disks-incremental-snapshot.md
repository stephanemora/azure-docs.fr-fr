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
ms.openlocfilehash: a0325a7fd3aca3d27b24c193a9f131546a70d80b
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566251"
---
Les instantanés incrémentiels (préversion) sont des sauvegardes des disques managés exécutées à un moment donné et contenant uniquement les modifications apportées depuis la dernière capture instantanée. Lorsque vous essayez de télécharger ou d'utiliser un instantané incrémentiel, tout le disque dur virtuel est utilisé. Cette nouvelle fonctionnalité de capture instantanée des disques managés peut améliorer leur rentabilité car il n'est plus nécessaire de stocker l'intégralité du disque avec chaque instantané, sauf si vous y tenez. Comme pour les instantanés classiques, les instantanés incrémentiels peuvent être utilisés pour créer un disque managé complet ou pour créer un instantané classique.

Il existe un certain nombre de différences entre un instantané incrémentiel et un instantané classique. Les instantanés incrémentiels utilisent toujours le stockage HDD Standard, quel que soit le type de stockage du disque, alors que les instantanés classiques peuvent utiliser des disques SSD premium. Si vous utilisez des instantanés classiques sur le Stockage Premium pour effectuer un scale-up de déploiements de machines virtuelles, nous vous recommandons d'utiliser des images personnalisées sur le stockage standard dans [Shared Image Gallery](../articles/virtual-machines/linux/shared-image-galleries.md). Cela vous aidera à atteindre une plus grande échelle à moindre coût. En outre, les instantanés incrémentiels offrent potentiellement une meilleure fiabilité avec un [stockage redondant interzone](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Si le stockage ZRS est disponible dans la région sélectionnée, l'instantané incrémentiel l'utilisera automatiquement. Si le stockage ZRS n'est pas disponible dans la région, l'instantané utilisera par défaut le [stockage localement redondant](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Vous pouvez remplacer ce comportement et sélectionner un autre type de stockage manuellement, mais nous vous le déconseillons.

Les instantanés incrémentiels offrent également une capacité différentielle, qui est uniquement disponible pour les disques managés. Ils vous permettent d'obtenir les modifications apportées entre deux instantanés incrémentiels des mêmes disques managés, jusqu'au niveau bloc. Vous pouvez utiliser cette fonctionnalité pour réduire l'empreinte de vos données lors de la copie d'instantanés d'une région à une autre.

Si vous ne vous êtes pas encore inscrit pour bénéficier de la préversion et que vous souhaitez commencer à utiliser des instantanés incrémentiels, envoyez-nous un e-mail à l’adresse AzureDisks@microsoft.com pour accéder à la préversion publique.

## <a name="restrictions"></a>Restrictions

- Les instantanés incrémentiels ne sont à l’heure actuelle disponibles que dans les régions USA Est, USA Centre, Canada Centre, USA Centre-Ouest et Europe Nord.
- Il est actuellement impossible de créer des instantanés incrémentiels après avoir modifié la taille d'un disque.
- Les instantanés incrémentiels ne peuvent actuellement pas être transférés d'un abonnement à un autre.
- À un moment donné, vous ne pouvez actuellement générer que des URI SAS comprenant jusqu'à cinq instantanés d'une famille d'instantanés particulière.
- Vous ne pouvez pas créer d'instantané incrémentiel pour un disque particulier en dehors de l'abonnement associé à ce disque.
- Vous pouvez créer jusqu'à sept instantanés incrémentiels par disque toutes les cinq minutes.
- Au total, 200 instantanés incrémentiels peuvent être créés pour un même disque.

## <a name="powershell"></a>PowerShell

Vous pouvez utiliser Azure PowerShell pour créer un instantané incrémentiel. Vous devez disposer de la dernière version d'Azure PowerShell. La commande suivante vous permettra de l'installer ou de mettre à jour votre installation existante :

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Une fois la dernière version installée, connectez-vous à votre session PowerShell à l'aide de la commande `az login`.

Pour créer un instantané incrémentiel avec Azure PowerShell, définissez la configuration avec [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) avec le paramètre `-Incremental`, puis passez-la en tant que variable à [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) par le biais du paramètre `-Snapshot`.

Remplacez `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` et `<yourDesiredSnapShotNameHere>` par vos valeurs. Vous pouvez ensuite utiliser le script suivant pour créer un instantané incrémentiel :

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Vous pouvez identifier des instantanés incrémentiels à partir du même disque avec les propriétés `SourceResourceId` et `SourceUniqueId` des instantanés. `SourceResourceId` est l’ID de ressource Azure Resource Manager du disque parent. `SourceUniqueId` est la valeur héritée de la propriété `UniqueId` du disque. Si vous supprimez un disque et que vous créez ensuite un disque portant le même nom, la valeur de la propriété `UniqueId` change.

Vous pouvez utiliser `SourceResourceId` et `SourceUniqueId` pour créer une liste de tous les instantanés associés à un disque particulier. Remplacez `<yourResourceGroupNameHere>` par votre valeur, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants :

```PowerShell
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

## <a name="cli"></a>Interface de ligne de commande

Vous pouvez créer un instantané incrémentiel avec Azure CLI (dans ce cas, il vous faut la dernière version d’Azure CLI). 

Sur Windows, la commande suivante vous permet de l’installer ou de mettre à jour votre installation existante avec la dernière version :
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Sur Linux, l’installation de l’interface de ligne de commande varie en fonction de la version du système d’exploitation.  Consultez [Installer l’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) pour votre version Linux spécifique.

Pour créer un instantané incrémentiel, utilisez [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) avec le paramètre `--incremental`.

L’exemple suivant crée un instantané incrémentiel. Remplacez `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`, `<exampleDiskName>` et `<exampleLocation>` par vos propres valeurs, puis exécutez l’exemple :

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Vous pouvez identifier des instantanés incrémentiels à partir du même disque avec les propriétés `SourceResourceId` et `SourceUniqueId` des instantanés. `SourceResourceId` est l’ID de ressource Azure Resource Manager du disque parent. `SourceUniqueId` est la valeur héritée de la propriété `UniqueId` du disque. Si vous supprimez un disque et que vous créez ensuite un disque portant le même nom, la valeur de la propriété `UniqueId` change.

Vous pouvez utiliser `SourceResourceId` et `SourceUniqueId` pour créer une liste de tous les instantanés associés à un disque particulier. L’exemple suivant liste tous les instantanés incrémentiels associés à un disque particulier, mais il nécessite une certaine configuration.

Cet exemple utilise jq pour interroger les données. Pour exécuter l’exemple, vous devez [installer jq](https://stedolan.github.io/jq/download/).

Remplacez `<yourResourceGroupNameHere>` et `<exampleDiskName>` par vos valeurs, puis utilisez l’exemple suivant pour lister vos instantanés incrémentiels existants (à condition que jq soit aussi installé) :

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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

## <a name="next-steps"></a>Étapes suivantes

1. Si vous ne vous êtes pas encore inscrit pour bénéficier de la préversion et que vous souhaitez commencer à utiliser des instantanés incrémentiels, envoyez-nous un e-mail à l’adresse AzureDisks@microsoft.com pour accéder à la préversion publique. 

2. Explorez les exemples suivants pour la copie entre régions des instantanés incrémentiels à l’aide de la fonctionnalité différentielle   

    - [Utilisation des kits SDK Azure .NET](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
