---
title: Comment spécifier un modèle de reconnaissance - API visage
titleSuffix: Azure Cognitive Services
description: Cet article montre comment choisir quel modèle de reconnaissance à utiliser avec votre application API visage de Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815823"
---
# <a name="specify-a-face-recognition-model"></a>Spécifier un modèle de reconnaissance faciale

Ce guide vous montre comment spécifier un modèle de reconnaissance faciale pour la détection de visage, l’identification et recherche de similarité à l’aide de l’API visage de Azure.

L’API visage utilise les modèles d’apprentissage automatique pour effectuer des opérations sur les visages humains dans des images. Nous continuons à améliorer la précision de nos modèles basés sur les commentaires des clients et des avances dans la recherche, et nous fournir ces améliorations en tant que mises à jour du modèle. Les développeurs ont la possibilité de spécifier la version du modèle de reconnaissance faciale il souhaite utiliser ; ils peuvent choisir le modèle qui convient le mieux à leurs cas d’usage.

Si vous êtes un nouvel utilisateur, nous vous recommandons de qu'utiliser le dernier modèle. Poursuivez votre lecture pour savoir comment spécifier dans différentes opérations de visage tout en évitant les conflits de modèle. Si vous êtes un utilisateur expérimenté et que vous ne savez pas si vous devez basculer vers le dernier modèle, passez à la [évaluer les différents modèles](#evaluate-different-models) section pour évaluer le nouveau modèle et comparer les résultats à l’aide de votre jeu de données actuel.

## <a name="prerequisites"></a>Conditions préalables

Vous devez connaître les concepts de détection de visage d’intelligence artificielle et d’identification. Si vous n’êtes pas, consultez tout d’abord ces guides de procédures :

