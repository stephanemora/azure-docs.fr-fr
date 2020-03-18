---
title: Interroger les données de télémétrie ingérées
description: Cet article explique comment interroger les données de télémétrie ingérées.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 2786519bfc54b39c986ba57c78d9d6409a596351
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129785"
---
# <a name="query-ingested-telemetry-data"></a>Interroger les données de télémétrie ingérées

Cet article explique comment interroger des données de capteur ingérées à partir d’Azure FarmBeats.

L’ingestion de données à partir de ressources IoT (Internet des objets) telles que des appareils et des capteurs est un scénario courant dans FarmBeats. Vous créez des métadonnées pour les appareils et les capteurs, puis vous ingérez les données historiques dans FarmBeats dans un format canonique. Une fois que les données de capteur sont disponibles sur le hub de données FarmBeats, nous pouvons l’interroger pour générer des insights actionnables ou créer des modèles.

## <a name="before-you-begin"></a>Avant de commencer

Avant de poursuivre cet article, vérifiez que vous avez installé FarmBeats et ingéré des données de télémétrie de capteur à partir de vos appareils IoT vers FarmBeats.
Pour ingérer des données de télémétrie de capteur, consultez [Ingérer des données de télémétrie historiques](ingest-historical-telemetry-data-in-azure-farmbeats.md).

Avant de continuer, familiarisez-vous avec les API REST FarmBeats, car vous interrogerez la télémétrie ingérée à l’aide d’API. Pour plus d’informations sur les API FarmBeats, consultez [API REST FarmBeats](rest-api-in-azure-farmbeats.md). **Vérifiez que vous pouvez effectuer des requêtes d’API à votre point de terminaison de hub de données FarmBeats.**

## <a name="query-ingested-sensor-telemetry-data"></a>Interroger les données de télémétrie de capteur ingérées

Il existe deux façons d’accéder aux données de télémétrie et de les interroger à partir de FarmBeats : API et Time Series Insights (TSI). 

### <a name="query-using-rest-api"></a>Interroger à l’aide des API REST

Effectuez les étapes ci-dessous pour interroger les données de télémétrie de capteur ingérées à l’aide des API REST FarmBeats :

1. Identifiez le capteur qui vous intéresse. Pour ce faire, vous pouvez exécuter une requête d’extraction sur l’API /Sensor. Notez l’**id** et le **sensorModelId** de l’objet capteur qui vous intéresse.

2. Exécutez une opération GET/{id} sur l’API /SensorModel pour le **sensorModelId** comme indiqué à l’étape 1. Le modèle de capteur contient toutes les métadonnées et les détails relatifs à la télémétrie ingérée du capteur. Par exemple, la mesure de capteur dans l’objet modèle de capteur contient des détails sur les mesures envoyées par le capteur et sur les types et les unités. Par exemple,

```json
{
    "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
    "dataType": "Double <Data Type - eg. Double>",
    "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
    "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
    "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
    "description": "<Description of the measure>"
}
```
Prenez note de la réponse de l’appel GET/{id} pour le modèle de capteur.

3. Effectuez un appel sur l’API /Telemetry avec la charge utile d’entrée suivante :

```json
{
  "sensorId": "<id of the sensor as noted in step 1>",
  "searchSpan": {
    "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
    "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
  },
  "filter": {
    "tsx": "string"
  },
  "projectedProperties": [
    {
      "additionalProp1": "string",
      "additionalProp2": "string",
      "additionalProp3": "string"
    }
  ]
}
```
4. La réponse de l’API /Telemetry ressemblera à ceci :

```json
{
  "timestamps": [
    "2020-XX-XXT07:30:00Z",
    "2020-XX-XXT07:45:00Z"
  ],
  "properties": [
    {
      "values": [
        "<id of the sensor>",
        "<id of the sensor>"
      ],
      "name": "Id",
      "type": "String"
    },
    {
      "values": [
        2.1,
        2.2
      ],
      "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
      "type": "Double <Data Type of the value - eg. Double>"
    }
  ]
}
```
Dans l’exemple de réponse ci-dessus, la télémétrie de capteur interrogée fournit des données pour deux horodatages ainsi que le nom de la mesure (« moist_soil_last ») et les valeurs de la télémétrie signalée dans les deux horodatages. Vous devrez faire référence au modèle de capteur associé (comme décrit à l’étape 2) pour interpréter le type et l’unité des valeurs signalées.

### <a name="query-using-azure-time-series-insights-tsi"></a>Interroger à l’aide d’Azure Time Series Insights (TSI)

FarmBeats s’appuie sur [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) pour ingérer, stocker, interroger et visualiser les données à l’échelle IoT (des données hautement contextuelles et optimisées pour les séries chronologiques).

Les données de télémétrie sont reçues sur un EventHub, puis traitées et transmises à un environnement TSI au sein du groupe de ressources FarmBeats. Les données peuvent ensuite être interrogées directement à partir de STI. Pour plus d'informations, reportez-vous à la [documentation de TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Effectuez les étapes ci-dessous pour visualiser les données sur TSI :

1. Accédez au portail Azure -> Groupe de ressources FarmBeats DataHub-> cliquez sur Environnement Time Series Insights (TSI-xxxx) -> Stratégies d’accès aux données. Ajoutez un utilisateur avec un accès Lecteur ou Contributeur.
2. Accédez à la page Vue d’ensemble de l’environnement Time Series Insights (TSI-xxxx) et cliquez sur l’URL de l’Explorateur Time Series Insights. Vous pouvez maintenant visualiser la télémétrie ingérée.

Outre le stockage, l’interrogation et la visualisation des données de télémétrie, TSI autorise également l’intégration à un tableau de bord Power BI. Vous trouverez plus de détails [ici]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="next-steps"></a>Étapes suivantes

Vous avez interrogé les données de capteur à partir de votre instance d’Azure FarmBeats. À présent, découvrez comment [générer des cartes](generate-maps-in-azure-farmbeats.md#generate-maps) pour vos fermes.
