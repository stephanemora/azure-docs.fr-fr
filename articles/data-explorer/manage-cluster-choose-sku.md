---
title: Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer
description: Cet article explique comment sélectionner la taille optimale de référence SKU pour le cluster Azure Data Explorer.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226146"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer 

Azure Data Explorer propose plusieurs références SKU de machine virtuelle pour la création d'un cluster ou l'optimisation du cluster avec une charge de travail changeante. Les machines virtuelles ont été soigneusement choisies pour permettre le coût le plus optimal pour n'importe quelle charge de travail. 

La taille et la référence SKU d’une machine virtuelle du cluster de gestion des données sont entièrement gérées par le service Azure Data Explorer. Elle est déterminée par des facteurs tels que la taille de la machine virtuelle du moteur et la charge de travail d'ingestion. 

La référence SKU de machine virtuelle du cluster moteur peut être modifiée à tout moment en [mettant à l'échelle le cluster](manage-cluster-vertical-scaling.md). Par conséquent, il est préférable de commencer par la taille minimale de référence SKU correspondant au scénario initial. Gardez à l'esprit que la mise à l'échelle du cluster entraîne un temps d'arrêt pouvant atteindre 30 minutes lorsque le cluster est recréé avec la nouvelle référence SKU de machine virtuelle.

> [!TIP]
> Le calcul des [RI (instances réservées)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) est applicable au cluster Azure Data Explorer.  

Cet article décrit les différentes options de référence SKU de machine virtuelle et fournit des détails techniques qui peuvent vous aider à faire le meilleur choix.

## <a name="select-the-cluster-type"></a>Sélectionner le type de cluster

Azure Data Explorer offre deux types de clusters :

* **Production** : Les clusters de production contiennent deux nœuds pour les clusters de moteur et de gestion de données et sont utilisés conformément au [contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) Azure Data Explorer.

* **Développement/test (aucun SLA)**  : Les clusters de développement/test ne comportent qu’un seul nœud D11_v2 pour le cluster moteur et un seul nœud D1 pour le cluster de gestion des données. Ce type de cluster représente la configuration la moins chère en raison du faible nombre d'instances et de l'absence de frais de balisage moteur. Il n'existe pas de SLA pour cette configuration de cluster car elle manque de redondance.

## <a name="sku-types"></a>Types de référence SKU

Lors de la création d'un cluster Azure Data Explorer, sélectionnez la référence SKU de machine virtuelle *optimale* pour la charge de travail planifiée. Azure Data Explorer propose deux familles de références SKU :

* **D_V2** : La référence SKU **D** est optimisée pour le calcul et disponible en deux versions.
    * Machine virtuelle seule
    * Machine virtuelle regroupée avec des disques de stockage Premium

* **LS** : La référence SKU **L** est optimisée pour le stockage. Pour le même prix, elle propose un disque SSD beaucoup plus grand que la référence SKU **D**.

Le tableau suivant présente les principales différences entre les types de référence SKU disponibles :
 
|**Attribut** | **Référence SKU D** | **Référence SKU L**
|---|---|---
|**Petites références SKU**|La taille minimale est 'D11' avec deux cœurs|La taille minimale est 'L4' avec quatre cœurs
|**Disponibilité**|Disponible dans toutes les régions (la version DS+PS offre une disponibilité plus limitée)|Disponible dans quelques régions
|**Coût par Go de cache par cœur**|Elevé avec la référence SKU D, faible avec la version DS+PS|Moins cher avec l'option *Paiement à l’utilisation*
|**Prix des RI (instances réservées)**|Remise élevée (plus de 55 % pour un engagement sur trois ans)|Remise plus petite (20 % pour un engagement sur trois ans)  

## <a name="select-your-cluster-vm"></a>Sélectionner votre machine virtuelle de cluster 

Pour sélectionner votre machine virtuelle de cluster, [configurez une mise à l'échelle verticale](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Les différentes options de référence SKU de machine virtuelle vous permettent d'optimiser les coûts en fonction des performances nécessaires et des exigences de cache à chaud pour le scénario souhaité. Si le scénario exige des performances optimales pour un volume de requêtes élevé, la référence SKU idéale doit être optimisée pour le calcul. D'autre part, si le scénario exige d'interroger de grands volumes de données avec une charge de requêtes relativement plus faible, la référence SKU optimisée pour le stockage réduira les coûts tout en offrant d'excellentes performances.

Le nombre d'instances par cluster pour les petites références SKU étant limité, il est préférable d'utiliser des machines virtuelles de plus grande taille avec une plus grande RAM. La taille de la RAM est nécessaire pour certains types de requêtes qui exigent plus de ressources RAM, notamment celles qui utilisent `joins`. Par conséquent, lorsque vous étudiez les options de mise à l'échelle, il est conseillé de monter en puissance une référence SKU plus grande plutôt que d’ajouter plus d'instances pour monter en charge.

## <a name="vm-options"></a>Options de la machine virtuelle

Le tableau suivant fournit les spécifications techniques des machines virtuelles du cluster Azure Data Explorer :

|**Nom**| **Catégorie** | **Taille du disque SSD** | **Cœurs** | **RAM** | **Disques de stockage Premium (1 To)**| **Nombre minimum d'instances par cluster** | **Nombre maximum d'instances par cluster**
|---|---|---|---|---|---|---|---
|D11_v2| optimisé pour le calcul | 75 Go    | 2 | 14 Go | 0 | 1 | 8 (sauf pour les références SKU de développement/test où il est de 1)
|D12_v2| optimisé pour le calcul | 150 Go   | 4 | 28 Go | 0 | 2 | 16
|D13_v2| optimisé pour le calcul | 307 Go   | 8 | 56 Go | 0 | 2 | 1 000
|D14_v2| optimisé pour le calcul | 614 Go   | 16| 112 Go | 0 | 2 | 1 000
|DS13_v2 + 1 To PS| optimisé pour le stockage | 1 To | 8 | 56 Go | 1 | 2 | 1 000
|DS13_v2 + 2 To PS| optimisé pour le stockage | 2 To | 8 | 56 Go | 2 | 2 | 1 000
|DS14_v2 + 3 To PS| optimisé pour le stockage | 3 To | 16 | 112 Go | 2 | 2 | 1 000
|DS14_v2 + 4 To PS| optimisé pour le stockage | 4 To | 16 | 112 Go | 4 | 2 | 1 000
|L4s_v1| optimisé pour le stockage | 650 Go | 4 | 32 Go | 0 | 2 | 16
|L8s_v1| optimisé pour le stockage | 1,3 To | 8 | 64 Go | 0 | 2 | 1 000
|L16s_1| optimisé pour le stockage | 2,6 To | 16| 128 Go | 0 | 2 | 1 000

* Visualisez la liste mise à jour des références SKU de machine virtuelle par région à l'aide de l'[API ListSkus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) Azure Data Explorer. 
* En savoir plus sur les [différentes références SKU de calcul](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez à tout moment [monter ou descendre en puissance](manage-cluster-vertical-scaling.md) le cluster moteur en changeant la référence SKU de machine virtuelle pour différents besoins. 

* La taille du cluster moteur peut être [diminuée ou augmentée](manage-cluster-horizontal-scaling.md) pour modifier sa capacité en fonction de l'évolution de la demande.

