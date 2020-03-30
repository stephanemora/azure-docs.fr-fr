---
title: Extraire des entités de correspondance de texte - LUIS
titleSuffix: Azure Cognitive Services
description: Apprenez à ajouter une entité de liste pour aider LUIS à étiqueter les variations d’un mot ou d’une phrase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499474"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Utiliser une entité de liste pour améliorer la détection d’entité 
Cet article illustre l’utilisation d’une [entité de liste](luis-concept-entity-types.md) afin d’améliorer la détection d’entité. Les entités de liste n’ont pas besoin d’être étiquetées car elles correspondent parfaitement aux termes.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une entité de liste 
> * Ajouter des valeurs et synonymes normalisés
> * Valider l’identification améliorée d’entité

## <a name="prerequisites"></a>Conditions préalables requises

> [!div class="checklist"]
> * Dernière version de [Node.js](https://nodejs.org)
> * [Application LUIS HomeAutomation](luis-get-started-create-app.md). Si vous n’avez pas créé d’application Home Automation, créez une application et ajoutez-y le domaine prégénéré **HomeAutomation**. Former et publier l’application 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (si plusieurs requêtes), ID d’application, ID de version et [région](luis-reference-regions.md) pour l’application LUIS.

> [!Tip]
> Si vous n’avez pas encore d’abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).

Tout le code de cet article est disponible dans le [dépôt GitHub d’exemples Azure](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Utiliser l’application HomeAutomation
L’application HomeAutomation vous donne le contrôle d’appareils tels que l’éclairage, les systèmes de divertissement, mais aussi de l’environnement comme le chauffage et la climatisation. Ces systèmes ont plusieurs noms différents, dont les noms du fabricant, les surnoms, les acronymes et les noms familiers. 

Le thermostat est un système disposant de nombreux noms dans différentes régions et cultures. Un thermostat peut contrôler à la fois le chauffage et la climatisation dans une maison ou un immeuble.

Idéalement, les énoncés suivants doivent résoudre l’entité prédéfinie **HomeAutomation.Device** :

|#|Énoncé|entité identifiée|score|
|--|--|--|--|
|1|activer la climatisation|HomeAutomation.Device - "ac"|0.8748562|
|2|monter le chauffage|HomeAutomation.Device - "heat"|0.784990132|
|3|rafraîchir|||

Les deux premiers énoncés désignent différents services. Le troisième énoncé, « rafraîchir », n’indique pas un appareil mais un résultat. LUIS ne sait pas que le terme « rafraîchir » signifie que le thermostat est l’appareil demandé. Idéalement, LUIS doit résoudre tous ces énoncés sur le même appareil. 

## <a name="use-a-list-entity"></a>Utiliser une entité de liste
L’entité HomeAutomation.Device est idéale pour un petit nombre d’appareils ou pour des appareils avec peu de variations nominales. Pour un immeuble de bureaux, ou un campus, les noms d’appareil sont bien supérieurs à l’utilité de l’entité HomeAutomation.Device. 

Une **entité de liste** est un bon choix pour ce scénario, car l’ensemble de termes pour un appareil dans un immeuble ou un campus est connu, même s’il est important. En utilisant une entité de liste, LUIS peut recevoir n’importe quelle valeur possible dans l’ensemble pour le thermostat, et le réduire à l’appareil unique « thermostat ». 

Cet article va créer une liste d’entités avec le thermostat. Les noms possibles d’un thermostat dans cet article sont : 

|noms possibles pour thermostat|
|--|
| clim |
| a/c|
| a-c|
|chauffage|
|chaleur|
|chaud|
|frais|
|froid|

Si LUIS a souvent besoin de déterminer une alternative, alors une [liste de phrase](luis-concept-feature.md#how-to-use-phrase-lists) est préférable.

## <a name="create-a-list-entity"></a>Créer une entité de liste
Créez un fichier Node.js et copiez-y le code suivant. Modifiez les valeurs AuthoringKey, appid, versionId et region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Utilisez la commande suivante pour installer les dépendances NPM et exécutez le code pour créer l’entité de liste :

```console
npm install && node add-entity-list.js
```

La sortie de l’exécution représente l’ID de l’entité de liste :

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Effectuer l’apprentissage du modèle
Formez LUIS afin que la nouvelle liste y affecte les résultats de requête. La formation se fait en deux parties : la formation, puis la vérification. Une application disposant de nombreux modèles peut prendre un certain moment à être formée. Le code suivant forme l’application puis patiente jusqu’à la réalisation de la formation. Le code utilise une stratégie de nouvelle tentative afin d’éviter l’erreur 429 « Trop de requêtes ». 

Créez un fichier Node.js et copiez-y le code suivant. Modifiez les valeurs AuthoringKey, appid, versionId et region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Utilisez la commande suivante pour exécuter le code afin de former l’application :

```console
node train.js
```

Le statut de chaque itération de la formation des modèles LUIS est la sortie de l’exécution. L’exécution suivante n’a nécessité qu’une seule vérification de la formation :

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publier le modèle
Publiez le modèle afin que l’entité de liste soit disponible à partir du point de terminaison.

Créez un fichier Node.js et copiez-y le code suivant. Modifiez les valeurs endpointKey, appId et region. Vous pouvez utiliser la valeur authoringKey si vous ne prévoyez pas d’appeler ce fichier au-delà du quota.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Utilisez la commande suivante pour exécuter le code afin d’interroger l’application :

```console
node publish.js
```

La sortie suivante comprend l’URL du point de terminaison pour n’importe quelle requête. Les véritables résultats JSON devraient inclure la véritable valeur appID. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Interroger l’application 
Interrogez l’application depuis le point de terminaison pour prouver que l’entité de liste aide LUIS à déterminer le type d’appareil.

Créez un fichier Node.js et copiez-y le code suivant. Modifiez les valeurs endpointKey, appId et region. Vous pouvez utiliser la valeur authoringKey si vous ne prévoyez pas d’appeler ce fichier au-delà du quota.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Utilisez la commande suivante pour exécuter le code afin d’interroger l’application :

```console
node train.js
```

Vous obtenez les résultats de la requête. Comme le code a ajouté la paire nom/valeur **verbose** à la chaîne de requête, la sortie inclut toutes les intentions et leurs scores :

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Le périphérique spécifique **Thermostat** est identifié avec une requête de résultat « monter le chauffage ». Comme l’entité d’origine HomeAutomation.Device est toujours dans l’application, vous pouvez aussi consulter ses résultats. 

Essayez les deux autres énoncés pour voir qu’ils sont aussi retournés comme résultat pour thermostat. 

|#|Énoncé|entité|type|value|
|--|--|--|--|--|
|1|activer la climatisation| clim | DevicesList | Thermostat|
|2|monter le chauffage|chauffage| DevicesList |Thermostat|
|3|rafraîchir|froid|DevicesList|Thermostat|

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez créer une autre entité de liste pour étendre les emplacements d’appareil à des pièces, des étages ou des bâtiments. 
