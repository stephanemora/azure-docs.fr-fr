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
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553558"
---
Vous pouvez créer plusieurs services au sein d’un abonnement. Chacun d’eux peut être configuré à un niveau spécifique. Vous êtes limité uniquement par le nombre de services autorisé à chaque niveau. Ainsi, vous pouvez créer jusqu’à 12 services au niveau de base et 12 autres au niveau S1 au sein du même abonnement. Pour plus d’informations sur les couches, consultez [choisir un niveau ou une référence (SKU) pour Azure Search](../articles/search/search-sku-tier.md).

Les limites de service maximales peuvent être augmentées sur demande. Si vous avez besoin de plus de services dans le même abonnement, contactez le Support Azure.

| Ressource            | Gratuit<sup>1</sup> | De base | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Nombre de services maximum    |1     | 12    | 12  | 6.  | 6.  | 6.     |
| Échelle maximale en unités de recherche (SU)<sup>2</sup> |S.O. |3 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |36 unités de recherche |

<sup>1</sup>gratuit est basé sur les ressources partagées, non dédiés,. Le « scale up » n’est pas pris en charge sur les ressources partagées.

<sup>2</sup>unités de recherche sont unités de facturation, allouées, soit comme un *réplica* ou un *partition*. Vous avez besoin des deux types de ressource pour les opérations de stockage, d’indexation et d’interrogation. Pour en savoir plus sur les calculs SU, consultez [Mettre à l’échelle les niveaux de ressources pour les charges de travail de requête et d’index](../articles/search/search-capacity-planning.md). 