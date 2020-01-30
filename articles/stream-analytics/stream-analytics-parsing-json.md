---
title: Analyse de données JSON et d’AVRO dans Stream Analytics
description: Cet article explique comment utiliser des types de données complexes tels que des tableaux, et des données au format JSON ou CSV.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: cbfa6f8b85814f0f77234e014ade0ff757a4c4b8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720076"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analyser des données JSON et Avro dans Azure Stream Analytics

Azure Stream Analytics prend en charge le traitement d’événements dans les formats de données CSV, JSON et Avro. Des données JSON et Avro peuvent être structurées et contenir certains types complexes, tels que des tableaux et objets (enregistrements) imbriqués. 




## <a name="record-data-types"></a>Données de type enregistrement
Des données de type enregistrement sont utilisées pour représenter des tableaux JSON et Avro quand des formats correspondants sont utilisés dans les flux de données en entrée. Ces exemples montrent un capteur qui lit les événements en entrée au format JSON. Voici un exemple d’événement unique :

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>Accéder aux champs imbriqués dans un schéma connu
Utilisez la notation par points (.) pour accéder facilement aux champs imbriqués, directement à partir de votre requête. Par exemple, cette requête sélectionne les coordonnées de latitude et de longitude sous la propriété Location dans les données de l’extrait JSON précédent. La notation par points peut servir à parcourir plusieurs niveaux, comme indiqué ci-dessous.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Sélectionner toutes les propriétés
Vous pouvez sélectionner toutes les propriétés d’un enregistrement imbriqué à l’aide du caractère générique «  * ». Prenons l’exemple suivant :

```SQL
SELECT input.Location.*
FROM input
```

Le résultat est le suivant :

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Accéder aux champs imbriqués lorsque le nom de la propriété est une variable
Utilisez la fonction [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) si le nom de la propriété est une variable. 

Par exemple, imaginez qu’un exemple de flux de données doive être joint à des données de référence contenant des seuils pour chaque capteur d’appareil. Un extrait de ce type de données de référence est reproduit ci-dessous.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Convertir les champs d’enregistrement en événements distincts
Pour convertir les champs d’enregistrement en événements distincts, utilisez l’opérateur [APPLIQUER](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) avec la fonction [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics). Par exemple, si l’exemple précédent contenait plusieurs enregistrements pour SensorReading, la requête suivante pourrait servir à les extraire dans différents événements :

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Données de type tableau

Les données de type tableau sont des collections ordonnées de valeurs. Certaines opérations courantes sur des valeurs de tableau sont décrites ci-dessous. Ces exemples supposent que les événements en entrée ont une propriété nommée « arrayField » qui est un type de données de tableau.

Ces exemples utilisent les fonctions [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) et [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics), ainsi que l’opérateur [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics).

### <a name="working-with-a-specific-array-element"></a>Utilisation d’un élément de tableau spécifique
Sélectionnez un élément de tableau à un index spécifié (en sélectionnant le premier élément du tableau) :

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Sélection d’une longueur de tableau

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Conversion des éléments de tableau en événements distincts
Sélectionnez tous les éléments du tableau en tant qu’événements individuels. L’opérateur [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) avec la fonction intégrée [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extrait tous les éléments du tableau en tant qu’événements individuels :

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Voir aussi
[Types de données dans Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
