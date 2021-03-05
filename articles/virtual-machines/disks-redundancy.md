---
title: Options de redondance pour les disques managés Azure
description: Apprenez-en davantage sur le stockage redondant interzone et le stockage localement redondant pour les disques managés Azure.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043641"
---
# <a name="redundancy-options-for-managed-disks"></a>Options de redondance pour les disques managés

Les disques managés Azure offrent deux options de redondance du stockage : le stockage redondant interzone (ZRS), proposé en préversion, et le stockage localement redondant. Le stockage ZRS offre une disponibilité plus élevée pour les disques managés que le stockage localement redondant (LRS). Toutefois, la latence d’écriture des disques LRS est meilleure que celle des disques ZRS. En effet, les disques LRS écrivent les données sur trois copies de manière synchrone dans un centre de données unique.

## <a name="locally-redundant-storage-for-managed-disks"></a>Stockage localement redondant pour les disques managés

Le stockage localement redondant (LRS) réplique vos données trois fois au sein d’un même centre de données dans la région sélectionnée. Il protège vos données contre les défaillances de disque et de rack du serveur. 

Il existe plusieurs façons de protéger votre application à l’aide de disques LRS en cas de défaillance d’une zone entière, ce qui peut se produire en raison d’une catastrophe naturelle ou de problèmes matériels :
- Utilisez une application comme SQL Server AlwaysOn, qui peut écrire des données de façon synchrone dans deux zones et basculer automatiquement vers une autre zone en cas d’incident.
- Effectuez des sauvegardes fréquentes des disques LRS avec des instantanés ZRS.
- Activez la reprise d’activité interzone pour les disques LRS avec [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Toutefois, la reprise d’activité interzone n’offre pas d’objectif de point de récupération (RPO) zéro.

Si votre workflow ne prend pas en charge les écritures synchrones au niveau de l’application entre les zones, ou si votre application doit respecter un RPO zéro, les disques ZRS représentent la solution idéale.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Stockage redondant interzone pour les disques managés (préversion)

Le stockage redondant interzone (ZRS) réplique votre disque managé Azure de façon synchrone dans trois zones de disponibilité Azure au sein de la région sélectionnée. Chaque zone de disponibilité est un emplacement physique distinct avec une alimentation, un refroidissement et une mise en réseau indépendants. 

Les disques ZRS assurent la reprise d’activité après sinistre dans les zones de disponibilité. En cas de défaillance d’une zone entière, un disque ZRS peut être attaché à une machine virtuelle dans une autre zone. Vous pouvez également utiliser des disques ZRS comme disque partagé afin d’améliorer la disponibilité des applications en cluster ou distribuées comme SQL FCI, SAP ASCS/SCS ou GFS2. Vous pouvez attacher un disque ZRS partagé à des machines virtuelles principales et secondaires dans des zones différentes pour tirer parti à la fois du stockage ZRS et des [zones de disponibilité](../availability-zones/az-overview.md). En cas de défaillance de la zone principale, vous pouvez rapidement basculer vers la machine virtuelle secondaire à l’aide de la [réservation persistante SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Limites

Dans le cadre de la préversion, le stockage ZRS pour les disques managés présente les restrictions suivantes :

- Il est pris en charge uniquement avec des disques SSD Premium et Standard.
- Pour le moment, il est disponible uniquement dans la région EastUS2EUAP.
- Les disques ZRS ne peuvent être créés qu’avec des modèles Azure Resource Manager à l’aide de l’API `2020-12-01`.

Inscrivez-vous à la préversion [ici](https://aka.ms/ZRSDisksPreviewSignUp).

### <a name="billing-implications"></a>Implications de facturation

Pour plus d’informations, consultez la [page des tarifs Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Comparaison avec d’autres types de disques

Les disques utilisant le stockage ZRS sont identiques aux disques utilisant le stockage LRS sauf en ce qui concerne la latence d’écriture. Ils présentent les mêmes cibles de performances.

### <a name="create-zrs-managed-disks"></a>Créer des disques managés ZRS

Utilisez l’API `2020-12-01` avec votre modèle Azure Resource Manager pour créer un disque ZRS.

#### <a name="create-a-vm-with-zrs-disks"></a>Créer une machine virtuelle avec des disques ZRS

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Créer des machines virtuelles avec un disque ZRS partagé attaché aux machines virtuelles de différentes zones

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Créer un groupe de machines virtuelles identiques avec des disques ZRS

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Étapes suivantes

- Utilisez ces exemples de [modèles Azure Resource Manager pour créer une machine virtuelle avec des disques ZRS](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).