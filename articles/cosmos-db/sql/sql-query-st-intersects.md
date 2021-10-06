---
title: 'Langage de requête Azure Cosmos DB : ST_INTERSECTS'
description: Découvrez la fonction système SQL ST_INTERSECTS dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ac3ba6ec00dc617d434026a6693c6c4554421140
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647755"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne une expression booléenne indiquant si l’objet GeoJSON (Point, Polygon, MultiPolygon ou LineString) spécifié dans le premier argument croise le GeoJSON (Point, Polygon, MultiPolygon ou LineString) du deuxième argument.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*spatial_expr*  
   Est une expression d’objet GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une valeur booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment rechercher toutes les zones ayant une intersection avec le polygone donné.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Notes

Cette fonction système tirera parti d’un [index géospatial](../index-policy.md#spatial-indexes), sauf dans les requêtes avec agrégats.

> [!NOTE]
> La spécification GeoJSON nécessite que les points d’un polygone soient spécifiés dans le sens inverse des aiguilles d’une montre. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions spatiales Azure Cosmos DB](sql-query-spatial-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
