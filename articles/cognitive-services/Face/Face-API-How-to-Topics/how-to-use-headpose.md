---
title: HeadPose permet d’ajuster le rectangle de face
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’attribut HeadPose pour automatiquement faire pivoter le rectangle de face.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576501"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>L’attribut HeadPose permet d’ajuster le rectangle de face

Dans ce guide, vous allez utiliser un attribut de visage détecté, HeadPose, pour faire pivoter le rectangle d’un objet de visage. L’exemple de code dans ce guide, à partir de la [WPF de visage Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) exemple d’application, utilise le kit SDK .NET.

Le rectangle de face, retourné avec chaque face détectée, marque l’emplacement et la taille de la face de l’image. Par défaut, le rectangle est toujours aligné avec l’image (ses côtés sont parfaitement horizontale et verticale) ; Cela peut être inefficace pour visages en angle de tramage. Dans les situations où vous souhaitez découper par programmation des visages dans une image, il est avantageux de pouvoir faire pivoter le rectangle pour rogner.

## <a name="explore-the-sample-code"></a>Explorer l’exemple de code

Vous pouvez par programmation faire pivoter le rectangle de face à l’aide de l’attribut HeadPose. Si vous spécifiez cet attribut lors de la détection des visages (consultez [comment détecter des visages](HowtoDetectFacesinImage.md)), vous serez en mesure d’interroger plus tard. La méthode suivante à partir de la [WPF de visage Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) application accepte une liste de **DetectedFace** objets et retourne une liste de **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objets. **Visage** ici est une classe personnalisée que magasins doivent faire face les données, y compris les coordonnées du rectangle de mise à jour. Nouvelles valeurs sont calculées pour **haut**, **gauche**, **largeur**, et **hauteur**et un nouveau champ **FaceAngle**spécifie la rotation.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

## <a name="display-the-updated-rectangle"></a>Afficher le rectangle de mise à jour

À ce stade, vous pouvez utiliser retourné **Face** objets dans votre affichage. Les lignes suivantes à partir de [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) montrent comment le nouveau rectangle est rendu à partir de ces données :

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="next-steps"></a>Étapes suivantes

Consultez le [WPF de visage Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) application sur GitHub pour obtenir un exemple de rectangles de visage pivoté. Vous pouvez aussi consulter le [Face API HeadPose exemple](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) application, qui effectue le suivi de l’attribut HeadPose en temps réel pour détecter les mouvements principal (saluer, secouer).