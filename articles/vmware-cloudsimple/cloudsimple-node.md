---
title: Vue d’ensemble de nœuds pour la Solution VMware par CloudSimple - Azure
description: Découvrez les concepts et les nœuds CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577359"
---
# <a name="cloudsimple-nodes-overview"></a>Vue d’ensemble de nœuds CloudSimple

Un nœud est :

* Un système nu dédié calcul hôte où VMware ESXi hyperviseur est installé  
* Une unité de calcul que vous pouvez acheter ou de réserve pour créer des nuages privés  
* Disponible à l’achat ou réserver dans une région où le service CloudSimple est disponible

Les nœuds sont des blocs de construction d’un cloud privé.  Pour créer un cloud privé, vous avez besoin d’un minimum de trois nœuds de la même référence SKU.  Pour développer un cloud privé, ajouter des nœuds supplémentaires.  Vous pouvez ajouter des nœuds à un cluster existant. Ou bien, vous pouvez créer un nouveau cluster en achetant des nœuds dans le portail Azure et leur association avec le service CloudSimple.  Tous les nœuds achetés sont visibles sous le service CloudSimple.  Vous créez un cloud privé à partir des nœuds sur le portail CloudSimple achetées.

## <a name="purchased-nodes"></a>Nœuds achetées

Nœuds achetées fournissent une capacité paiement à l’utilisation. Nœuds d’achat vous permet de rapidement mettre à l’échelle votre cluster VMware à la demande. Vous pouvez ajouter des nœuds en fonction des besoins, ou supprimer un nœud acheté à l’échelle votre cluster VMware. Nœuds achetées sont facturés sur une base mensuelle et facturés à l’abonnement dans lequel elles sont achetées :

* Si vous payez par carte de crédit pour votre abonnement Azure, il se peut que la carte est facturée immédiatement.
* Si vous êtes facturé par facture, les frais s’affichent sur votre prochaine facture.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>Solution de VMware par nœuds CloudSimple référence (SKU)

Les types de nœuds suivants sont disponibles pour l’achat ou de la réservation.

| SKU | CS28 - nœud | CS36 - Node |
|-----|-------------|-------------|
| UC | 2x2.2 GHz, 28 cœurs (56 HT) | 2x2.3 GHz, 36 cœurs (72 HT) |
| RAM | 256 Go | 512 Go |
| Cache disque |  1,6 To NVMe | 3.2-To NVMe |
| Capacité disque | 5.625 To bruts | 11,25 To bruts |
| Type de stockage | Flash | Flash |

## <a name="limits"></a>limites

Les limites de nœud suivantes s’appliquent à des clouds privés.

| Ressource | Limite |
|----------|-------|
| Nombre minimal de nœuds pour créer un cloud privé | 3 |
| Nombre maximal de nœuds dans un Cluster sur un cloud privé | 16 |
| Nombre maximal de nœuds dans un cloud privé | 64 |
| Nombre minimal de nœuds sur un nouveau Cluster | 3 |

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [acheter des nœuds](create-nodes.md)
* En savoir plus sur [Cloud privé](cloudsimple-private-cloud.md)