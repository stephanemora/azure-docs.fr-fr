---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008332"
---
## <a name="limitations"></a>Limites

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tailles du disque

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Déployer des disques partagés

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Déployer un disque SSD Premium en tant que disque partagé

Pour déployer un disque managé avec la fonctionnalité de disque partagé activée, utilisez la nouvelle propriété `maxShares` et définissez une valeur supérieure à 1. Cela permet de partager le disque sur plusieurs machines virtuelles.

> [!IMPORTANT]
> La valeur de `maxShares` peut être définie ou modifiée uniquement lorsqu’un disque est démonté de toutes les machines virtuelles. Consultez les [tailles de disque](#disk-sizes) afin de connaître les valeurs autorisées pour `maxShares`.

Avant d’utiliser le modèle suivant, remplacez `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]` et `[parameters('maxShares')]` par vos propres valeurs.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Déployer un disque Ultra en tant que disque partagé

#### <a name="cli"></a>Interface de ligne de commande

Pour déployer un disque managé avec la fonctionnalité de disque partagé activée, définissez le paramètre `maxShares` sur une valeur supérieure à 1. Cela permet de partager le disque sur plusieurs machines virtuelles.

> [!IMPORTANT]
> La valeur de `maxShares` peut être définie ou modifiée uniquement lorsqu’un disque est démonté de toutes les machines virtuelles. Consultez les [tailles de disque](#disk-sizes) afin de connaître les valeurs autorisées pour `maxShares`.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Pour déployer un disque managé avec la fonctionnalité de disque partagé activée, utilisez la propriété `maxShares` et définissez une valeur supérieure à 1. Cela permet de partager le disque sur plusieurs machines virtuelles.

> [!IMPORTANT]
> La valeur de `maxShares` peut être définie ou modifiée uniquement lorsqu’un disque est démonté de toutes les machines virtuelles. Consultez les [tailles de disque](#disk-sizes) afin de connaître les valeurs autorisées pour `maxShares`.

Avant d’utiliser le modèle suivant, remplacez `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` et `[parameters('diskMBpsReadOnly')]` par vos propres valeurs.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Utilisation de disques partagés Azure avec vos machines virtuelles

Une fois que vous avez déployé un disque partagé avec `maxShares>1`, vous pouvez monter le disque sur une ou plusieurs de vos machines virtuelles.

> [!IMPORTANT]
> Toutes les machines virtuelles partageant un disque doivent être déployées dans le même [groupe de placement de proximité](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Commandes PR SCSI prises en charge

Une fois que vous avez monté le disque partagé sur vos machines virtuelles dans votre cluster, vous pouvez établir un quorum et lire/écrire sur le disque à l’aide de demandes de tirage (pull requests) SCSI. Les commandes PR suivantes sont disponibles lors de l’utilisation de disques partagés Azure :

Pour interagir avec le disque, démarrez avec la liste persistent-reservation-action :

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Lorsque vous utilisez PR_RESERVE, PR_PREEMPT_RESERVATION ou PR_RELEASE_RESERVATION, fournissez l’un des persistent-reservation-type suivants :

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Vous devez également fournir une clé persistent-reservation-key lorsque vous utilisez PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION ou PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer la fonctionnalité Disques partagés, [inscrivez-vous à la préversion](https://aka.ms/AzureSharedDiskPreviewSignUp).