---
title: 'Langage de requête Azure Cosmos DB : ST_DISTANCE'
description: Découvrez la fonction système SQL ST_DISTANCE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b1cf5bdad567a82bd3936f2b2a187375259407ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625339"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne la distance entre les deux expressions Point, Polygon,MultiPolygon ou LineString de GeoJSON. Pour en savoir plus, consultez l’article [Données de localisation géospatiales et GeoJSON](sql-query-geospatial-intro.md).
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*spatial_expr*  
   Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression numérique contenant la distance. Elle est exprimée en mètres pour le système de référence par défaut.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant vous montre comment retourner tous les documents de famille se trouvant dans un rayon de 30 kilomètres de l’emplacement spécifié à l’aide de la fonction intégrée `ST_DISTANCE`. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Notes

Cette fonction système tirera parti d’un [index géospatial](../index-policy.md#spatial-indexes), sauf dans les requêtes avec agrégats.

> [!NOTE]
> La spécification GeoJSON exige que les points d’un polygone soient spécifiés dans le sens inverse des aiguilles d’une montre. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions spatiales Azure Cosmos DB](sql-query-spatial-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
