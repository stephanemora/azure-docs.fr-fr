---
title: Comment spécifier un modèle de détection - API visage
titleSuffix: Azure Cognitive Services
description: Cet article montre comment choisir quel modèle de détection de visage à utiliser avec votre application API visage de Azure.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576696"
---
# <a name="specify-a-face-detection-model"></a>Spécifiez un modèle de détection de visage

Ce guide vous montre comment spécifier un modèle de détection de visage pour l’API visage de Azure.

L’API visage utilise les modèles d’apprentissage automatique pour effectuer des opérations sur les visages humains dans des images. Nous continuons à améliorer la précision de nos modèles basés sur les commentaires des clients et des avances dans la recherche, et nous fournir ces améliorations en tant que mises à jour du modèle. Les développeurs ont la possibilité de spécifier la version du modèle de détection de visage il souhaite utiliser ; ils peuvent choisir le modèle qui convient le mieux à leurs cas d’usage.

Poursuivez votre lecture pour savoir comment spécifier le modèle de détection de visage dans certaines opérations de face. L’API visage utilise la détection de visage chaque fois qu’il convertit une image d’un visage en une autre forme de données.

Si vous ne savez pas si vous devez utiliser le dernier modèle, passez à la [évaluer les différents modèles](#evaluate-different-models) section pour évaluer le nouveau modèle et comparer les résultats à l’aide de votre jeu de données actuel.

## <a name="prerequisites"></a>Conditions préalables

Vous devez connaître avec le concept de détection de visage d’intelligence artificielle. Si vous n’êtes pas, consultez le guide conceptuel de détection de visage ou le guide de procédures :

* [Concepts de détection de visage](../concepts/face-detection.md)
* [Comment détecter des visages dans une image](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Détectez les visages avec le modèle spécifié

Détection de visage recherche les emplacements de la zone englobante de visages humains et identifie leurs points de repère visuel. Extrait les fonctionnalités de la face et les stocke pour une utilisation ultérieure dans [reconnaissance](../concepts/face-recognition.md) operations.

Lorsque vous utilisez le [doivent faire Face : détecter] API, vous pouvez affecter la version du modèle avec le `detectionModel` paramètre. Les valeurs disponibles sont :

* `detection_01`
* `detection_02`

Une URL de demande pour le [doivent faire Face : détecter] API REST se présentera comme suit :

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

Si vous utilisez la bibliothèque cliente, vous pouvez affecter la valeur de `detectionModel` en passant une chaîne appropriée. Si vous laissez pas attribuées, l’API utilise la version de modèle par défaut (`detection_01`). Consultez l’exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Ajouter face à personne avec le modèle spécifié

L’API visage peuvent extraire des données de visage à partir d’une image et associez-la à une **personne** via la [personne du groupe de personnes - ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. Dans cet appel d’API, vous pouvez spécifier le modèle de détection de la même façon que dans [doivent faire Face : détecter].

Consultez l’exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Ce code crée un **groupe de personnes** avec l’ID `mypersongroupid` et ajoute un **personne** à celui-ci. Puis il ajoute une Face à ce **personne** à l’aide de la `detection_02` modèle. Si vous ne spécifiez pas le *detectionModel* paramètre, l’API utilise le modèle par défaut, `detection_01`.

> [!NOTE]
> Vous n’avez pas besoin d’utiliser le même modèle de détection pour tous les visages dans une **personne** objet et vous n’avez pas besoin d’utiliser le même modèle de détection lors de la détection des visages de nouveau à comparer à un **personne** objet (dans le [Visage - Identifier] API, par exemple).

## <a name="add-face-to-facelist-with-specified-model"></a>Ajouter face à FaceList avec le modèle spécifié

Vous pouvez également spécifier un modèle de détection lorsque vous ajoutez une face à un existant **FaceList** objet. Consultez l’exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Ce code crée un **FaceList** appelé `My face collection` et lui ajoute un visage avec le `detection_02` modèle. Si vous ne spécifiez pas le *detectionModel* paramètre, l’API utilise le modèle par défaut, `detection_01`.

> [!NOTE]
> Vous n’avez pas besoin d’utiliser le même modèle de détection pour tous les visages dans une **FaceList** objet et vous n’avez pas besoin d’utiliser le même modèle de détection lors de la détection des visages de nouveau à comparer à un **FaceList** objet.

## <a name="evaluate-different-models"></a>Évaluer des modèles différents

Les modèles de détection de visage différents sont optimisés pour différentes tâches. Consultez le tableau suivant pour une vue d’ensemble des différences.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Choix par défaut pour toutes les opérations de détection de visage. | Publié en mai 2019 et disponible si vous le souhaitez dans toutes les opérations de détection de visage.
|Pas optimisée pour les petites, latérale ou faces floues.  | Amélioration de la précision sur petit, latérale et visages floues. |
|Retourne doivent faire face les attributs (pose principal, âge, émotion et ainsi de suite) si elles sont spécifiées dans l’appel de détecter. |  Ne retourne pas les attributs de visage.     |
|Retourne doivent faire face les points de repère si elles sont spécifiées dans l’appel de détecter.   | Ne retourne pas de points de repère de face.  |

La meilleure façon de comparer les performances de la `detection_01` et `detection_02` modèles consiste à les utiliser sur un échantillon de dataset. Nous vous recommandons d’appeler le [doivent faire Face : détecter] API sur un grand nombre d’images, en particulier les images de nombreuses facettes ou des faces qui sont difficiles à voir, à l’aide de chaque modèle de détection. Faites attention au nombre de visages qui retourne chaque modèle.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à spécifier le modèle de détection à utiliser avec différentes API visage. Ensuite, suivez un guide de démarrage rapide pour commencer à utiliser la détection de visages.

* [Détectez les visages dans une image (SDK .NET)](../quickstarts/csharp-detect-sdk.md)

[Doivent faire face : détecter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Visage - Identifier]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc