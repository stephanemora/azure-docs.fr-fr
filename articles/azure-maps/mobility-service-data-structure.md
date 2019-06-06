---
title: Structures de données de Service de mobilité dans Azure Maps | Microsoft Docs
description: Structures de données du service mobilité Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735555"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Structures de données du service mobilité Azure Maps

Cet article présente le concept de zone Metro dans [Service de mobilité d’Azure Maps](https://aka.ms/AzureMapsMobilityService) et certains des champs communs renvoyés via les services, lorsqu’elle est interrogée pour le transit public s’arrête et les lignes. Nous vous recommandons de passer par cet article avant de démarrer avec les API de Service de mobilité. Nous abordons ces champs communs ci-dessous.

## <a name="metro-area"></a>Zone Metro

Données du Service mobilité sont divisées en zones metro pris en charge. Banlieue ne suivre pas les limites de la ville, une zone metro peut contenir plusieurs villes, par exemple, très peuplée ville et son environnantes villes ; et un pays/région peut être une zone metro. 

Le `metroID` est l’ID d’une zone metro qui peut être utilisé pour appeler le [Metro zone d’informations sur l’API Get](https://aka.ms/AzureMapsMobilityMetroAreaInfo) aux types de transit de demande pris en charge et des détails supplémentaires sur la zone metro telles que les alertes actives et les agences de transit. Vous pouvez utiliser l’API de Metro obtenir d’Azure Maps pour demander la banlieue pris en charge et metroIDs. ID de zone Metro sont susceptibles d’être modifiées.

**metroID :** 522 **nom :** Seattle-Tacoma-Bellevue

![Région de Seattle metro](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Arrêter l’ID

S’arrête de transit peut être référencés par les deux types d’ID, le [général Transit flux spécification (GFTS)](https://gtfs.org/) ID (appelé stopKey) et les mappages Azure arrêter ID (appelé stopId). Lorsque vous faites référence à s’arrête au fil du temps, il est recommandé d’utiliser l’ID d’arrêt Azure Maps, comme ce code est plus stable et probablement ne changera pas tant que l’arrêt physique existe. L’ID d’arrêt GTFS est mis à jour plus souvent, par exemple, au cas où le fournisseur GTFS a besoin pour la modifier ou la nouvelle version GTFS est publiée, bien que l’arrêt physique ne comportait aucune modification.

Pour commencer, vous pouvez demander à proximité transit s’arrête à l’aide de [API obtention du Transit à proximité](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Groupes de lignes et de lignes

Service mobilité utilise un modèle de données en parallèle pour les lignes et groupes de ligne pour mieux traiter modifications héritée [GTFS](https://gtfs.org/) itinéraires et courses modèle de données.


### <a name="line-groups"></a>Groupes de lignes

Un groupe de ligne est une entité, qui regroupe toutes les lignes qui font logiquement partie du même groupe. Généralement un groupe de courbes contiendra deux lignes, un seul passage du point A vers B et les autres retour du point B vers A, les deux appartenant à l’agence de transports publics même et ayant le même numéro de ligne. Toutefois, il peut arriver dans lequel un groupe de la ligne a plus de deux lignes ou qu’une seule ligne qu’il contient.


### <a name="lines"></a>Lignes

Comme expliqué ci-dessus, chaque groupe de lignes est composé d’un ensemble de lignes. Fréquence à laquelle chaque ligne décrit une direction et chaque ligne de groupe est composé de deux lignes. Cependant, il existe des cas dans les lignes plus constituent un groupe de la ligne, par exemple il est une ligne qui parfois détourne via un certain voisinage et parfois pas et est géré dans les deux cas sous le même numéro de ligne, et il existe des autres cas dans lequel une ligne g roupe est composé d’une seule ligne, par exemple une ligne circulaire avec un seul sens.

Pour commencer, vous pouvez demander des groupes de lignes à l’aide de la [obtenir Transit ligne API](https://aka.ms/AzureMapsMobilityTransitLine) et versions ultérieur accédez aux lignes.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment demander des données de transit à l’aide du Service mobilité :

> [!div class="nextstepaction"]
> [Comment demander des données de transit](how-to-request-transit-data.md)

Découvrez comment demander des données en temps réel à l’aide du Service mobilité :

> [!div class="nextstepaction"]
> [Comment demander des données en temps réel](how-to-request-real-time-data.md)

Explorez la documentation de l’API du Service mobilité Azure Maps

> [!div class="nextstepaction"]
> [Documentation de l’API du Service mobilité](https://aka.ms/AzureMapsMobilityService)