* [Comment détecter des visages dans une image](HowtoDetectFacesinImage.md)
* [Comment identifier les visages dans une image](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Détectez les visages avec le modèle spécifié

Détection de visage identifie les points de repère visuel de visages humains et recherche de leurs emplacements de la zone englobante. Il extrait les fonctionnalités de la face et les stocke pour une utilisation dans l’identification. Toutes ces informations constitue la représentation d’une face.

Le modèle de reconnaissance est utilisé lorsque les caractéristiques de visage sont extraites, afin de pouvoir spécifier une version de modèle lors de l’exécution de l’opération de détection.

Lorsque vous utilisez le [doivent faire Face : détecter] API, affecter la version de modèle avec le `recognitionModel` paramètre. Les valeurs disponibles sont :

* `recognition_01`
* `recognition_02`

Si vous le souhaitez, vous pouvez spécifier le _returnRecognitionModel_ paramètre (par défaut **false**) pour indiquer si _recognitionModel_ doivent être retournées dans la réponse. Par conséquent, une URL de demande pour le [doivent faire Face : détecter] API REST se présentera comme suit :

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Si vous utilisez la bibliothèque cliente, vous pouvez affecter la valeur de `recognitionModel` en passant une chaîne représentant la version.
Si vous laisser non attribués, la version du modèle par défaut (_recognition_01_) sera utilisé. Consultez l’exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifiez les visages avec le modèle spécifié

L’API visage peuvent extraire des données de visage à partir d’une image et associez-la à une **personne** objet (via le [ajouter face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) appel d’API, par exemple) et plusieurs **personne** objets peuvent être stockées ensemble dans un **groupe de personnes**. Ensuite, un nouveau visage peut être comparé à un **groupe de personnes** (avec le [Visage - Identifier] appeler), et la personne correspondante au sein de ce groupe peut être identifiée.

A **groupe de personnes** doit avoir un seul modèle de reconnaissance unique pour tous les **personne**s et vous pouvez le spécifier à l’aide de la `recognitionModel` paramètre lorsque vous créez le groupe ([PersonGroup - Créer] ou [LargePersonGroup - créer]). Si vous ne spécifiez pas ce paramètre, la version d’origine `recognition_01` modèle est utilisé. Un groupe utilisent toujours le modèle de reconnaissance qu’il a été créé avec, et nouvelles faces deviendra associés à ce modèle quand ils sont ajoutés à ce dernier ; Cela ne peut pas être modifié après la création d’un groupe. Pour voir quel modèle un **groupe de personnes** est configuré avec, utilisez le [groupe de personnes - Get] API avec le _returnRecognitionModel_ paramètre défini en tant que **lavaleurtrue**.

Consultez l’exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Dans ce code, un **groupe de personnes** avec l’ID `mypersongroupid` est créé, et elle est configurée pour utiliser le _recognition_02_ modèle pour extraire des fonctionnalités de face.

En conséquence, vous devez spécifier quel modèle utiliser lors de la détection des visages à comparer à cette **groupe de personnes** (via le [doivent faire Face : détecter] API). Le modèle que vous utilisez doit toujours être cohérent avec la **groupe de personnes**de configuration ; sinon, l’opération échoue en raison des modèles incompatibles.

Aucune modification n’est dans le [Visage - Identifier] API ; vous devez uniquement spécifier la version du modèle de détection.

## <a name="find-similar-faces-with-specified-model"></a>Rechercher des visages similaires avec le modèle spécifié

Vous pouvez également spécifier un modèle de reconnaissance pour la recherche de similarité. Vous pouvez affecter la version du modèle avec `recognitionModel` lors de la création de la liste de face avec [FaceList - créer] API ou [LargeFaceList - créer]. Si vous ne spécifiez pas ce paramètre, la version d’origine `recognition_01` modèle est utilisé. Une liste de visages utilisent toujours le modèle de reconnaissance qu’il a été créé avec, et nouvelles faces deviendra associés à ce modèle quand ils sont ajoutés à ce dernier ; Cela ne peut pas être modifié après sa création. Pour voir quel modèle de liste face est configurée avec, utilisez le [FaceList - Get] API avec le _returnRecognitionModel_ paramètre défini en tant que **true**.

Consultez l’exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ce code crée une liste de visages appelée `My face collection`, en utilisant le _recognition_02_ modèle pour l’extraction des caractéristiques. Lorsque vous recherchez des visages similaires à un nouveau visage détecté dans cette liste face, auxquels sont confrontées doivent avoir été détectée ([doivent faire Face : détecter]) à l’aide de la _recognition_02_ modèle. Comme dans la section précédente, le modèle doit être cohérent.

Aucune modification n’est dans le [Visage - Rechercher similaire] API ; vous spécifiez uniquement la version du modèle de détection.

## <a name="verify-faces-with-specified-model"></a>Vérifiez les visages avec le modèle spécifié

Le [Doivent faire face : vérifier] API vérifie si deux visages appartiennent à la même personne. Il n’existe aucune modification de l’API de vérifier en ce qui concerne les modèles de reconnaissance, mais vous ne pouvez comparer les visages qui ont été détectées avec le même modèle. Par conséquent, les deux faces à la fois devrez ont été détectées à l’aide de `recognition_01` ou `recognition_02`.

## <a name="evaluate-different-models"></a>Évaluer des modèles différents

Si vous souhaitez comparer les performances de la _recognition_01_ et _recognition_02_ modèles sur vos données, vous devez :

1. Créez deux **groupe de personnes**s avec _recognition_01_ et _recognition_02_ respectivement.
1. Utiliser vos données d’image pour détecter les visages et les inscrire à **personne**s pour ces deux **groupe de personnes**s et la formation de déclencheur de processus avec [PersonGroup - Effectuer l’apprentissage] API.
1. Tester avec [Visage - Identifier] sur les deux **groupe de personnes**s et comparer les résultats.

Si vous spécifiez normalement un seuil de confiance (il s’agit d’une valeur comprise entre zéro et un qui détermine comment convaincus que le modèle doit être pour identifier un visage), vous devrez peut-être utiliser des seuils différents pour différents modèles. Un seuil pour un modèle n’est pas destiné à être partagé à un autre et ne génère pas nécessairement les mêmes résultats.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à spécifier le modèle de reconnaissance à utiliser avec les API de service Face différents. Ensuite, suivez un guide de démarrage rapide pour commencer à utiliser la détection de visages.

* [Détectez les visages dans une image](../quickstarts/csharp-detect-sdk.md)

[Doivent faire face : détecter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Visage - Rechercher similaire]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Visage - Identifier]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Doivent faire face : vérifier]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Créer]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Groupe de personnes - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Effectuer l’apprentissage]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Créer]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - créer]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - créer]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
