---
title: 'Langage de requête Azure Cosmos DB : ST_DISTANCE'
description: Découvrez la fonction système SQL ST_DISTANCE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4908d5f9f6eccaaaf71308b868d712f0eb96cb52
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303152"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Retourne la distance entre les deux expressions Point, Polygone ou LineString de GeoJSON.  
  
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

Cette fonction système bénéficiera d’un [index géospatial](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions spatiales Azure Cosmos DB](sql-query-spatial-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](introduction.md)
