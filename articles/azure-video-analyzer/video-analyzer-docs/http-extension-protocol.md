---
title: Protocole d’extension HTTP – Azure
description: Azure Video Analyzer vous permet d’étendre ses fonctionnalités de traitement via un nœud d’extension de pipeline. Le processeur d’extension HTTP permet des scénarios d’extensibilité à l’aide du protocole HTTP, où les performances et/ou l’utilisation optimale des ressources ne constituent pas la préoccupation principale.
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 0c87bf83692798fd416f9d416d1f2a217c99d40a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604847"
---
# <a name="use-the-http-extension-protocol"></a>Utiliser le protocole d’extension HTTP 

Azure Video Analyzer vous permet d’étendre ses fonctionnalités de traitement via un nœud d’[extension de pipeline](pipeline-extension.md). Le nœud processeur d’extension HTTP permet des scénarios d’extensibilité utilisant le protocole d’extension HTTP, dans lesquels les performances et/ou l’utilisation optimale des ressources ne constituent pas la préoccupation principale. Cet article explique comment utiliser ce protocole pour envoyer des messages entre le service Video Analyzer et un point de terminaison HTTP REST, qui serait généralement enveloppé autour d’un serveur d’inférence d’intelligence artificielle.

Le contrat HTTP est défini entre les deux composants suivants :

* Le serveur HTTP
* Le module Video Analyzer agit en tant que client HTTP

## <a name="http-contract"></a>Contrat HTTP

### <a name="request"></a>Requête

Les requêtes que le module Video Analyzer adresse à votre serveur HTTP seraient les suivantes :

|Clé|Valeur|
|---|---|
|POST|https://hostname/optional-path?optional-query|
|Acceptation|application/json|
|Autorisation| Basic, Digest, Bearer (par le biais de la prise en charge des en-têtes personnalisés)|
|Content-Type|  image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length : Longueur du corps, en octets   ||
|User-Agent. |Azure Media Services|
|body|  Octets d’image, encodés en binaire dans l’un des types de contenu pris en charge.|
```

### Example

```html
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>response

Les réponses de votre serveur d’inférence au module Video Analyzer devraient être les suivantes :

|Clé|   Valeur|
|---|----|
|Codes d’état|  200 OK - Résultats d’inférence trouvés<br/>204 Pas de contenu  –  Aucun résultat trouvé par l’intelligence artificielle<br/>400 Demande incorrecte - Inattendue<br/>500 Erreur interne du serveur - Inattendue<br/>503 Serveur occupé – Video Analyzer s’interrompt en fonction de l’en-tête « Retry-after » ou d’un intervalle de temps par défaut en l’absence d’en-tête.|
|Content-Type   |application/json|
|Content-Length |Longueur du corps, en octets|
|body|  Objet JSON avec une seule propriété « inferences ».|

### <a name="example"></a>Exemple

```html
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2021 04:44:01 GMT

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

Il est recommandé de retourner les réponses à l’aide de documents JSON valides, conformes au schéma préétabli défini comme étant le [modèle d’objet du schéma des métadonnées d’inférence](inference-metadata-schema.md). La conformité au schéma garantit l’interopérabilité avec d’autres composants dans Video Analyzer, par exemple, la possibilité de suivre des objets dans la vidéo en direct, et la superposition de métadonnées d’inférence sur la vidéo pendant la lecture, comme illustré [ici](record-stream-inference-data-with-video.md).

Si votre module retourne une réponse dans laquelle le type de contenu n’est pas « application/JSON », Video Analyzer encode le message sous forme de contenu en base 64 et le sérialise en tant que charge utile JSON opaque.

Si votre module retourne une réponse avec le type de contenu « application/JSON », mais que le schéma JSON ne suit pas le schéma des métadonnées d’inférence ci-dessus, la charge utile du message est transférée via le pipeline, mais l’interopérabilité est réduite.

> [!NOTE]
> Si votre serveur d’inférence ne produit aucun résultat pour une image donnée, il doit retourner le code d’état HTTP 204 (Pas de contenu) avec un corps de réponse vide. Le service Video Analyzer va l’interpréter comme un résultat vide et ne va pas transférer l’événement dans le pipeline.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez le protocole d’extension gRPC](grpc-extension-protocol.md)


