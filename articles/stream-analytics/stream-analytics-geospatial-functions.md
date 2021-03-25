---
title: Présentation des fonctions géospatiales d’Azure Stream Analytics
description: Cet article décrit les fonctions géospatiales qui sont utilisées dans les travaux Azure Stream Analytics.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438538"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Présentation des fonctions géospatiales Stream Analytics

Les fonctions géospatiales d’Azure Stream Analytics permettent d’effectuer une analytique en temps réel des données géospatiales de streaming. Avec seulement quelques lignes de code, vous pouvez développer une solution de production adaptée aux scénarios complexes. Ces fonctions prennent en charge tous les types WKT et les géométries GeoJSON Point, Polygon et LineString.

Voici quelques exemples de scénarios qui peuvent bénéficier des fonctions géospatiales :

* Le covoiturage
* La gestion de flotte
* Suivi des ressources
* Délimitation géographique
* Traçage des téléphones sur les stations cellulaires

Le langage de requête Stream Analytics possède sept fonctions géospatiales intégrées : **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** et **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

La fonction `CreateLineString` accepte des points et retourne un LineString GeoJSON, qui peut être tracé sous la forme d’une ligne sur une carte. Vous devez avoir au moins deux points pour créer un LineString. Les points LineString seront connectés dans l’ordre.

La requête suivante utilise `CreateLineString` pour créer un LineString à l’aide de trois points. Le premier point est créé à partir des données d’entrée de streaming, et les deux autres sont créés manuellement.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exemple d’entrée  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemple de sortie  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Pour plus d’informations, consultez la référence [CreateLineString](/stream-analytics-query/createlinestring).

## <a name="createpoint"></a>CreatePoint

La fonction `CreatePoint` accepte une latitude et une longitude, et retourne un point GeoJSON, qui peut être tracé sur une carte. Le type de données de vos latitudes et de vos longitudes doit être **float**.

L’exemple de requête suivant utilise `CreatePoint` pour créer un point à l’aide des latitudes et des longitudes issues des données d’entrée de streaming.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exemple d’entrée  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemple de sortie
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

Pour plus d’informations, consultez la référence [CreatePoint](/stream-analytics-query/createpoint).

## <a name="createpolygon"></a>CreatePolygon

La fonction `CreatePolygon` accepte des points et retourne un enregistrement de polygone GeoJSON. L’ordre des points doit respecter le sens antihoraire. Imaginez-vous marcher d’un point à l’autre dans l’ordre dans lequel ils sont déclarés. Le centre du polygone se trouverait toujours sur votre gauche.

L’exemple de requête suivant utilise `CreatePolygon` pour créer un polygone à partir de trois points. Les deux premiers points sont créés manuellement, et le dernier point est créé à partir des données d’entrée.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exemple d’entrée  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemple de sortie  

 {"type" : "Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Pour plus d’informations, consultez la référence [CreatePolygon](/stream-analytics-query/createpolygon).


## <a name="st_distance"></a>ST_DISTANCE
La fonction `ST_DISTANCE` retourne la distance en mètres entre deux géométries. 

La requête suivante utilise `ST_DISTANCE` pour générer un événement lorsqu’une station-service se trouve à moins de 10 kilomètres de votre voiture.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Pour plus d’informations, consultez la référence [ST_DISTANCE](/stream-analytics-query/st-distance).

## <a name="st_overlaps"></a>ST_OVERLAPS
La fonction `ST_OVERLAPS` compare deux géométries. Si les géométries se chevauchent, la fonction retourne la valeur 1. Si les géométries ne se chevauchent pas, la fonction retourne la valeur 0. 

La requête suivante utilise `ST_OVERLAPS` pour générer un événement lorsqu’un bâtiment se trouve dans une zone inondable.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

La requête suivante génère un événement lorsqu’une tempête arrive en direction d’une voiture.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Pour plus d’informations, consultez la référence [ST_OVERLAPS](/stream-analytics-query/st-overlaps).

## <a name="st_intersects"></a>ST_INTERSECTS
La fonction `ST_INTERSECTS` compare deux géométries. Si les géométries se croisent, la fonction retourne la valeur 1. Si les géométries ne se croisent pas, la fonction retourne la valeur 0.

L’exemple de requête suivant utilise `ST_INTERSECTS` pour déterminer si une route pavée croise un chemin de terre.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exemple d’entrée  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exemple de sortie  

 1  
  
 0  

Pour plus d’informations, consultez la référence [ST_INTERSECTS](/stream-analytics-query/st-intersects).

## <a name="st_within"></a>ST_WITHIN
La fonction `ST_WITHIN` détermine si une géométrie se trouve dans une autre géométrie. Si la première est contenue dans la dernière, la fonction retourne la valeur 1. Si la première géométrie n’est pas située dans la dernière, la fonction retourne la valeur 0.

L’exemple de requête suivant utilise `ST_WITHIN` pour déterminer si le point de la destination de livraison se trouve dans le polygone d’entrepôt donné.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exemple d’entrée  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{"type":"Point", "coordinates": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "coordinates": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exemple de sortie  

 0  
  
 1  

Pour plus d’informations, consultez la référence [ST_WITHIN](/stream-analytics-query/st-within).

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](/rest/api/streamanalytics/)
