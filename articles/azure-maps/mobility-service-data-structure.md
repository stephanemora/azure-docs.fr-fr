---
title: Structures des données du service Mobility dans Azure Maps | Microsoft Azure Maps
description: Dans cet article, vous allez découvrir les structures des données et les champs courants retournés via les services Mobility Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910719"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Structure des données Azure Maps Mobility Service

Cet article présente le concept de Zone urbaine dans [Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) ainsi que certains champs communs retournés via les services, lorsqu’ils sont demandés pour renseigner les arrêts et lignes de transport. Nous vous recommandons de consulter cet article avant de commencer à utiliser les API Mobility Service. Nous traitons de ces champs communs ci-dessous.

## <a name="metro-area"></a>Zone urbaine

Les données Mobility Service sont divisées en zones urbaines prises en charge. Les zones urbaines ne sont pas soumises aux limites des villes. Par exemple, une zone urbaine peut comprendre plusieurs villes, une ville très peuplée et les villes alentours. Une région ou un pays peuvent être une zone urbaine. 

`metroID` est l’ID d’une zone urbaine qui peut être utilisé pour appeler la commande [Get Metro Area Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) pour demander les types de transports disponibles et d’autres informations supplémentaires concernant la zone urbaine, telles que les sociétés de transport et les alertes actives. Vous pouvez utiliser l’API Get Metro d’Azure Maps pour demander les zones urbaines prises en charge et les metroID. Les ID de zones urbaines sont susceptibles d’être modifiés.

**metroID :** 522   **Nom :** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>ID d’arrêts

Les arrêts de transport peuvent avoir deux types d’ID, l’ID [General Transit Feed Specification (GFTS)](https://gtfs.org/), appelé stopKey, et l’ID d’arrêt Azure Maps, appelé stopId. Lorsque vous faites référence plusieurs fois à des arrêts, il est recommandé d’utiliser l’ID d’arrêt Azure Maps, car il s’agit de l’ID le plus stable et ne changera probablement pas, tant que l’arrêt physique existe. L’ID d’arrêt GTFS est mis à jour plus souvent, par exemple dans le cas où le fournisseur GTFS doit le changer ou qu’une nouvelle version GTFS est disponible, même si l’arrêt physique ne change pas.

Pour commencer, vous pouvez demander des arrêts de transport proches, en utilisant l’[API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Lignes et groupes de lignes

Mobility Service utilise un modèle de données parallèle pour les lignes et groupes de lignes afin de mieux gérer les changements hérités du modèle de données des trajets et parcours [GTFS](https://gtfs.org/).


### <a name="line-groups"></a>Groupes de lignes

Un groupe de lignes est une entité regroupant toutes les lignes qui font logiquement partie du même groupe. En général, un groupe comprendra deux lignes, le trajet aller du point A au point B, et le trajet retour du point B au point A, toutes deux faisant partie de la même société de transports publics et ayant le même numéro de ligne. Toutefois, il se peut qu’un groupe de lignes comprenne plus de deux lignes ou seulement une ligne.


### <a name="lines"></a>Lignes

Comme indiqué ci-dessus, chaque groupe de lignes comprend un ensemble de lignes. Le plus souvent, chaque ligne décrit une direction et chaque groupe de lignes comprend deux lignes. Toutefois, il se peut qu’un groupe de lignes comprenne plusieurs lignes. Par exemple, une ligne fait parfois un détour par un certain quartier, d’autres fois non, mais conserve toutefois le même numéro de ligne. Il se peut également qu’un groupe de lignes ne comprenne qu’une seule ligne, une ligne circulaire et une seule direction, par exemple.

Pour commencer, vous pouvez demander les groupes de lignes en utilisant l’[API Get Transit Line](https://aka.ms/AzureMapsMobilityTransitLine) et explorer plus en détail les lignes ultérieurement.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment demander des données de transit à l’aide de Mobility Service :

> [!div class="nextstepaction"]
> [Comment demander des données de transit](how-to-request-transit-data.md)

Découvrez comment demander des données en temps réel à l’aide de Mobility Service :

> [!div class="nextstepaction"]
> [Comment demander des données en temps réel](how-to-request-real-time-data.md)

Explorer la documentation de l’API Mobility Service d’Azure Maps

> [!div class="nextstepaction"]
> [Documentation de l’API Mobility Service](https://aka.ms/AzureMapsMobilityService)
