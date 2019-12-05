---
title: Guide pratique pour spécifier un modèle de reconnaissance - API Visage
titleSuffix: Azure Cognitive Services
description: Cet article vous montrera comment choisir le modèle de reconnaissance à utiliser avec votre application API Visage Azure.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: 5b84e078e3b674a539b61c07c4bb4370719e4799
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771017"
---
# <a name="specify-a-face-recognition-model"></a>Spécifier un modèle de reconnaissance faciale

Ce guide vous montre comment spécifier un modèle de reconnaissance faciale pour la détection de visages, l'identification et la recherche de similarités en utilisant l'API Visage Azure.

L’API Visage utilise des modèles d’apprentissage automatique pour effectuer des opérations sur les visages humains dans des images. Nous continuons d’améliorer la précision de nos modèles en fonction des commentaires de nos clients et des progrès de la recherche, et nous intégrons ces améliorations sous forme de mises à jour de modèles. Les développeurs peuvent spécifier la version du modèle de reconnaissance faciale qu'ils souhaitent utiliser ; ils peuvent choisir le modèle qui correspond le mieux à leur cas d'utilisation.

Si vous êtes un nouvel utilisateur, nous vous recommandons d'utiliser le dernier modèle. Poursuivez la lecture pour découvrir comment spécifier ce modèle dans différentes opérations de détection de visages, tout en évitant les conflits de modèles. Si vous êtes un utilisateur avancé et hésitez à passer au modèle le plus récent, passez à la section [Évaluer des modèles différents](#evaluate-different-models) pour évaluer le nouveau modèle et comparer les résultats en utilisant votre ensemble de données actuel.

## <a name="prerequisites"></a>Prérequis

Vous devez maîtriser les concepts de la détection et de l'identification de visages par intelligence artificielle (AI). Si ce n’est pas les cas, consultez d’abord ces guides pratiques :

