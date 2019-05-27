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
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124547"
---
# <a name="get-face-detection-data"></a>Obtenir des données de détection de visage

Ce guide explique comment utiliser la détection de visage pour extraire les attributs tels que le sexe, d’âge ou pose à partir d’une image donnée. Les extraits de code dans ce guide sont écrits en C# à l’aide de la bibliothèque cliente API visage Azure Cognitive Services. La même fonctionnalité est disponible via le [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Ce guide vous montre comment à :

- Obtenir les emplacements et les dimensions de visages dans une image.
- Obtenir les emplacements des différents points d’intérêt face, telles que les élèves, nez et bouche, dans une image.
- Deviner le sexe, l’âge, émotion et autres attributs de visage détecté.

## <a name="setup"></a>Paramétrage

Ce guide suppose que vous avez déjà construit un [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objet, nommé `faceClient`, avec une URL de clé et le point de terminaison de l’abonnement Face. À ce stade, vous pouvez utiliser la fonctionnalité de détection de visage en appelant [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), qui est utilisé dans ce guide, ou [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Pour obtenir des instructions sur la façon de configurer cette fonctionnalité, consultez le [détecter confronté à démarrage rapide pour C# ](../quickstarts/csharp-detect-sdk.md).

Ce guide se concentre sur les spécificités de l’appel de détecter, telles que les arguments que vous pouvez passer et ce que vous pouvez faire avec les données retournées. Nous recommandons que vous recherchez uniquement les fonctionnalités que vous avez besoin. Chaque opération prend plus de temps pour terminer.

## <a name="get-basic-face-data"></a>Obtenir des données de base face

Pour rechercher des visages et obtenir leurs emplacements dans une image, appelez la méthode avec le _returnFaceId_ paramètre défini sur **true**. Il s’agit du paramètre par défaut.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Vous pouvez interroger retourné [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objets pour leur unique ID et un rectangle qui donne les coordonnées en pixels de la face.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Pour plus d’informations sur la façon d’analyser l’emplacement et les dimensions de la face, consultez [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). En règle générale, ce rectangle contient les yeux, sourcils, nez et bouche. Haut de la tête, oreilles et chin ne sont pas nécessairement inclus. Pour utiliser le rectangle de face pour rogner une tête complète ou obtenir un capture d’intermédiaire portrait, peut-être pour une image de type d’ID de photo, vous pouvez développer le rectangle dans chaque direction.

## <a name="get-face-landmarks"></a>Obtenir les points de repère de visage

[Doivent faire face les points de repère](../concepts/face-detection.md#face-landmarks) sont un ensemble de points facile à trouver sur une face, telles que les élèves ou de l’extrémité du nez. Pour obtenir des données d’éléments géographiques face, définissez le _returnFaceLandmarks_ paramètre **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Le code suivant montre comment vous pouvez extraire les emplacements des élèves et nez :

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

Vous pouvez également utiliser des données de points de repère de face pour calculer avec exactitude la direction de la face. Par exemple, vous pouvez définir la rotation de la face comme un vecteur à partir du centre de la bouche au centre des yeux. Le code suivant calcule ce vecteur :

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

Lorsque vous savez que la direction de la face, vous pouvez faire pivoter le cadre du visage rectangulaire pour aligner plus correctement. Pour rogner les visages dans une image, vous pouvez faire par programmation pivoter l’image afin que les visages apparaissent toujours verticales.

## <a name="get-face-attributes"></a>Obtenir les attributs de visage

Outre les rectangles de face et les éléments géographiques, le API de détection de visage peut analyser plusieurs attributs conceptuels d’une face. Pour obtenir la liste complète, consultez le [attributs](../concepts/face-detection.md#attributes) section conceptuelle.

Pour analyser les attributs de visage, définissez le _returnFaceAttributes_ paramètre à une liste de [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) valeurs.

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

Ensuite, obtenir des références aux données retournées et effectuer des opérations plus selon vos besoins.

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

Pour en savoir plus sur chacun des attributs, consultez le [détection des visages et attributs](../concepts/face-detection.md) guide conceptuel.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à utiliser les différentes fonctionnalités de détection de visages. Ensuite, intégrer ces fonctionnalités dans votre application en suivant un didacticiel approfondi.

- [Tutoriel : Créer une application WPF pour afficher les données de visage dans une image](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutoriel : Créer une application Android pour détecter et de frame de visages dans une image](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Rubriques connexes

- [Documentation de référence (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentation de référence (SDK .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)