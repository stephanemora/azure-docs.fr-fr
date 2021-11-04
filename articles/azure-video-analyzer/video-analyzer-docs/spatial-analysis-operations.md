---
title: Opérations d’analyse spatiale prises en charge - Azure
description: Cet article de référence détaille les différentes propriétés des opérations d’analyse spatiale prises en charge par Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 11062ebbe48e8c7e2451ff448770055073d7e792
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560139"
---
# <a name="supported-spatial-analysis-operations"></a>Opérations d’analyse spatiale prises en charge

L’analyse spatiale permet l’analyse du streaming vidéo en temps réel à partir des caméras. Pour chaque appareil photo que vous configurez, les opérations génèrent un flux de sortie de messages JSON envoyé à Azure Video Analyzer. 

Video Analyzer implémente les opérations d’analyse spatiale suivantes : 

| Identificateur d'opération| Description|
|---------|---------|
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation | Émet un événement _personZoneEnterExitEvent_ quand une personne entre ou quitte la zone et fournit des informations directionnelles avec le côté numéroté de la zone qui a été franchie. Émet un événement _personZoneDwellTimeEvent_ quand la personne quitte la zone et fournit des informations directionnelles ainsi que le nombre de millisecondes que la personne a passées à l’intérieur de la zone. |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation | Effectue le suivi lorsqu’une personne traverse une ligne désignée dans le champ de vue de la caméra.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation | Effectue le suivi lorsqu’une personne enfreint une règle de distance.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation | Compte les personnes dans une zone désignée dans le champ de vue de la caméra. La zone doit être entièrement couverte par une seule caméra pour permettre à PersonCount d’enregistrer un total précis.  |
| Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation | Opération générique qui peut être utilisée pour exécuter tous les scénarios mentionnés ci-dessus. Cette option est plus utile lorsque vous souhaitez exécuter plusieurs scénarios sur la même caméra ou utiliser des ressources système (par exemple, GPU) de manière plus efficace. |



## <a name="person-zone-crossing"></a>Traversée d’une zone par une personne

**Identificateur d'opération** : `Microsoft.VideoAnalyzer.SpatialAnalysisPersonZoneCrossingOperation`

Consultez un exemple d'[Opération de croisement de zone de personne](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json) dans notre exemple GitHub.

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Liste de zones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                     | string  | Nom convivial de cette zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | Chaque paire de valeurs représente x et y pour les vertex d’un polygone. Le polygone représente les zones dans lesquelles les personnes sont suivies ou comptées. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre. Les événements sont émis lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 48 quand le type est zonecrossing et 16 quand l’heure est DwellTime. Il s’agit des valeurs recommandées pour obtenir une précision maximale. |
| `eventType`                 | string  | Pour cognitiveservices.vision.spatialanalysis-personcrossingpolygon, cela doit être `zonecrossing` ou `zonedwelltime`.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `trigger`                   | string  | Type de déclencheur pour l’envoi d’un événement. Valeurs prises en charge : « event » : se déclenche quand une personne entre ou quitte la zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `focus`                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 16. Il s’agit de la valeur recommandée pour obtenir une précision maximale.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | Paramètres TRACKER_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

### <a name="orientation-parameter-settings"></a>Configurations des paramètres d’orientation

Vous pouvez configurer le calcul d’orientation par le biais des paramètres DETECTOR_NODE_CONFIG
```json
{
    "enable_orientation": true,
}

```
| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_orientation`                     | bool    | Indique si vous voulez calculer, ou non, l’orientation pour les personnes détectées. `enable_orientation` est défini par défaut sur True..                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
### <a name="speed-parameter-settings"></a>Paramètres de vitesse

Vous pouvez configurer le calcul de la vitesse par le biais des paramètres TRACKER_NODE_CONFIG
```json
{
    "enable_speed": true,
}

