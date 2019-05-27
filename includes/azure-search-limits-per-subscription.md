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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160518"
---
Vous pouvez créer plusieurs services au sein d’un abonnement. Chacun d’eux peut être configuré à un niveau spécifique. Vous êtes limité uniquement par le nombre de services autorisé à chaque niveau. Ainsi, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement. Pour plus d’informations sur les couches, consultez [choisir un niveau ou une référence (SKU) pour Azure Search](../articles/search/search-sku-tier.md).

Les limites de service maximales peuvent être augmentées sur demande. Si vous avez besoin de plus de services dans le même abonnement, contactez le Support Azure.

| Ressource            | Gratuit<sup>1</sup> | De base | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Nombre de services maximum    |1     | 16    | 16  | 8  | 6.  | 6.     | 6.  | 6.  |
| Échelle maximale en unités de recherche (SU)<sup>2</sup> |N/A |3 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |

<sup>1</sup> Le niveau Gratuit est basé sur des ressources partagées, non des ressources dédiées. Le « scale up » n’est pas pris en charge sur les ressources partagées.

<sup>2</sup> unités de recherche sont unités de facturation, allouées, soit comme un *réplica* ou un *partition*. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Pour en savoir plus sur les calculs SU, consultez [Mettre à l’échelle les niveaux de ressources pour les charges de travail de requête et d’index](../articles/search/search-capacity-planning.md). 