---
title: 'Langage de requête Azure Cosmos DB : ST_ISVALID'
description: Découvrez la fonction système SQL ST_ISVALID dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad7238f5bb5301e14cfaca518b2bd7d9bc7139fb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525667"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Renvoie une valeur booléenne indiquant si l’expression Point, Polygon, MultiPolygon ou LineString GeoJSON spécifiée est valide.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*spatial_expr*  
   Est une expression GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment vérifier si un point est valide avec ST_VALID.  
  
  Par exemple, ce point a une valeur de latitude qui n’est pas dans la plage valide de valeurs [-90, 90]. Par conséquent, la requête retourne false.  
  
  La spécification GeoJSON impose que, pour les polygones, la dernière paire de coordonnées fournie soit identique à la première, pour créer une forme fermée. Les points dans un polygone doivent être spécifiés dans le sens antihoraire. Un polygone spécifié dans le sens horaire représente l'inverse de la région qu'il contient.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions spatiales Azure Cosmos DB](sql-query-spatial-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
