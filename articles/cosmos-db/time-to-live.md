---
title: Faire expirer des données dans Cosmos DB avec la durée de vie
description: Avec la TTL, Microsoft Azure Cosmos DB offre la possibilité de vider automatiquement les documents du système après une période déterminée.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.openlocfilehash: c08c171e3a95b0d0f408660a7ec9021ca0323fbd
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621273"
---
# <a name="time-to-live-for-azure-cosmos-db-data"></a>Durée de vie pour données Azure Cosmos DB

Avec la « durée de vie » (TTL, Time to Live), Azure Cosmos DB permet de supprimer automatiquement des éléments d'un conteneur après une période déterminée. La durée de vie par défaut peut être définie au niveau du conteneur et être substituée par élément. Une fois la durée de vie définie au niveau d'un conteneur ou d'un élément, Azure Cosmos DB supprime automatiquement les éléments correspondants au terme de la période écoulée depuis la dernière modification. La valeur de durée de vie est définie en secondes. Contrairement à une opération de suppression explicitement émise par l'application cliente, lorsque vous définissez la durée de vie, le système supprime automatiquement les éléments arrivés à expiration en fonction de la valeur de durée de vie.

## <a name="time-to-live-for-containers-and-items"></a>Durée de vie pour les conteneurs et éléments

La valeur de durée de vie est définie en secondes et interprétée en tant qu'écart par rapport à la dernière modification de l'élément. Vous pouvez définir la durée de vie sur un conteneur ou sur un élément présent dans le conteneur :

1. **Durée de vie sur un conteneur** (définie via `DefaultTimeToLive`) :

   - Si ce paramètre est manquant (ou s'il est défini sur null), les éléments n'expirent pas automatiquement.

   - Si ce paramètre est présent et que sa valeur est définie sur « -1 », il est égal à l'infini et, par défaut, les éléments n'expirent pas.

   - Si ce paramètre est présent et que sa valeur est définie sur un nombre quelconque (« n »), les éléments expirent « n » secondes après leur dernière modification.

2. **Durée de vie sur un élément** (définie via `TimeToLive`) :

   - Cette propriété s'applique uniquement si la propriété `DefaultTimeToLive` est présente et qu'elle n'est pas définie sur null pour le conteneur parent.

   - Si elle est présente, elle remplace la valeur `DefaultTimeToLive` du conteneur parent.

## <a name="time-to-live-configurations"></a>Configurations de durée de vie

* Si une durée de vie de « n » est définie sur un conteneur, les éléments présents dans ce conteneur expireront après n secondes.  Si le même conteneur contient des éléments qui possèdent leur propre durée de vie, définie sur -1 (ce qui indique qu'ils n'expirent pas), ou si certains éléments ont remplacé le paramètre de durée de vie par un autre nombre, ces éléments expirent en fonction de la valeur de durée de vie définie. 

* Si aucune durée de vie n'est définie sur un conteneur, la durée de vie définie sur un élément présent dans ce conteneur n'a aucun effet. 

* Si une durée de vie de -1 est définie sur un conteneur, un élément présent dans ce conteneur et dont la durée de vie est définie sur n expirera après n secondes, et les autres éléments n'expireront pas. 

La suppression d'éléments basée sur la durée de vie est gratuite. Aucun coût supplémentaire n'est appliqué (autrement dit, aucune RU supplémentaire n'est utilisée) lorsque l'élément est supprimé suite à l'expiration de la durée de vie.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à configurer la durée de vie dans les articles suivants :

* [Configurer la durée de vie](how-to-time-to-live.md)
