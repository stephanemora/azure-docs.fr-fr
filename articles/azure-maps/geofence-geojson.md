---
title: Format de données GeoJSON pour la limite géographique | Microsoft Azure Maps
description: Dans cet article, vous découvrirez comment préparer les données de limite géographique qui peuvent être utilisées dans les API GET et POST Geofence de Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7d1c9a1587771a020f5c9f89e2497a25eb1bba70
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210019"
---
# <a name="geofencing-geojson-data"></a>Données Geofencing GeoJSON

Les API [GET Geofence](/rest/api/maps/spatial/getgeofence) et [POST Geofence](/rest/api/maps/spatial/postgeofence) Azure Maps vous permettent de récupérer la proximité d’une coordonnée par rapport à une limite géographique (geofence) ou un ensemble de limites géographiques. Cet article explique comment préparer les données de limite géographique qui peuvent être utilisées dans les API GET et POST d’Azure Maps.

Les données de la limite géographique ou d’un ensemble de limites géographiques sont représentées par l’objet `Feature` et l’objet `FeatureCollection` dans au format `GeoJSON`, ce qui est défini dans [rfc7946](https://tools.ietf.org/html/rfc7946). En outre :

* Le type d’objet GeoJSON peut être un objet `Feature` ou un objet `FeatureCollection`.
* Le type d’objet Geometry peut être un objet `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon` et `GeometryCollection`.
* Toutes les propriétés présentées doivent contenir un `geometryId`, qui est utilisé pour identifier la limite géographique.
* La fonctionnalité avec `Point`, `MultiPoint`, `LineString`, `MultiLineString` doit contenir `radius` dans les propriétés. La valeur `radius` est mesurée en mètres, la valeur `radius` est comprise entre 1 et 10 000.
* La fonctionnalité avec le type géométrique `polygon` et `multipolygon` n’a pas de propriété de rayon.
* `validityTime` est une propriété facultative qui permet à l’utilisateur de définir une heure d’expiration et la période de validité des données de la limite géographique. Si cela n’est pas spécifié, les données n’expirent jamais et sont toujours valides.
* `expiredTime` est la date et l’heure d’expiration des données de limite géographique. Si la valeur de `userTime` dans la requête est ultérieure à cette valeur, les données de la limite géographique correspondantes sont considérées comme données expirées de données et ne sont pas interrogées. Dans ce cas, le geometryId de cette limite géographique figurera dans le tableau `expiredGeofenceGeometryId` dans la réponse de la limite géographique.
* `validityPeriod` est une liste de périodes de validité pour la limite géographique. Si la valeur de `userTime` dans la requête se situe en dehors de la période de validité, les données de la limite géographique correspondantes sont considérées comme non valides et ne seront pas être interrogées. Le geometryId de cette limite géographique figure dans le tableau `invalidPeriodGeofenceGeometryId` dans la réponse de la limite géographique. Le tableau suivant présente les propriétés de l’élément validityPeriod.

| Nom | Type | Obligatoire  | Description |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | Date et heure de début de la période de validité. |
| endTime   | Datetime  | true |  Date et heure de fin de la période de validité. |
| recurrenceType | string | false |   Le type de périodicité de la période. La valeur peut être `Daily`, `Weekly`, `Monthly` ou `Yearly`. La valeur par défaut est `Daily`.|
| businessDayOnly | Boolean | false |  Indiquer si les données sont uniquement valides pendant les jours ouvrés. La valeur par défaut est `false`.|


* Toutes les valeurs des coordonnées sont représentées sous forme [longitude, latitude] définies dans `WGS84`.
* Pour chaque fonctionnalité, qui contient `MultiPoint`, `MultiLineString`, `MultiPolygon` ou `GeometryCollection`, les propriétés sont appliquées à tous les éléments. Par exemple : Tous les points dans `MultiPoint` utiliseront le même rayon pour former une limite géographique à plusieurs cercles.
* Dans un scénario de cercle à points, une géométrie cercle peut être représentée à l’aide un objet geometry `Point` avec des propriétés développés dans [Extension des géométries GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Voici un exemple de corps de demande pour une limite géographique représentée sous la forme d’une géométrie de limite géographique cercle dans `GeoJSON` à l’aide d’un point central et d’un rayon. La période de validité des données de limite géographique démarre le 22/10/2018, de 9 h 00 à 17 h 00, répétée tous les jours à l’exception du week-end. `expiredTime` indique que ces données de limite géographique seront considérées comme expirées, si `userTime` dans la demande est postérieur à `2019-01-01`.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
