---
title: Modifier les performances des disques managés Azure – CLI/PowerShell
description: Découvrez comment modifier les niveaux de performances des disques managés existants à l’aide du module Azure PowerShell ou de l’interface Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6d13f5927e31fc7f8cf412f6bba6088360af610d
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356218"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Changer votre niveau de performance à l’aide du module Azure PowerShell ou de l’interface Azure CLI

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Créez un disque de données vide avec un niveau supérieur au niveau de base de référence

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Créer un disque de système d’exploitation avec un niveau supérieur au niveau de base d’une image de la Place de marché Azure

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>Mettre à jour le niveau d’un disque

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>Afficher le niveau d’un disque

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>Modifier le niveau de performance d’un disque sans temps d’arrêt (préversion)

Vous pouvez également modifier votre niveau de performance sans temps d’arrêt. Ainsi, vous n’avez pas besoin de libérer votre machine virtuelle ou de détacher votre disque pour modifier le niveau.

### <a name="prerequisites"></a>Prérequis

Votre disque doit répondre aux exigences énoncées dans la section [Modifier le niveau de performance sans temps d'arrêt (préversion)](#change-performance-tier-without-downtime-preview). Si ce n'est pas le cas, la modification du niveau de performance entraînera un temps d'arrêt.

Vous devez activer la fonctionnalité pour votre abonnement avant de pouvoir modifier le niveau de performance d'un disque sans temps d'arrêt. Suivez les étapes ci-dessous pour activer la fonctionnalité pour votre abonnement :

1.  Exécutez la commande suivante pour inscrire la fonctionnalité pour votre abonnement

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  Vérifiez que l'état de l'inscription est **Inscrit** (cela peut prendre quelques minutes) à l'aide de la commande suivante avant d'essayer la fonctionnalité.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="update-the-performance-tier-of-a-disk-without-downtime-via-azure-cli"></a>Mettre à jour le niveau de performance d'un disque sans temps d'arrêt via Azure CLI

Le script suivant remplace le niveau d'un disque par un niveau supérieur au niveau de base. Remplacez `<yourResourceGroupNameHere>`, `<yourDiskNameHere>` et `<yourDesiredPerformanceTier>`, puis exécutez le script :

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
 
az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

### <a name="update-the-performance-tier-of-a-disk-without-downtime-via-arm-template"></a>Mettre à jour le niveau de performance d'un disque sans temps d'arrêt via un modèle ARM

Le script suivant met à jour le niveau d’un disque à un niveau supérieur au niveau de base à l’aide de l’exemple de modèle [CreateUpdateDataDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json). Remplacez `<yourSubScriptionID>`, `<yourResourceGroupName>`, `<yourDiskName>`, `<yourDiskSize>` et `<yourDesiredPerformanceTier>`, puis exécutez le script :

 ```cli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
diskName=<yourDiskName>
diskSize=<yourDiskSize>
performanceTier=<yourDesiredPerformanceTier>
region=EastUS2EUAP

 az login

 az account set --subscription $subscriptionId

 az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
--parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
```

## <a name="confirm-your-disk-has-changed-tiers"></a>Vérifier que le niveau de votre disque a changé

La modification d’un niveau de performance peut prendre jusqu’à 15 minutes. Pour vérifier que le niveau de votre disque a changé, utilisez l'une des méthodes suivantes :

### <a name="azure-resource-manager"></a>Azure Resource Manager

```cli
az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-12-01 --query [properties.tier] -o tsv
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de redimensionner un disque pour tirer parti de niveaux de performance supérieurs, consultez les articles suivants :

- [Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](linux/expand-disks.md)
- [Développer un disque managé attaché à une machine virtuelle Windows](windows/expand-os-disk.md)
