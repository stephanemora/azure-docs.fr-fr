---
title: Schéma des métadonnées d’inférence – Azure
description: Dans Azure Video Analyzer, chaque objet d’inférence, qu’un contrat basé sur HTTP ou sur gRPC soit utilisé, suit le modèle objet décrit dans cette rubrique.
ms.topic: reference
ms.date: 04/11/2021
ms.openlocfilehash: 4400978fca931741a1a12531d7923b82a5ead3e5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386247"
---
# <a name="inference-metadata-schema"></a>Schéma des métadonnées d’inférence 

Dans Azure Video Analyzer, chaque objet d’inférence, qu’un contrat basé sur HTTP ou sur gRPC soit utilisé, suit le modèle objet décrit dans cette rubrique.

## <a name="object-model"></a>Modèle objet

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/inference-metadata-schema/object-model.png" alt-text="Modèle Oot Azure Video Analyzer" lightbox="./media/inference-metadata-schema/object-model.png":::

 
|Définition de type|Description|
|---|---|
|Tag|Balise ou étiquette associée au résultat. En plus du balisage, vous obtenez également la valeur de confiance associée à l’étiquette.|
|Attribut|Attributs supplémentaires associés au résultat. Vous pouvez ajouter de nouveaux attributs que vous recevez du moteur d’inférence à la valeur de confiance.|
|Liste d’attributs|Liste des attributs facultatifs.|
|Rectangle|Région rectangulaire relative à l’angle supérieur gauche de l’image. Les propriétés requises sont « length », « width », « height » et « top edge distance from the origin », qui correspondent aux longueur, largeur, hauteur et distance du bord supérieur à l’origine.|
|classification ;|Étiquette du classifieur souvent applicable à l’échantillon entier. Avec l’aide de « tag », vous pouvez classifier le résultat.|
|Entité|Entité détectée ou identifiée sur l’échantillon. Lorsqu’une entité est détectée par le moteur d’inférence, elle obtient une balise (« tag »), des attributs supplémentaires qui ont été déduits et les coordonnées d’une zone rectangulaire autour de l’entité trouvée.|
|Événement|Événement détecté sur l’échantillon. Lorsqu’un événement est détecté dans l’échantillon, le nom de l’événement et les propriétés spécifiques à l’événement sont retournés.|
|Mouvement|Mouvement détecté sur l’échantillon. Lorsqu’un mouvement est détecté dans l’échantillon, les coordonnées d’un cadre englobant rectangulaire où le mouvement a été détecté sont retournées.|
|Text|Le texte associé à l’échantillon, ainsi que l’horodateur de début et de fin de ce texte sont retournés.|
|Autres|Retourne d’autres informations génériques sur la charge utile.|

L’exemple ci-dessous contient un événement unique avec certains types d’inférence pris en charge :

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Étapes suivantes

- [Contrat de données gRPC](./grpc-extension-protocol.md)
- [Contrat de données HTTP](./http-extension-protocol.md)