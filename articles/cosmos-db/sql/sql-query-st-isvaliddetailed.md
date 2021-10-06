---
title: 'Langage de requête Azure Cosmos DB : ST_ISVALIDDETAILED'
description: Découvrez la fonction système SQL ST_ISVALIDDETAILED dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1a1ca571dd90f59cf5d4a88440dbc631fe2d4553
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647754"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Renvoie une valeur JSON contenant une valeur booléenne si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide et, dans le cas contraire, le motif sous forme de valeur de chaîne.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*spatial_expr*  
   Est une expression de point ou de polygone GeoJSON.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une valeur JSON contenant une valeur booléenne si l'expression de points ou de polygones GeoJSON spécifiée est valide et si elle est non valide, le motif sous forme de valeur de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment vérifier la validité (avec détails) avec `ST_ISVALIDDETAILED`.  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

> [!NOTE]
> La spécification GeoJSON nécessite que les points d’un polygone soient spécifiés dans le sens inverse des aiguilles d’une montre. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions spatiales Azure Cosmos DB](sql-query-spatial-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
