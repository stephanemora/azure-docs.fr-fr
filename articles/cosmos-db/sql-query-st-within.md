---
title: 'Langage de requête Azure Cosmos DB : ST_WITHIN'
description: Découvrez la fonction système SQL ST_WITHIN dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296114"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Retourne une expression booléenne indiquant si l’objet GeoJSON (Point, Polygone ou LineString) spécifié dans le premier argument se trouve dans le GeoJSON (Point, Polygone ou LineString) du deuxième argument.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*spatial_expr*  
   Est une expression d’objet GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une valeur booléenne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment rechercher tous les documents de famille d’un polygone avec `ST_WITHIN`.  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Notes

Cette fonction système bénéficiera d’un [index géospatial](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions spatiales Azure Cosmos DB](sql-query-spatial-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
