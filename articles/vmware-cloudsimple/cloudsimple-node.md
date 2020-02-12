---
title: Azure VMware Solutions (AVS) - Présentation des nœuds
description: Découvrez les nœuds et concepts AVS.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024923"
---
# <a name="avs-nodes-overview"></a>Présentation des nœuds AVS

Les nœuds sont les blocs de construction d'un cloud privé AVS. Un nœud est :

* Un hôte de calcul complet dédié sur lequel l'hyperviseur VMware ESXi est installé  
* Une unité de calcul que vous pouvez acheter ou réserver pour créer des clouds privés AVS
* Disponible à l'achat ou à la réservation dans une région où le service AVS est disponible

Vous créez un cloud privé AVS à partir des nœuds achetés. Pour créer un cloud privé AVS, il vous faut au moins trois nœuds de la même référence SKU. Pour développer un cloud privé AVS, ajoutez-y des nœuds. Vous pouvez ajouter des nœuds à un cluster existant ou créer un nouveau cluster en achetant des nœuds sur le Portail Azure et en les associant au service AVS. Tous les nœuds achetés sont visibles sous le service AVS. 

## <a name="provisioned-nodes"></a>Nœuds approvisionnés

Les nœuds approvisionnés peuvent faire l’objet d’un paiement à l’utilisation. L’approvisionnement de nœuds vous permet de procéder rapidement et à la demande à une mise à l’échelle de votre cluster VMware. Si nécessaire, vous pouvez ajouter des nœuds ou supprimer un nœud approvisionné pour faire descendre en puissance (scale down) votre cluster VMware. Les nœuds approvisionnés sont facturés au mois et imputés à l’abonnement dans lequel ils sont approvisionnés.

* Si vous payez votre abonnement Azure par carte de crédit, celle-ci est immédiatement débitée.
* Si vous réglez sur facture, les frais sont portés sur votre facture suivante.

## <a name="vmware-solution-by-avs-nodes-sku"></a>Références SKU des nœuds VMware Solution by AVS

Les types de nœuds suivants sont disponibles à l’approvisionnement ou à la réservation.

| SKU           | CS28 - Nœud                 | CS36 - Nœud                 | CS36m - Nœud                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Région        | USA Est, USA Ouest            | USA Est, USA Ouest            | Europe Ouest                 |
| UC           | 2 x 2,2 GHz, 28 cœurs (56 HT) | 2 x 2,3 GHz, 36 cœurs (72 HT) | 2 x 2,3 GHz, 36 cœurs (72 HT) |
| RAM           | 256 Go                      | 512 Go                      | 576 Go                      |
| Disque de cache    | NVMe de 1,6 To                 | NVMe de 3,2 To                 | NVMe de 3,2 To                 |
| Capacité de disque | 5,625 To de données brutes                | 11,25 To de données brutes                | 15,36 To de données brutes                |
| Type de stockage  | Flash                   | Flash                   | Flash                   |

## <a name="limits"></a>limites

Les limites de nœud suivantes s'appliquent aux clouds privés AVS.

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour la création d'un cloud privé AVS | 3 |
| Nombre maximal de nœuds dans un cluster situé sur un cloud privé AVS | 16 |
| Nombre maximal de nœuds dans un cloud privé AVS | 64 |
| Nombre minimal de nœuds sur un nouveau cluster | 3 |

## <a name="next-steps"></a>Étapes suivantes

* Apprenez à [acheter des nœuds](create-nodes.md)
* Découvrez-en plus sur le [cloud privé AVS](cloudsimple-private-cloud.md)
* Découvrez comment [approvisionner des nœuds](create-nodes.md)
* Découvrez-en plus sur le [Cloud privé](cloudsimple-private-cloud.md)