```
| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enable_speed`                     | bool    | Indique si vous voulez calculer, ou non, la vitesse pour les personnes détectées. `enable_speed` est défini par défaut sur True. Il est fortement recommandé d’activer à la fois la vitesse et l’orientation pour obtenir les meilleures valeurs estimées.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |

#### <a name="output"></a>Sortie :
```json
{
  "body": {
    "timestamp": 147026846756730,
    "inferences": [
      {
        "type": "entity",
        "inferenceId": "8e8269c1a9584b3a8f16a3cd7a2cd45a",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.9511422
          },
          "box": {
            "l": 0.59845686,
            "t": 0.35958588,
            "w": 0.11951797,
            "h": 0.50172085
          }
        },
        "extensions": {
          "centerGroundPointY": "0.0",
          "footprintY": "inf",
          "centerGroundPointX": "0.0",
          "mappedImageOrientation": "inf",
          "groundOrientationAngle": "inf",
          "footprintX": "inf",
          "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
          "speed": "0.0"
        }
      },
      {
        "type": "entity",
        "inferenceId": "c54c9f92dd0d442b8d1840756715a5c7",
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.92762595
          },
          "box": {
            "l": 0.8098704,
            "t": 0.47707137,
            "w": 0.18019487,
            "h": 0.48659682
          }
        },
        "extensions": {
          "footprintY": "inf",
          "groundOrientationAngle": "inf",
          "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
          "mappedImageOrientation": "inf",
          "speed": "0.0",
          "centerGroundPointX": "0.0",
          "centerGroundPointY": "0.0",
          "footprintX": "inf"
        }
      },
      {
        "type": "event",
        "inferenceId": "aad2778756a94afd9055fdbb3a370d62",
        "relatedInferences": [
          "8e8269c1a9584b3a8f16a3cd7a2cd45a"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "f54d4c8fb4f345a9afb944303b0f3b40",
            "zone": "retailstore",
            "status": "Enter"
          }
        }
      },
      {
        "type": "event",
        "inferenceId": "e30103d3af28485688d7c77bbe10b5b5",
        "relatedInferences": [
          "c54c9f92dd0d442b8d1840756715a5c7"
        ],
        "event": {
          "name": "personZoneEnterExitEvent",
          "properties": {
            "trackingId": "a226eda9226e4ec9b39ebceb7c8c1f61",
            "status": "Enter",
            "zone": "retailstore"
          }
        }
      }
    ]
  }
```

## <a name="person-line-crossing"></a>Traversée d’une ligne par une personne

**Identificateur d'opération** : `Microsoft.VideoAnalyzer.SpatialAnalysisPersonLineCrossingOperation`

