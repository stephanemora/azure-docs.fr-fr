---
title: Appeler l’API Émotion pour la vidéo | Microsoft Docs
description: Découvrez comment appeler l’API Émotion pour la vidéo dans Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368552"
---
# <a name="how-to-call-emotion-api-for-video"></a>Guide pratique pour appeler l’API Émotion pour la vidéo

> [!IMPORTANT]
> La préversion de l’API Vidéo a pris fin le 30 octobre 2017. Essayez la nouvelle [préversion de l’API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) pour extraire facilement des insights des vidéos et améliorer les expériences de découverte de contenu, telles que les résultats de recherche, en détectant le texte parlé, les visages, les personnes et les émotions. [Plus d’informations](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)

Ce guide montre comment appeler l’API Émotion pour la vidéo. Les exemples sont écrits en C# à l’aide de la bibliothèque cliente de l’API Émotion pour la vidéo.

### <a name="Prep">Préparation</a> 
Pour pouvoir utiliser l’API Émotion pour la vidéo, vous avez besoin d’une vidéo qui inclut des personnes, de préférence faisant face à la caméra.

### <a name="Step1">Étape 1 : Autoriser l’appel d’API</a> 
Chaque appel à l’API Émotion pour la vidéo nécessite une clé d’abonnement. Cette clé doit être transmise par le biais d’un paramètre de chaîne de requête ou spécifiée dans l’en-tête de requête. Pour transmettre la clé d’abonnement par le biais d’une chaîne de requête, reportez-vous à l’URL de requête ci-dessous pour l’API Émotion pour la vidéo en guise d’exemple :

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Vous pouvez également spécifier la clé d’abonnement dans l’en-tête de requête HTTP :

```
ocp-apim-subscription-key: <Your subscription key>
```

Quand vous utilisez une bibliothèque cliente, la clé d’abonnement est transmise par le biais du constructeur de la classe VideoServiceClient. Par exemple : 

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Pour obtenir une clé d’abonnement, consultez [Abonnements] (https://azure.microsoft.com/try/cognitive-services/). 

### <a name="Step2">Étape 2 : Charger une vidéo sur le service et vérifier l’état</a>
La façon la plus simple d’effectuer un appel d’API Émotion pour la vidéo consiste à charger une vidéo directement. Pour cela, vous devez envoyer une requête « POST » avec le type de contenu application/octet-stream ainsi que les données lues à partir d’un fichier vidéo. La taille maximale de la vidéo est de 100 Mo.

À l’aide de la bibliothèque cliente, la stabilisation au moyen du chargement est effectuée en passant un objet de flux. Prenons l'exemple suivant :

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Notez que la méthode CreateOperationAsync de VideoServiceClient est asynchrone. La méthode appelante doit également être marquée comme asynchrone pour pouvoir utiliser la clause await.
Si la vidéo est déjà sur le web et a une URL publique, l’API Émotion pour la vidéo est accessible en fournissant l’URL. Dans cet exemple, le corps de la requête est une chaîne JSON qui contient l’URL.

À l’aide de la bibliothèque cliente, la stabilisation au moyen d’une URL peut facilement être exécutée à l’aide d’une autre surcharge de la méthode CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Cette méthode de chargement sera la même pour tous les appels d’API Émotion pour la vidéo. 

Une fois que vous avez chargé une vidéo, l’opération suivante consiste à vérifier son état. Les fichiers vidéo étant généralement plus volumineux et plus variés que les autres fichiers, les utilisateurs peuvent s’attendre à un temps de traitement long durant cette étape. La durée dépend de la taille et de la longueur du fichier.

À l’aide de la bibliothèque cliente, vous pouvez récupérer l’état et le résultat de l’opération à l’aide de la méthode GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
En règle générale, le client doit récupérer régulièrement l’état de l’opération jusqu’à ce que l’état affiché soit « Succès » ou « Échec ».

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Quand l’état de VideoOperationResult est « Succès », le résultat peut être récupéré en effectuant un cast de VideoOperationResult en VideoOperationInfoResult<VideoAggregateRecognitionResult> et en accédant au champ ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Étape 3 : Récupération et compréhension de la reconnaissance d’émotion, et suivi de la sortie JSON</a>

Le fichier de sortie au format JSON contient les métadonnées des visages détectés.

Comme expliqué à l’étape 2, la sortie JSON est disponible dans le champ ProcessingResult d’OperationResult, quand son état est « Succès ».

La détection et le suivi des visages JSON incluent les attributs suivants :

Attribut | Description
-------------|-------------
Version | Fait référence à la version du code JSON de l’API Émotion pour la vidéo.
Timescale | « Cycles » par seconde de la vidéo.
Offset  |Décalage des horodatages. Dans la version 1.0 de l’API Émotion pour la vidéo, ce sera toujours 0. Cette valeur est susceptible de changer dans les scénarios pris en charge ultérieurement.
Framerate | Images par seconde de la vidéo.
Fragments   | Les métadonnées sont découpées en différents éléments plus petits appelés fragments. Chaque fragment contient des valeurs de début (start), de durée (duration), un numéro d’intervalle et des événements (event).
Start   | Heure de début du premier événement, en « cycles ».
Duration |  Durée du fragment en « cycles ».
Interval |  Longueur de chaque événement du fragment, en « cycles ».
Événements  | Tableau d’événements. Le tableau externe représente un intervalle de temps. Le tableau interne se compose de 0 événement ou plus ayant eu lieu à ce moment précis.
windowFaceDistribution |    Pourcentage des visages ayant une émotion particulière lors de l’événement.
windowMeanScores |  Scores moyens pour chaque émotion des visages de l’image.

La raison de cette mise en forme du JSON est la configuration des API pour les scénarios ultérieurs, où il sera important de récupérer rapidement des métadonnées et de gérer un flux volumineux de résultats. Cette mise en forme utilise les techniques de fragmentation (qui vous permet de fragmenter les métadonnées en parties chronologiques, où vous pouvez télécharger uniquement ce dont vous avez besoin) et de segmentation (qui vous permet de fragmenter les événements s’ils sont trop grands). Des calculs simples peuvent vous aider à transformer les données. Par exemple, si un événement a démarré à 6300 (cycles), avec une échelle de temps de 2997 (cycles par seconde) et si la fréquence d’images est de 29,97 (images par seconde), alors :

*   Démarrage/Échelle de temps = 2,1 secondes
*   Secondes x (fréquence d’images/échelle de temps) = 63 images

Voici un exemple simple d’extraction du JSON en format « par image » pour la détection faciale et le suivi :

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Les émotions étant lissées au fil du temps, si vous générez une visualisation pour superposer vos résultats par-dessus la vidéo d’origine, soustrayez 250 millisecondes des horodatages fournis.

### <a name="Summary">Résumé</a>
Dans ce guide, vous avez découvert les fonctionnalités de l’API Émotion pour la vidéo : comment charger une vidéo, vérifier son état et récupérer les métadonnées de reconnaissance d’émotion.

Pour plus de détails sur l’API, consultez le guide de référence d’API « [Informations de référence sur l’API Émotion pour la vidéo](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e) ».
