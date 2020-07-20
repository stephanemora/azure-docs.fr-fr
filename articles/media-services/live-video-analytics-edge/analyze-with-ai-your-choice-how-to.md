---
title: Analyser la vidéo en direct avec l’IA de votre choix - Azure
description: Dans cet article, vous allez apprendre à créer un module IoT Edge intégrable à Live Video Analytics sur IoT Edge pour analyser la vidéo en direct à l’aide d’un modèle de vision par ordinateur de votre choix.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6a1ea3ebd8c7de4c691d7a982dbc08e9d08d9e38
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182863"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analyser les vidéo en direct avec l’IA de votre choix

Dans cet article, vous allez apprendre à créer un module IoT Edge intégrable à Live Video Analytics sur IoT Edge pour analyser la vidéo en direct à l’aide d’un modèle de vision par ordinateur de votre choix. 

## <a name="pre-reading"></a>Prélecture

[Concept de graphe multimédia](media-graph-concept.md)

## <a name="media-graph-extension"></a>Extension de graphe multimédia

Live Video Analytics sur IoT Edge définit un modèle d’extensibilité qui vous permet d’étendre les fonctionnalités de traitement des graphes multimédias à vos propres composants d’analytique multimédia par le biais d’une extension de graphe. Votre composant d’analytique peut utiliser des techniques de traitement d’image traditionnelles ou des modèles d’IA de vision par ordinateur. Les extensions de graphe sont activées en incluant le nœud du [processeur d’extension HTTP](media-graph-concept.md#http-extension-processor) dans un graphe multimédia. Le processeur d’extension HTTP peut relayer des images vidéo à un point de terminaison HTTP spécifié par vos soins et servir d’interface avec votre composant par ce biais. La connexion peut être établie à un point de terminaison local ou distant. Elle peut être sécurisée par une authentification et un chiffrement TLS, si nécessaire. De plus, le processeur autorise une mise à l’échelle et un encodage facultatifs des images vidéo avant leur relais.

Vous pouvez choisir d’exécuter le modèle d’inférence de votre choix sur un runtime d’inférence disponible, comme ONNX, TensorFlow, PyTorch, etc. sur votre propre conteneur Docker. Vous pouvez également utiliser le langage de programmation et les bibliothèques de votre choix pour exposer les fonctionnalités d’inférence d’image par le biais d’un serveur HTTP sur votre propre conteneur. Le conteneur d’inférence doit être déployé en même temps que le module Live Video Analytics pour obtenir de meilleures performances, puis il est appelé par le biais du processeur d’extension HTTP inclus dans la topologie de votre graphe.
De plus, la fréquence des appels dans votre module personnalisé peut être limitée en ajoutant éventuellement un [processeur de détection de mouvement](media-graph-concept.md#motion-detection-processor) et un [processeur de filtre de fréquence d’images ](media-graph-concept.md#frame-rate-filter-processor) en amont au processeur d’extension HTTP.

Le diagramme ci-dessous illustre le flux de données général :

![Appareil de périphérie](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Ainsi, vous pouvez analyser la vidéo à l’aide des modèles IA de votre choix pour répondre à vos besoins métier uniques. Les modèles IA peuvent provenir de la communauté open source, de vos propres scientifiques des données ou d’un fournisseur IA spécialisé.

## <a name="media-graph-http-extension-contract-definitions"></a>Définitions du contrat d’extension HTTP de graphe multimédia

Cette section définit le contrat HTTP qui définit le flux de données.

### <a name="http-extensibility-protocol"></a>Protocole d’extensibilité HTTP  

Le contrat HTTP se définit comme suit :

* Votre module sert de serveur HTTP.
* Le modèle Live Video Analytics sur IoT Edge joue le rôle de client HTTP.

### <a name="request"></a>Requête

Les requêtes provenant du module Live Video Analytics adressées à votre module sont les suivantes :

| Clé | Valeur |
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Acceptation|application/json, */*|
|Autorisation| Basic, Digest, Bearer (par le biais de la prise en charge des en-têtes personnalisés)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length|Longueur du corps, en octets|
|User-Agent.|Azure Media Services|
|body|Octets d’image, encodés en binaire dans l’un des types de contenu pris en charge.|

#### <a name="example"></a>Exemple

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>response

Les réponses de votre module au module Live Video Analytics doivent être les suivantes :

| Clé | Valeur |
|---|---|
|Codes d’état|200 OK - Résultats d’inférence trouvés<br/>204 Pas de contenu - Aucun contenu trouvé par l’IA<br/>400 Demande incorrecte - Inattendue<br/>500 Erreur interne du serveur - Inattendue<br/>503 Serveur occupé - AMS se retire en fonction de l’en-tête « Retry-after » ou d’un intervalle de temps par défaut en cas d’en-tête non prédéfini.|
|Content-Type|application/json|
|Content-Length|    Longueur du corps, en octets|
|body|Objet JSON avec une seule propriété « inferences ».|

#### <a name="example"></a>Exemple

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Il est fortement recommandé de retourner les réponses à l’aide de documents JSON valides conformes au schéma pré-établi défini ci-dessous. L’interopérabilité avec d’autres composants et les futures fonctionnalités ajoutées au module Live Video Analytics est ainsi mieux garantie.

Si votre module retourne une réponse dans laquelle le type de contenu n’est pas « application/JSON », Live Video Analytics encode le message sous forme de contenu en base 64 et le sérialise en tant que charge utile JSON opaque.

Si votre module retourne une réponse avec le type de contenu « application/JSON », mais que le schéma JSON ne suit pas le [schéma des métadonnées d’inférence décrit ci-dessous](#inference-metadata-schema), la charge utile du message est transférée par le biais du pipeline, mais l’interopérabilité est réduite.

> [!NOTE]
> Si votre module ne produit aucun résultat, il doit retourner le code d’état HTTP 204 (Pas de contenu) avec un corps de réponse vide. Live Video Analytics va l’interpréter comme un résultat vide et ne va pas transférer l’événement dans le pipeline.

### <a name="inference-metadata-schema"></a>Schéma des métadonnées d’inférence

Chaque objet d’inférence suit le schéma de ce sur-ensemble :

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Contrats de données - hiérarchie des classes

![Contrats de données - hiérarchie des classes](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Exemples  

L’exemple ci-dessous contient un événement unique avec tous les types d’inférence pris en charge :

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Exemples de modules d’extension HTTP

Quelques exemples de modules d’extension HTTP sont disponibles dans le [dépôt GitHub de Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). L’un de ces [exemples d’analyse vidéo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) montre comment utiliser le modèle [YOLOv3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) pour générer un module IoT Edge pour la détection d’objets. Un autre [exemple d’analyse vidéo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx-tiny) montre comment utiliser Tiny YOLOv3, une version allégée du modèle YOLOv3 ONNX. Vous pouvez utiliser la même approche pour créer votre propre module avec un modèle d’IA de votre choix.

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes](troubleshoot-how-to.md)
