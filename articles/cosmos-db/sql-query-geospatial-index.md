---
title: Indexer des données géospatiales avec Azure Cosmos DB
description: Indexer des données spatiales avec Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566474"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexer des données géospatiales avec Azure Cosmos DB

Nous avons conçu le moteur de base de données d’Azure Cosmos DB pour être véritablement indépendant du schéma et fournir une assistance exceptionnelle pour JSON. Le moteur de base de données optimisé en écriture d’Azure Cosmos DB comprend les données spatiales représentées dans la norme GeoJSON en mode natif.

En bref, la géométrie est projetée à partir des coordonnées géodésiques sur un plan 2D, puis divisée progressivement en cellules à l'aide un **quadtree**. Ces cellules sont mappées en 1D selon l'emplacement de la cellule dans une **courbe de remplissage d'espace de Hilbert**qui permet de préserver la localité des points. En outre, quand les données d’emplacement sont indexées, elles passent par un processus connu sous le nom de **pavage**, c’est-à-dire que toutes les cellules qui se croisent à un emplacement sont identifiées et stockées en tant que clés dans l’index Azure Cosmos DB. Au moment de la requête, des arguments comme les points et les polygones sont également fractionnés pour extraire les plages d’ID de cellule appropriées, puis utilisés pour récupérer des données à partir de l’index.

Si vous spécifiez une stratégie d’indexation qui inclut un index spatial pour /* (tous les chemins d’accès), toutes les données trouvées dans le conteneur sont indexées pour des requêtes spatiales efficaces.

> [!NOTE]
> Azure Cosmos DB prend en charge l’indexation des instances Points, LineStrings, Polygons et MultiPolygons.
>
>

## <a name="geography-data-indexing-examples"></a>Exemples d’indexation des données Geography

L’extrait de code JSON suivant montre une stratégie d’indexation avec l’indexation spatiale activée pour le type de données **Geography**. Il est valide pour les données spatiales ayant le type de données Geography et indexe toute instance Point, Polygon, MultiPolygon ou LineString GeoJSON trouvée dans les documents pour la requête spatiale. Si vous modifiez la stratégie d’indexation à l’aide du Portail Azure, vous pouvez spécifier le JSON suivant pour la stratégie d’indexation afin d’activer l’indexation spatiale pour votre conteneur :

**JSON de stratégie d’indexation de conteneur avec indexation spatiale géographique**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Si la valeur GeoJSON d'emplacement dans le document est incorrecte ou non valide, elle n’est pas indexée pour les requêtes spatiales. Vous pouvez valider les valeurs d'emplacement à l'aide de ST_ISVALID et ST_ISVALIDDETAILED.
>
>
>

Vous pouvez également [modifier la stratégie d’indexation](how-to-manage-indexing-policy.md) à l’aide d’Azure CLI, de PowerShell ou de n’importe quel Kit de développement logiciel (SDK).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment démarrer avec la prise en charge géospatiale dans Azure Cosmos DB, vous pouvez :

* En savoir plus sur les [requêtes Azure Cosmos DB](sql-query-getting-started.md)
* En savoir plus sur l’[Interrogation de données spatiales avec Azure Cosmos DB](sql-query-geospatial-query.md)
* En savoir plus sur les [données de localisation géospatiales et GeoJSON dans Azure Cosmos DB](sql-query-geospatial-intro.md)