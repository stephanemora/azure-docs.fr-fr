---
title: Scénarios d’agrégation gardiennage virtuel et géospatiales avec Azure Stream Analytique
description: Cet article décrit comment utiliser Azure Stream Analytique pour l’agrégation gardiennage virtuel et géospatiales.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: cc301855e4cdcb8eb687e753835577399cfe72b0
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806959"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scénarios d’agrégation gardiennage virtuel et géospatiales avec Azure Stream Analytique

Avec des fonctions géospatiales intégrées, vous pouvez utiliser Azure Stream Analytique pour générer des applications pour des scénarios tels que la gestion de flotte, puisse surfer sur le partage, les voitures connectées et suivi des ressources.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytique prend en charge les calculs de géorepérage en temps réel à faible latence dans le cloud et sur le runtime IoT Edge.

### <a name="geofencing-scenario"></a>Scénario de gardiennage virtuel

Une société de fabrication doit suivre les ressources sur leurs bâtiments. Ils équipé de chaque appareil avec un système GPS et que vous souhaitez recevoir des notifications si un périphérique quitte une certaine zone.

Les données de référence utilisées dans cet exemple contient les informations de la clôture virtuelle seulement avoir pour les bâtiments et les appareils qui sont autorisés dans chacun des bâtiments. N’oubliez pas que les données de référence peut être statiques ou lentes de modification. Données de référence statiques sont utilisées pour ce scénario. Un flux de données continue d’émettre l’ID d’appareil et sa position actuelle.

### <a name="define-geofences-in-reference-data"></a>Définir les clôtures virtuelles dans les données de référence

Une clôture virtuelle seulement avoir peut être définie à l’aide d’un objet GeoJSON. Pour les tâches avec la version 1.2 et versions ultérieures de la compatibilité, clôtures virtuelles peuvent également être définis à l’aide de texte WKT (WKT) en tant que `NVARCHAR(MAX)`. WKT est un Consortium OGC (Open Geospatial) standard qui est utilisé pour représenter les données spatiales dans un format texte.

Les fonctions géospatiales intégrées peuvent utiliser clôtures virtuelles définies pour déterminer si un élément est dans ou hors d’un polygone de la clôture virtuelle seulement avoir spécifique.

Le tableau suivant est un exemple de données de référence de clôture virtuelle seulement avoir qui pourraient être stockées dans le stockage blob Azure ou une table SQL Azure. Chaque site est représenté par un polygone géographique, et chaque appareil est associé à un ID de site autorisé.

|SiteID|SiteName|Limite géographique|AllowedDeviceID|
|------|--------|--------|---------------|
|1|« Redmond Building 41 »|« POLYGONE ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)) »|"B"|
|2|« Building Redmond 40 »|« POLYGONE ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)) »|« A »|
|3|« Building Redmond 22 »|« POLYGONE ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)) »|« C »|

### <a name="generate-alerts-with-geofence"></a>Générer des alertes avec clôture virtuelle seulement avoir

Les appareils peuvent émettre leurs ID et l’emplacement chaque minute via un flux nommé `DeviceStreamInput`. Le tableau suivant est un flux d’entrée.

|ID d'appareil|GeoPosition|
|--------|-----------|
|« A »|« POINT(-122.13292341559497 47.636318374032726) »|
|"B"|« POINT(-122.13338475554553 47.63743531308874) »|
|« C »|« POINT(-122.13354001095752 47.63627622505007) »|

Vous pouvez écrire une requête qui joint le flux de l’appareil avec les données de référence de clôture virtuelle seulement avoir et génère une alerte chaque fois qu’un appareil est en dehors d’un bâtiment autorisé.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

L’illustration suivante représente la clôture virtuelle. Vous pouvez voir où les périphériques se conformément à l’entrée de données de flux de données.

![Clôture virtuelle de génération](./media/geospatial-scenarios/building-geofences.png)

