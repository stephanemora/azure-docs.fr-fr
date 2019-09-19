---
title: Fichier Include
description: Fichier Include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67177442"
---
Vous pouvez créer plusieurs services au sein d’un abonnement. Chacun peut être approvisionné à un niveau spécifique. Vous êtes uniquement limité par le nombre de services autorisé à chaque niveau. Ainsi, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement. Pour en savoir plus sur ces niveaux, consultez [Choose a pricing tier for Azure Search](../articles/search/search-sku-tier.md) (Choisir un niveau tarifaire pour Recherche Azure).

Les limites de service maximales peuvent être augmentées sur demande. S’il vous faut davantage de services dans le même abonnement, contactez le Support Azure.

| Ressource            | Gratuit<sup>1</sup> | De base | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Nombre de services maximum    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Augmentation de la taille des instances : nombre maximal d’unités de recherche (SU) <sup>2</sup> |N/A |3 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |

<sup>1</sup> Le niveau Gratuit est basé sur des ressources partagées, non des ressources dédiées. Le « scale up » n’est pas pris en charge sur les ressources partagées.

<sup>2</sup> Les unités de recherche sont des unités de facturation, allouées en tant que *réplicas* ou *partitions*. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Pour en savoir plus sur les calculs SU, consultez [Mettre à l’échelle les niveaux de ressources pour les charges de travail de requête et d’index](../articles/search/search-capacity-planning.md). 