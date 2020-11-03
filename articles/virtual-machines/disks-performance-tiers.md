---
title: Modifier les performances des disques managés Azure
description: Découvrez les niveaux de performances pour les disques managés et apprenez à modifier les niveaux de performances des disques managés existants.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 404f435e321e53694807a627121d84f6cbf6724d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359677"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Niveaux de performance des disques managés (préversion)

La fonctionnalité Stockage sur disque Azure offre actuellement des fonctionnalités de bursting intégrées afin d’améliorer les performances pour la gestion du trafic inattendu à court terme. Les disques SSD Premium offrent la possibilité d’augmenter les performances du disque sans augmenter sa taille réelle. Cette fonctionnalité vous permet de répondre aux besoins en performances de votre charge de travail et de réduire les coûts. 

> [!NOTE]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. 

Cette fonctionnalité est idéale pour les événements qui nécessitent temporairement un niveau de performances systématiquement plus élevé, comme les achats pendant les fêtes, les tests de performance ou l’exécution d’un environnement d’entraînement. Pour gérer ces événements, vous pouvez utiliser un niveau de performance plus élevé aussi longtemps que vous en avez besoin. Vous pouvez ensuite revenir au niveau d’origine lorsque vous n’avez plus besoin de performances supplémentaires.

## <a name="how-it-works"></a>Fonctionnement

Quand vous déployez ou provisionnez un disque pour la première fois, le niveau de performance de base de référence pour ce disque est défini en fonction de la taille de disque provisionnée. Vous pouvez utiliser un niveau de performances supérieur pour répondre à une demande plus élevée. Lorsque vous n’avez plus besoin de ce niveau de performance, vous pouvez revenir au niveau de performance initial.

Votre facturation change à mesure que votre niveau change. Par exemple, si vous provisionnez un disque P10 (128 Gio), votre niveau de performance de base de référence est défini comme P10 (500 IOPS et 100 Mo/s). Vous êtes facturé au tarif P10. Vous pouvez augmenter le niveau pour qu’il corresponde aux performances P50 (7 500 E/S par seconde et 250 Mbits/s) sans que la taille du disque soit augmentée. Au moment de la mise à niveau, vous êtes facturé au tarif P50. Lorsque vous n’avez plus besoin de performances supérieures, vous pouvez revenir au niveau P10. Le disque sera à nouveau facturé au tarif P10.

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

Pour plus d’informations sur la facturation, consultez [Tarification des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrictions

- Cette fonctionnalité est actuellement prise en charge uniquement pour les disques SSD Premium.
- Vous devez libérer votre machine virtuelle ou détacher votre disque d'une machine virtuelle en cours d'exécution avant de pouvoir modifier le niveau du disque.
- L’utilisation des niveaux de performance P60, P70 et P80 est limitée aux disques de 4 096 Gio ou plus.
- Le niveau de performance d'un disque ne peut être rétrogradé qu'une fois toutes les 24 heures.

## <a name="regional-availability"></a>Disponibilité régionale

La possibilité d’ajuster le niveau de performance d’un disque géré est actuellement disponible uniquement sur les disques SSD Premium dans les régions USA Est 2, USA Centre Sud, USA Centre-Ouest et Sud-Est de l’Australie.

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Créez un disque de données vide avec un niveau supérieur au niveau de base de référence

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
     
## <a name="update-the-tier-of-a-disk"></a>Mettre à jour le niveau d’un disque

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Afficher le niveau d’un disque

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin de redimensionner un disque pour tirer parti de niveaux de performance supérieurs, consultez les articles suivants :

- [Étendre des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI](linux/expand-disks.md)
- [Développer un disque managé attaché à une machine virtuelle Windows](windows/expand-os-disk.md)