APPAREIL « C » se trouve à l’intérieur de la création d’ID 2, ce qui n’est pas autorisée en fonction des données de référence. Cet appareil doit se trouver à l’intérieur de la création d’ID 3. Exécution de ce travail génère une alerte pour cette violation spécifique.

### <a name="site-with-multiple-allowed-devices"></a>Site avec plusieurs périphériques autorisés

Si un site autorise plusieurs appareils, un tableau d’ID de périphérique peut être défini dans `AllowedDeviceID` et une fonction définie par l’utilisateur peut être utilisée sur le `WHERE` clause pour vérifier si l’ID de périphérique de flux de données correspond à n’importe quel ID de périphérique dans cette liste. Pour plus d’informations, consultez le [UDF Javascript](stream-analytics-javascript-user-defined-functions.md) didacticiel pour les travaux de cloud et le [ C# UDF](stream-analytics-edge-csharp-udf.md) didacticiel pour les tâches de périphérie.

## <a name="geospatial-aggregation"></a>Agrégation de géospatiales

Azure Stream Analytique prend en charge l’agrégation de géographique en temps réel à faible latence dans le cloud et sur le runtime IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Scénario de l’agrégation géospatiales

Une société de cab souhaite créer une application en temps réel pour guider leurs pilotes cab recherchez tour vers les zones des villes connaît actuellement supérieure à la demande.

La société stocke des régions logiques de la ville en tant que données de référence. Chaque région est définie par un regionid est, RegionName et clôture virtuelle seulement avoir.

### <a name="define-the-geofences"></a>Définir la clôture virtuelle

Le tableau suivant est un exemple de données de référence de clôture virtuelle seulement avoir qui pourraient être stockées dans le stockage blob Azure ou une table SQL Azure. Toutes les régions sont représentée par un polygone géographique, qui est utilisé pour mettre en corrélation avec les demandes provenant de diffusion en continu de données.

Ces polygones de référence uniquement et ne représentent pas les séparations logiques ou physiques réels de ville.

|RegionID|RegionName|Limite géographique|
|--------|----------|--------|
|1|"SoHo"|« POLYGONE ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|« Chinatown »|« POLYGONE ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)) »|
|3|« Tribeca »|« POLYGONE ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787)) »|

### <a name="aggregate-data-over-a-window-of-time"></a>Agréger des données sur une fenêtre de temps

Le tableau suivant contient des données de diffusion en continu de « trajets ».

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|« A »|« POINT(-74.00726861389182 40.71610611981975) »|« POINT(-73.98615095917779 40.703107386025835) »|"2019-03-12T07:00:00Z"|
|"B"|« POINT(-74.00249841021645 40.723827238895666) »|« POINT(-74.01160699942085 40.71378884930115) »|"2019-03-12T07:01:00Z"|
|« C »|« POINT(-73.99680120565864 40.716439898624024) »|« POINT(-73.98289663412544 40.72582343969828) »|"2019-03-12T07:02:00Z"|
|"D"|« POINT(-74.00741090068288 40.71615626755086) »|« POINT(-73.97999843120539 40.73477895807408) »|"2019-03-12T07:03:00Z"|

La requête suivante joint le flux de l’appareil avec les données de référence de clôture virtuelle seulement avoir et calcule le nombre de demandes par région sur une période de 15 minutes toutes les minutes.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Cette requête génère un nombre de requêtes pour toutes les minutes au cours des 15 dernières minutes par chaque région au sein de la ville. Ces informations peuvent être affichées facilement par tableau de bord Power BI ou diffusées à tous les pilotes en tant que messages de texte SMS grâce à l’intégration avec des services comme Azure functions.

L’image ci-dessous illustre la sortie de la requête au tableau de bord Power BI. 

![Sortie du résultat sur le tableau de bord Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Étapes suivantes

* [Introduction aux fonctions géospatiales de Stream Analytique](stream-analytics-geospatial-functions.md)
* [Fonctions géospatiales (Azure Stream Analytique)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