Consultez un exemple d'[Opération de croisement de ligne de personne](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json) dans notre exemple GitHub.

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lines`                     | list    | Liste de lignes.                                                                                                                                                                                                                                                                |
| `name`                      | string  | Nom convivial de cette ligne.                                                                                                                                                                                                                                                  |
| `line`                      | string  | Chaque paire de valeurs représente le début et la fin de la ligne. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre.                            |
| `start`                      | Paire de valeurs  | Coordonnées x, y pour le point de départ de la ligne. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre.                            |
| `end`                      | Paire de valeurs  | Coordonnées x, y pour le point de fin de la ligne. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre.                            |
| `type`                     | string  | Il doit s’agir de `linecrossing`. |
| `trigger`                     | string  | Type de déclencheur pour l’envoi d’un événement. Valeurs prises en charge : « event » : se déclenche quand quelqu’un traverse la ligne.|
| `outputFrequency`           | int     | Vitesse à laquelle les événements sont émis. Quand outputFrequency = X, chaque événement X est émis, par exemple, outputFrequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre outputFrequency s’applique à la fois à event et à interval.                                                       |
| `focus`                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint. |
| `threshold`                 | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 16. Il s’agit de la valeur recommandée pour obtenir une précision maximale.                                                                                                |
| `enableFaceMaskClassifier`  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.          |
| `detectorNodeConfiguration` | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                      |
| `trackerNodeConfiguration` | string  | Paramètres TRACKER_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="person-distance"></a>Distance de la personne

**Identificateur d'opération** : `Microsoft.VideoAnalyzer.SpatialAnalysisPersonDistanceOperation`

Consultez un exemple d'[Opération de distance de personne](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json) dans notre exemple GitHub.

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Liste de zones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | string  | Nom convivial de cette zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | Chaque paire de valeurs représente x et y pour les vertex d’un polygone. Le polygone représente les zones dans lesquelles les personnes sont suivies ou comptées. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre. Les événements sont émis lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 48 quand le type est zonecrossing et 16 quand l’heure est DwellTime. Il s’agit des valeurs recommandées pour obtenir une précision maximale. |
| `trigger`           | string     | Type de déclencheur pour l’envoi d’un événement. Les valeurs prises en charge sont event pour l’envoi d’événements lorsque le nombre change, ou interval pour envoyer des événements régulièrement, que le nombre ait changé ou non.                                                                                                                                                                                                                                                                                                                                                     |
| `focus`                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint.                                                                                                                                                                                                                                                                                               |
| `threshold`                | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `outputFrequency`           | int     | Vitesse à laquelle les événements sont émis. Quand outputFrequency = X, chaque événement X est émis, par exemple, outputFrequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre outputFrequency s’applique à la fois à event et à interval.                                                                                                                                                                                                                                                                                                                                                     |
| `minimumDistanceThreshold`  | float   | Distance en pieds qui déclenchera un événement « TooClose » lorsque les personnes sont moins éloignées que cette distance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `maximumDistanceThreshold`  | float   | Distance en pieds qui déclenchera un événement « TooFar » lorsque les personnes sont plus éloignées que cette distance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `aggregationMethod`         | string  | Méthode d’agrégation du résultat persondistance. aggregationMethod s’applique à la fois à mode et à average.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `enableFaceMaskClassifier`  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | Paramètres TRACKER_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

## <a name="person-count"></a>Nombre de personnes

**Identificateur d'opération** : `Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation`

Consultez un exemple d'[Opération de comptage de personnes](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json) dans notre exemple GitHub.

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `zones`                     | list    | Liste de zones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `name`                      | string  | Nom convivial de cette zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `polygon`                   | string  | Chaque paire de valeurs représente x et y pour les vertex d’un polygone. Le polygone représente les zones dans lesquelles les personnes sont suivies ou comptées. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre. Les événements sont émis lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 48 quand le type est zonecrossing et 16 quand l’heure est DwellTime. Il s’agit des valeurs recommandées pour obtenir une précision maximale. |
| `outputFrequency`           | int     | Vitesse à laquelle les événements sont émis. Quand outputFrequency = X, chaque événement X est émis, par exemple, outputFrequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre outputFrequency s’applique à la fois à event et à interval.                                                                                                                                                                                                                                                                                                                                                     |
| `trigger`                   | string  | Type de déclencheur pour l’envoi d’un événement. Les valeurs prises en charge sont event pour l’envoi d’événements lorsque le nombre change, ou interval pour envoyer des événements régulièrement, que le nombre ait changé ou non.                                                                                                                                                                                                                                                                                                                                                           |
| `focus`                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint.                                                                                                                                                                                                                                                                                               |
| `threshold`                 | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `enableFaceMaskClassifier`  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.                                                                                                                                                                                                                                                                                                        |
| `detectorNodeConfiguration` | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `trackerNodeConfiguration` | string  | Paramètres TRACKER_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

## <a name="custom-operation"></a>Opération personnalisée

**Identificateur d'opération** : `Microsoft.VideoAnalyzer.SpatialAnalysisCustomOperation`

Consultez un exemple d'[Opération de personnalisation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json) dans notre exemple GitHub.

#### <a name="parameters"></a>Paramètres :

| Nom                   | Type   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | string | Représentation JSON de l’opération. |

#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```
