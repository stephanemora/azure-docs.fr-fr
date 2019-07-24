---
title: Vue d'ensemble des nœuds pour la solution VMware de CloudSimple - Azure
description: Découvrez les nœuds et concepts CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165803"
---
# <a name="cloudsimple-nodes-overview"></a>Vue d'ensemble des nœuds CloudSimple

Un nœud est :

* Un hôte de calcul complet dédié sur lequel l'hyperviseur VMware ESXi est installé  
* Une unité de calcul que vous pouvez approvisionner ou réserver pour créer des clouds privés  
* Disponible à l’approvisionnement ou à la réservation dans une région où le service CloudSimple est disponible

Les nœuds sont les blocs de construction d'un cloud privé.  Pour créer un cloud privé, il vous faut au moins trois nœuds de la même référence SKU.  Pour développer un cloud privé, ajoutez-y des nœuds.  Vous pouvez ajouter des nœuds à un cluster existant. Vous pouvez également créer un nouveau cluster en approvisionnant des nœuds sur le portail Azure et en les associant au service CloudSimple.  Tous les nœuds approvisionnés sont visibles sous le service CloudSimple.  Vous créez un cloud privé à partir des nœuds approvisionnés sur le portail CloudSimple.

## <a name="provisioned-nodes"></a>Nœuds approvisionnés

Les nœuds approvisionnés peuvent faire l’objet d’un paiement à l’utilisation. L’approvisionnement de nœuds vous permet de procéder rapidement et à la demande à une mise à l’échelle de votre cluster VMware. Si nécessaire, vous pouvez ajouter des nœuds ou supprimer un nœud approvisionné pour faire descendre en puissance votre cluster VMware. Les nœuds approvisionnés sont facturés sur une base mensuelle et imputés à l’abonnement avec lequel ils sont approvisionnés :

* Si vous payez votre abonnement Azure par carte de crédit, celle-ci est immédiatement débitée.
* Si vous réglez sur facture, les frais sont portés sur votre facture suivante.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Références SKU de nœuds pour solution VMware de CloudSimple

Les types de nœuds suivants sont disponibles à l’approvisionnement ou à la réservation.

| SKU | CS28 - Nœud | CS36 - Nœud |
|-----|-------------|-------------|
| UC | 2 x 2,2 GHz, 28 cœurs (56 HT) | 2 x 2,3 GHz, 36 cœurs (72 HT) |
| RAM | 256 Go | 512 Go |
| Disque de cache |  NVMe de 1,6 To | NVMe de 3,2 To |
| Capacité de disque | 5,625 To de données brutes | 11,25 To de données brutes |
| Type de stockage | Flash | Flash |

## <a name="limits"></a>limites

Les limites de nœud suivantes s'appliquent aux clouds privés.

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour la création d'un cloud privé | 3 |
| Nombre maximal de nœuds dans un cluster sur un cloud privé | 16 |
| Nombre maximal de nœuds dans un cloud privé | 64 |
| Nombre minimal de nœuds sur un nouveau cluster | 3 |

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [approvisionner des nœuds](create-nodes.md)
* Découvrez-en plus sur le [Cloud privé](cloudsimple-private-cloud.md)