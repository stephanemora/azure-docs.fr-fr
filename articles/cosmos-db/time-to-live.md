---
title: Faire expirer des données dans Cosmos DB avec la durée de vie
description: Avec la TTL, Microsoft Azure Cosmos DB offre la possibilité de vider automatiquement les documents du système après une période déterminée.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271269"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Durée de vie (TTL) dans Azure Cosmos DB 

Avec **Time to Live** ou de la durée de vie, Azure Cosmos DB offre la possibilité de supprimer automatiquement des éléments à partir d’un conteneur après un certain laps de temps. La durée de vie par défaut peut être définie au niveau du conteneur et être substituée par élément. Une fois la durée de vie définie au niveau d'un conteneur ou d'un élément, Azure Cosmos DB supprime automatiquement les éléments correspondants au terme de la période écoulée depuis la dernière modification. La valeur de durée de vie est définie en secondes. Lorsque vous configurez la durée de vie, le système supprimera automatiquement les éléments expirés basés sur la valeur de durée de vie, sans avoir besoin d’une opération de suppression est explicitement émise par l’application cliente.

## <a name="time-to-live-for-containers-and-items"></a>Durée de vie pour les conteneurs et éléments

La valeur durée de vie est définie en secondes, et il est interprété en tant qu’écart entre le moment où un élément a été modifié. Vous pouvez définir la durée de vie sur un conteneur ou sur un élément présent dans le conteneur :

1. **Durée de vie sur un conteneur** (définie via `DefaultTimeToLive`) :

   - Si ce paramètre est manquant (ou s'il est défini sur null), les éléments n'expirent pas automatiquement.

   - Si l’heure actuelle et la valeur est définie sur « -1 », il est égal à l’infini et éléments n’expirent pas par défaut.

   - Si présent et a la valeur est définie sur un nombre *« n »* – éléments n’expirent *« n »* secondes après leur dernière heure de modification.

2. **Durée de vie sur un élément** (définie via `ttl`) :

   - Cette propriété s'applique uniquement si la propriété `DefaultTimeToLive` est présente et qu'elle n'est pas définie sur null pour le conteneur parent.

   - Si elle est présente, elle remplace la valeur `DefaultTimeToLive` du conteneur parent.

## <a name="time-to-live-configurations"></a>Configurations de durée de vie

* Si la durée de vie est définie sur *« n »* sur un conteneur, puis les éléments dans le conteneur arrivera à expiration après *n* secondes.  Si les éléments dans le même conteneur qui ont leur propre délai pour live, la valeur -1 (indiquant qu’ils n’expirent pas) ou si certains éléments ont remplacé la durée de vie de paramètre avec un nombre différent, ces éléments expirent selon leur propre valeur de durée de vie configurée. 

* Si aucune durée de vie n'est définie sur un conteneur, la durée de vie définie sur un élément présent dans ce conteneur n'a aucun effet. 

* Si une durée de vie de -1 est définie sur un conteneur, un élément présent dans ce conteneur et dont la durée de vie est définie sur n expirera après n secondes, et les autres éléments n'expireront pas. 

La suppression d'éléments basée sur la durée de vie est gratuite. Aucun coût supplémentaire n'est appliqué (autrement dit, aucune RU supplémentaire n'est utilisée) lorsque l'élément est supprimé suite à l'expiration de la durée de vie.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer la durée de vie dans les articles suivants :

* [Configurer la durée de vie](how-to-time-to-live.md)