* [Comment détecter des visages dans une image](HowtoDetectFacesinImage.md)
* [Comment identifier des visages dans une image](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Détecter des visages avec le modèle spécifié

La détection des visages identifie les repères visuels des visages humains et trouve leur emplacement dans les cadres englobants. Elle extrait également les traits du visage et les stocke pour les utiliser à des fins d’identification. Toutes ces informations forment la représentation d’un visage.

Le modèle de reconnaissance est utilisé lors de l’extraction des traits du visage, de sorte que vous pouvez spécifier une version du modèle lors de l’exécution de l'opération de détection.

Lorsque vous utilisez l’API [Face - Detect], attribuez la version du modèle avec le paramètre `recognitionModel`. Les valeurs disponibles sont :

* `recognition_01`
* `recognition_02`

Si vous le souhaitez, vous pouvez spécifier le paramètre _returnRecognitionModel_ (par défaut **false**) pour indiquer si _recognitionModel_ doit être retourné comme réponse. Ainsi, une URL de requête pour l’API REST [Face - Detect] se présentera comme suit :

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Si vous utilisez la bibliothèque cliente, vous pouvez attribuer la valeur de `recognitionModel` en passant une chaîne représentant la version.
Si vous n’attribuez aucune valeur, la version du modèle par défaut (_recognition_01_) sera utilisée. Consultez l'exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifier des visages avec le modèle spécifié

L'API Visage peut extraire des données de visage d’une image et les associer à un objet **Person** (via l'appel d’API [Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), par exemple), et plusieurs objets **Personne** peuvent être stockés ensemble dans un objet **PersonGroup**. Ensuite, un nouveau visage peut être comparé à un objet **PersonGroup** (avec l'appel [Visage - Identifier]), et la personne correspondante dans ce groupe peut être identifiée.

Un objet **PersonGroup** doit avoir un modèle de reconnaissance unique pour toutes les **personnes**, et vous pouvez le spécifier en utilisant le paramètre `recognitionModel` lorsque vous créez le groupe ([PersonGroup - Créer] ou [LargePersonGroup - Créer]). Si vous ne spécifiez pas ce paramètre, le modèle original `recognition_01` est utilisé. Un groupe utilisera toujours le modèle de reconnaissance avec lequel il a été créé, et de nouveaux visages seront associés à ce modèle lorsqu'ils y seront ajoutés ; ceci ne peut pas être modifié après la création d’un groupe. Pour voir avec quel modèle un objet **PersonGroup** est configuré, utilisez l'API [PersonGroup - Get] avec le jeu de paramètres _returnRecognitionModel_ défini sur **true**.

Consultez l'exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Dans ce code, un objet **PersonGroup** avec l’ID `mypersongroupid` est créé, et il est configuré pour utiliser le modèle _recognition_02_ afin d’extraire les traits du visage.

En conséquence, vous devez spécifier quel modèle utiliser lors de la détection des visages à comparer à cet objet **PersonGroup** (via l’API [Face - Detect]). Le modèle que vous utilisez doit toujours être cohérent avec la configuration de l’objet **PersonGroup** ; sinon, l'opération échouera en raison de modèles incompatibles.

Aucun changement n’est apporté à l'API [Visage - Identifier] ; il vous suffit de spécifier la version du modèle lors de la détection.

## <a name="find-similar-faces-with-specified-model"></a>Rechercher des visages avec le modèle spécifié

Vous pouvez également spécifier un modèle de reconnaissance pour la recherche de similarités. Vous pouvez attribuer la version du modèle avec `recognitionModel` lors de la création de la liste des visages avec l’API [FaceList - Create] ou [LargeFaceList - Create]. Si vous ne spécifiez pas ce paramètre, le modèle original `recognition_01` est utilisé. Une liste de visages utilisera toujours le modèle de reconnaissance avec lequel il a été créé, et de nouveaux visages seront associés à ce modèle lorsqu'ils y seront ajoutés ; ceci ne peut pas être modifié après la création d’une liste. Pour voir avec quel modèle une liste de visages est configurée, utilisez l'API [FaceList - Get] avec le jeu de paramètres _returnRecognitionModel_ défini sur **true**.

Consultez l'exemple de code suivant pour la bibliothèque cliente .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Ce code crée une liste de visages appelée `My face collection`, en utilisant le modèle _recognition_02_ pour l’extraction des traits de visage. Lorsque vous recherchez dans cette liste des visages similaires à un nouveau visage détecté, ce visage doit avoir été détecté ([Face - Detect]) en utilisant le modèle _recognition_02_. Comme dans la section précédente, le modèle doit être cohérent.

Aucun changement n’est apporté à l'API [Face - Find Similar] ; vous spécifiez uniquement la version du modèle lors de la détection.

## <a name="verify-faces-with-specified-model"></a>Vérifier des visages avec le modèle spécifié

L’API [Face - Verify] évalue si deux visages appartiennent à la même personne. Aucun changement n’est apporté à l'API Verify au niveau des modèles de reconnaissance, mais vous pouvez uniquement comparer des visages qui ont été détectés avec le même modèle. Ainsi, les deux visages devront avoir été détectés avec `recognition_01` ou `recognition_02`.

## <a name="evaluate-different-models"></a>Évaluer des modèles différents

Si vous souhaitez comparer les performances des modèles _recognition_01_et _recognition_02_ sur vos données, vous devrez :

1. Créer deux objets **PersonGroup** avec _recognition_01_ et _recognition_02_, respectivement.
1. Utiliser vos données d’image pour détecter les visages et les enregistrer dans l’objet **Person** pour ces deux objets **PersonGroup**, et déclencher le processus de formation avec l’API [PersonGroup - Effectuer l’apprentissage].
1. Effectuer un test avec l’API [Visage - Identifier] sur les deux objets **PersonGroup** et comparer les résultats.

Si vous spécifiez normalement un seuil de confiance (une valeur comprise entre zéro et une valeur qui détermine le degré de confiance que doit avoir le modèle pour identifier un visage), vous devrez peut-être utiliser différents seuils pour différents modèles. Le seuil d’un modèle n'est pas destiné à être partagé avec un autre modèle, et il ne produira pas nécessairement les mêmes résultats.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment spécifier le modèle de reconnaissance à utiliser avec différentes API du service Visage. Voici maintenant un guide de démarrage rapide pour commencer à utiliser la détection des visages.

* [SDK .NET Visage](../Quickstarts/csharp-sdk.md)
* [Kit de développement logiciel (SDK) Face Python](../Quickstarts/python-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Visage - Identifier]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Créer]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Effectuer l’apprentissage]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Créer]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
