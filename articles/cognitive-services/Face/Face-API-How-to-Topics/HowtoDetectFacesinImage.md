---
title: 'Exemple : Détecter les visages dans une image - API Visage'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Visage pour détecter les visages d’une image.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a4c74ff70a4426abf97562bf997479a91afbf17a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124046"
---
# <a name="example-how-to-detect-faces-in-image"></a>Exemple : Comment détecter des visages dans une image

Ce guide va vous montrer comment détecter des visages sur une image, grâce à l’extraction d’attributs tels que le sexe, l’âge ou la posture. Les exemples sont écrits en C# à l’aide de la bibliothèque de client de l’API Visage. 

## <a name="concepts"></a>Concepts

Si vous n’êtes pas familiarisé avec les concepts suivants utilisés dans ce guide, vous pouvez vous reporter aux définitions dans notre [glossaire](../Glossary.md) à tout moment : 

- Détection des visages
- Points de repère du visage
- Posture de tête
- Attributs du visage

## <a name="preparation"></a>Préparation

Dans cet exemple, nous illustrons les fonctionnalités suivantes : 

- Détection des visages sur une image et leur marquage à l’aide d’un cadre rectangulaire
- Analyse de l’emplacement des pupilles, du nez ou de la bouche et leur marquage dans l’image
- Analyse de la posture de la tête, du sexe et de l’âge du visage

Pour exécuter ces fonctionnalités, vous devez préparer une image avec au moins un visage net. 

## <a name="step-1-authorize-the-api-call"></a>Étape 1 : Autoriser l’appel d’API

Chaque appel de l’API Visage nécessite une clé d’abonnement. Cette clé doit être transmise par le biais d’un paramètre de chaîne de requête ou spécifiée dans l’en-tête de requête. Pour transmettre la clé d’abonnement dans une chaîne de requête, reportez-vous à l’URL de demande pour [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en guise d’exemple :

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Une alternative consiste à spécifier la clé d’abonnement dans l’en-tête de demande HTTP : **ocp-apim-subscription-key: &lt;clé d’abonnement&gt;**. En cas d’utilisation d’une bibliothèque cliente, la clé d’abonnement est transmise dans le constructeur de la classe FaceServiceClient. Par exemple : 
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>Étape 2 : Charger une image dans le service et exécuter la détection des visages

La façon la plus simple d’effectuer la détection des visages est en chargeant directement une image. Pour cela, vous devez envoyer une requête « POST » avec le type de contenu application/octet-stream ainsi que les données lues à partir d’une image JPEG. La taille maximale de l’image est de 4 Mo.

À l’aide de la bibliothèque cliente, la détection des visages au moyen du chargement est effectuée en passant un objet de flux. Prenons l'exemple suivant :

```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Notez que la méthode DetectAsync de FaceServiceClient est asynchrone. La méthode appelante doit également être marquée comme asynchrone pour pouvoir utiliser la clause await.
Si l’image est déjà sur le web et possède une URL, la détection des visages peut également être exécutée en fournissant l’URL. Dans cet exemple, le corps de la requête sera une chaîne JSON qui contient l’URL.
À l’aide de la bibliothèque cliente, la détection des visages au moyen d’une URL peut être exécutée facilement en utilisant une autre surcharge de la méthode DetectAsync.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

La propriété FaceRectangle retournée avec les visages détectés indique principalement les emplacements sur les visages en pixels. En général, ce rectangle contient les yeux, les sourcils, le nez et la bouche. Le haut de la tête, les oreilles et le menton ne sont pas inclus. Si vous rognez une tête complète ou un portrait en plan moyen (une image de type photo d’identité), vous devriez étendre la zone du cadre rectangulaire, car la zone du visage peut être trop petite pour certaines applications. Pour localiser un visage plus précisément, l’utilisation de points de repère de visage (localiser les traits de visage ou les mécanismes de direction du visage) décrits dans la prochaine section peut s’avérer utile.

## <a name="step-3-understanding-and-using-face-landmarks"></a>Étape 3 : Comprendre et utiliser les points de repère du visage

Les points de repère du visage sont une série de points détaillés sur un visage ; en général, des points de composants du visage tels que les pupilles, le canthus ou le nez. Les points de repère du visage sont des attributs facultatifs qui peuvent être analysés au cours de la détection des visages. Vous pouvez transmettre « true » en tant que valeur booléenne au paramètre de requête returnFaceLandmarks lors de l’appel à l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), ou utiliser le paramètre facultatif returnFaceLandmarks dans la méthode DetectAsync pour la classe FaceServiceClient afin d’inclure les points de repère du visage dans les résultats de la détection.

Il existe 27 points de repères prédéfinis par défaut. L’illustration suivante montre comment les 27 points sont définis :

![HowToDetectFace](../Images/landmarks.1.jpg)

Les points de repères retournés sont en pixels, comme le cadre rectangulaire. Cela facilite le marquage de points d’intérêt spécifiques dans l’image. Le code suivant illustre la récupération de l’emplacement du nez et des pupilles :

```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

En plus de marquer les traits du visage dans une image, les points de repère de visage peuvent également servir à calculer précisément la direction du visage. Par exemple, nous pouvons définir la direction du visage en tant que vecteur à partir du centre de la bouche jusqu’au centre des yeux. Le code ci-dessous explique cela en détail :

```CSharp
var landmarks = face.FaceLandmarks;
 
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

En connaissant la direction du visage, vous pouvez ainsi faire pivoter le cadre rectangulaire pour l’aligner avec le visage. Il apparaît clairement que les points de repère du visage peuvent apporter davantage de détails et avoir plusieurs utilités.

## <a name="step-4-using-other-face-attributes"></a>Étape 4 : Utiliser d’autres attributs du visage

En plus des points de repère de visage, l’API Visage- Détecter peut également analyser plusieurs autres attributs d’un visage. Ces attributs incluent :

- Age
- Sexe
- Intensité du sourire
- Pilosité faciale
- Une posture de tête 3D

Ces attributs sont calculés à l’aide d’algorithmes statistiques et ne sont pas toujours précis à 100 %. Toutefois, ils sont toujours utiles lorsque vous souhaitez classer les visages en fonction de ces attributs. Pour plus d’informations sur chacun des attributs, reportez-vous au [Glossaire](../Glossary.md).

Voici un exemple simple d’extraction des attributs du visage lors de la détection des visages :

```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 

## <a name="summary"></a>Résumé

Dans ce guide, vous avez appris les fonctionnalités de l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) ; les manières dont elle peut détecter les visages sur des images locales chargées ou des URL d’image sur le web ; comment elle peut détecter des visages en retournant des cadres rectangulaires ; et comment elle peut analyser les points de repère des visages, les postures de tête 3D et d’autres attributs du visage.

Pour plus d’informations sur les API, veuillez consulter le guide de référence sur l’API pour [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Rubriques connexes

[How to identify faces in images](HowtoIdentifyFacesinImage.md) (Comment identifier les visages dans l’image)
