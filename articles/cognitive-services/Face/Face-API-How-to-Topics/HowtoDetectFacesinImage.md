---
title: Détectez les visages dans une image - API visage
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les différentes données retournées par la fonctionnalité de détection de visage.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588769"
---
# <a name="get-face-detection-data"></a>Obtenir des données de détection de visage

Ce guide va vous montrer comment utiliser la détection de visage pour extraire les attributs tels que le sexe, d’âge ou pose à partir d’une image donnée. Les extraits de code dans ce guide sont écrits en C# à l’aide de la bibliothèque cliente API visage, mais la même fonctionnalité est disponible via le [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Ce guide vous explique comment pour :

- Obtenir les emplacements et les dimensions de visages dans une image.
- Obtenir les emplacements des différents points de repère face (élèves nez, bouche et ainsi de suite) dans une image.
- Deviner le sexe, âge, émotion et des autres attributs de visage détecté.

## <a name="setup"></a>Paramétrage

Ce guide part du principe que vous avez déjà construit un **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objet, nommé `faceClient`, avec une URL de clé et le point de terminaison de l’abonnement Face. À ce stade, vous pouvez utiliser la fonctionnalité de détection de visage en appelant **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (utilisé dans ce guide) ou **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Consultez le [Guide de démarrage rapide pour détecter les Faces C# ](../quickstarts/csharp-detect-sdk.md) pour obtenir des instructions sur la façon de configurer ce paramètre.

Ce guide met l’accent sur les spécificités de l’appel de détecter&mdash;les arguments que vous pouvez passer et ce que vous pouvez faire avec les données retournées. Nous vous recommandons d’interrogation uniquement pour les fonctionnalités que vous avez besoin, car chaque opération prend plus de temps pour terminer.

## <a name="get-basic-face-data"></a>Obtenir des données de base face

Pour rechercher des visages et obtenir leurs emplacements dans une image, appelez la méthode avec le _returnFaceId_ paramètre défini sur **true** (valeur par défaut).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Retourné **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objets peuvent être interrogées pour leurs identificateurs uniques et un rectangle qui donne les coordonnées en pixels de la face.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Consultez **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** pour plus d’informations sur la façon d’analyser l’emplacement et les dimensions de la face. En règle générale, ce rectangle contient les yeux, sourcils, nez et bouche ; haut de la tête, oreilles et chin ne sont pas nécessairement inclus. Si vous envisagez d’utiliser le rectangle de face pour rogner une tête complète ou la capture d’intermédiaire portrait (une photo ID type image), vous souhaiterez étendez le rectangle par une certaine marge dans chaque direction.

## <a name="get-face-landmarks"></a>Obtenir les points de repère de visage

Points de repère face sont un ensemble de points facile à trouver sur un visage telles que les élèves ou de l’extrémité du nez. Vous pouvez obtenir des données d’éléments géographiques face en définissant le _returnFaceLandmarks_ paramètre **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Il existe 27 points de repères prédéfinis par défaut. La figure suivante montre tous les points de 27 :

![Un diagramme de face avec tous les points de 27 repère étiquetés](../Images/landmarks.1.jpg)

Les points renvoyés sont exprimées en unités de pixels, comme le frame de rectangle de face. Le code suivant montre comment vous pouvez extraire les emplacements des élèves et nez :

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Données de points de repère face peuvent également servir à calculer précisément la direction de la face. Par exemple, nous pouvons définir la rotation de la face comme un vecteur à partir du centre de la bouche au centre des yeux. Le code suivant calcule ce vecteur :

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Si nous connaissons la direction de la face, vous pouvez ensuite faire pivoter le cadre du visage rectangulaire pour aligner plus correctement. Si vous souhaitez rogner les visages dans une image, vous pouvez par programmation faire pivoter l’image afin que les visages apparaissent toujours verticales.

## <a name="get-face-attributes"></a>Obtenir les attributs de visage

Outre les rectangles de face et les éléments géographiques, le API de détection de visage peut analyser plusieurs attributs conceptuels d’une face. Il s’agit des actions suivantes :

- Age
- Sexe
- Intensité du sourire
- Pilosité faciale
- Lunettes
- 3D pose principal
- Emotion

> [!IMPORTANT]
> Ces attributs sont prédite via l’utilisation d’algorithmes statistiques et peuvent toujours être inexacte. Soyez prudent lors de l’établissement des décisions basées sur les données d’attribut.
>

Pour analyser les attributs de visage, définissez le _returnFaceAttributes_ paramètre à une liste de **[FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** valeurs.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Ensuite, obtenir des références aux données retournées et faire davantage les opérations selon vos besoins.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Pour en savoir plus sur chacun des attributs, reportez-vous à la [glossaire](../Glossary.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris comment utiliser les différentes fonctionnalités de détection de visages. Ensuite, consultez le [glossaire](../Glossary.md) pour une étude plus détaillée pour les données de la face que vous avez récupérés.

## <a name="related-topics"></a>Rubriques connexes

- [Documentation de référence (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentation de référence (SDK .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
