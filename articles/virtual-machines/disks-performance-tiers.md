---
title: Améliorer les performances des disques managés Azure
description: Découvrez les niveaux de performance des disques managés et comment les mettre à niveau.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974126"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Niveaux de performance des disques managés (préversion)

La fonctionnalité Stockage sur disque Azure offre actuellement des fonctionnalités de bursting intégrées afin d’améliorer les performances pour la gestion du trafic inattendu à court terme. Les disques SSD Premium offrent la possibilité d’améliorer les performances des disques sans augmenter la taille réelle du disque, ce qui vous permet de répondre aux besoins en termes de performances de votre charge de travail et de réduire les coûts. Cette fonctionnalité est actuellement en préversion. C’est idéal pour les événements qui nécessitent temporairement un niveau de performances systématiquement plus élevé, comme les achats pendant les fêtes, les tests de performances ou l’exécution d’un environnement d’entraînement. Pour gérer ces événements, vous pouvez sélectionner un niveau de performance supérieur aussi longtemps que nécessaire, puis revenir au niveau d’origine quand les performances supplémentaires ne sont plus requises.

## <a name="how-it-works"></a>Fonctionnement

Quand vous déployez ou provisionnez un disque pour la première fois, le niveau de performance de base de référence pour ce disque est défini en fonction de la taille de disque provisionnée. Vous pouvez sélectionner un niveau de performance supérieur pour répondre à une demande plus élevée et, quand ce niveau n’est plus nécessaire, vous pouvez revenir au niveau de performance de base de référence initial. Par exemple, si vous provisionnez un disque P10 (128 Gio), votre niveau de performance de base de référence est défini comme P10 (500 IOPS et 100 Mo/s). Vous pouvez mettre à jour le niveau de façon à ce qu’il corresponde aux performances de P50 (7 500 IOPS et 250 Mo/s) sans augmenter la taille du disque et revenir à P10 quand le niveau supérieur n’est plus nécessaire.

| Taille du disque | Niveau de performance de base de référence | Mise à niveau possible vers |
|----------------|-----|-------------------------------------|
| 4 Gio | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 Gio | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 Gio | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 Gio | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 Gio | P6 | P10, P15, P20, P30, P40, P50 |
| 128 Go | P10 | P15, P20, P30, P40, P50 |
| 256 Gio | P15 | P20, P30, P40, P50 |
| 512 Go | P20 | P30, P40, P50 |
| 1 Tio | P30 | P40, P50 |
| 2 Tio | P40 | P50 |
| 4 Tio | P50 | Aucun |
| 8 Tio | P60 |  P70, P80 |
| 16 Tio | P70 | P80 |
| 32 Tio | P80 | Aucun |

## <a name="restrictions"></a>Restrictions

- Prise en charge actuelle uniquement pour les disques SSD Premium.
- Les disques doivent être détachés d’une machine virtuelle en cours d’exécution avant de changer de niveau.
- L’utilisation des niveaux de performance P60, P70 et P80 est limitée aux disques de 4 096 Gio ou plus.

## <a name="regional-availability"></a>Disponibilité régionale

L’ajustement du niveau de performance d’un disque managé est actuellement uniquement disponible pour les disques SSD Premium dans les régions suivantes :

- Centre-USA Ouest 
- USA Est 2 
- Europe occidentale
- Australie Est 
- Australie du Sud-Est 
- Inde Sud

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Créer/mettre à jour un disque de données avec un niveau supérieur au niveau de base de référence

1. Créer un disque de données vide avec un niveau supérieur au niveau de base de référence ou mettre à jour le niveau d’un disque supérieur au niveau de base de référence à l’aide de l’exemple de modèle [CreateUpdateDataDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Confirmer le niveau du disque

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Créer/mettre à jour un disque de système d’exploitation avec un niveau supérieur au niveau de base de référence

1. Créer un disque de système d’exploitation à partir d’une image de la Place de marché ou mettre à jour le niveau d’un disque de système d’exploitation supérieur au niveau de base de référence à l’aide de l’exemple de modèle [CreateUpdateOSDiskWithTier.json](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Confirmer le niveau du disque
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Étapes suivantes

Si vous devez redimensionner un disque pour bénéficier des niveaux de performance plus importants, consultez nos articles sur le sujet :

- [Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](linux/expand-disks.md)
- [Développer un disque managé attaché à une machine virtuelle Windows](windows/expand-os-disk.md)