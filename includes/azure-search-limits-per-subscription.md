---
title: Fichier Include
description: Fichier Include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844075"
---
Vous pouvez créer plusieurs services au sein d’un abonnement (chacun d’eux étant approvisionné à un niveau spécifique) en leur affectant uniquement la limite du nombre de services autorisé à chaque niveau. Ainsi, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement. Pour en savoir plus sur ces niveaux, voir [Choisir une référence (SKU) ou un niveau tarifaire pour Azure Search](../articles/search/search-sku-tier.md).

Les limites de service maximales peuvent être augmentées sur demande. S’il vous faut davantage de services dans le même abonnement, contactez le Support Azure.

| Ressource            | Gratuit&nbsp;<sup>1</sup> | De base | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Nombre de services maximum    |1     | 12    | 12  | 6.  | 6.  | 6.     |
| Mise à l’échelle maximale en unités de recherche &nbsp;<sup>2</sup> |N/A |3 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |

<sup>1</sup> Le niveau Gratuit est basé sur des ressources partagées, non des ressources dédiées. Le « scale up » n’est pas pris en charge sur les ressources partagées.

<sup>2</sup> Les unités de recherche sont des unités de facturation, allouées en tant que *réplicas* ou *partitions*. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Pour en savoir plus sur les calculs SU, consultez [Mettre à l’échelle les niveaux de ressources pour les charges de travail de requête et d’index](../articles/search/search-capacity-planning.md). 