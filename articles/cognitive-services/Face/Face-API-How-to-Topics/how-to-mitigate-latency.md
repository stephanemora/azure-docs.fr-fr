---
title: Guide pratique pour limiter la latence lors de l’utilisation du service Visage
titleSuffix: Azure Cognitive Services
description: Découvrez comment limiter la latence lors de l’utilisation du service Visage.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 2c771509de5ac246bac0d8e006a5d0b884a410b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706807"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Guide pratique : limiter la latence lors de l’utilisation du service Visage

Vous pouvez rencontrer une latence lors de l’utilisation du service Visage. La latence fait référence à tout type de retard qui se produit lors de la communication sur un réseau. En général, les causes possibles de latence sont les suivantes :
- Distance physique que chaque paquet doit parcourir entre la source et la destination
- Problèmes liés au support de transmission
- Erreurs dans les routeurs ou commutateurs sur le chemin de transmission
- Temps nécessaire aux applications antivirus, pare-feu et autres mécanismes de sécurité pour inspecter les paquets
- Dysfonctionnements dans les applications clientes ou serveur

Cette rubrique traite des causes possibles de la latence propre à l’utilisation des services Azure Cognitive Services et de la façon dont vous pouvez atténuer ces causes.

> [!NOTE]
> Les services Azure Cognitive Services n’offrent aucun Contrat de niveau de service (SLA) concernant la latence.

## <a name="possible-causes-of-latency"></a>Causes de latence possibles

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Connexion lente entre le service Cognitive Services et une URL distante

Certains services Azure Cognitive Services offrent des méthodes qui obtiennent des données à partir d’une URL distante que vous fournissez. Par exemple, quand vous appelez la [méthode DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) du service Visage, vous pouvez spécifier l’URL d’une image dans laquelle le service tente de détecter les visages.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Le service Visage doit ensuite télécharger l’image à partir du serveur distant. Si la connexion du service Visage au serveur distant est lente, cela impacte le temps de réponse de la méthode de détection.

Pour atténuer ce risque, [stockez l’image dans le Stockage Blob Azure Premium](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Par exemple :

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Grande taille de chargement

Certains services Azure Cognitive Services offrent des méthodes qui obtiennent des données à partir d’un fichier que vous chargez. Par exemple, quand vous appelez la [méthode DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) du service Visage, vous pouvez charger une image dans laquelle le service tente de détecter les visages.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Si le fichier à charger est volumineux, le temps de réponse de la méthode `DetectWithStreamAsync` est affecté pour les raisons suivantes :
- Le chargement du fichier prend plus de temps.
- Le traitement du fichier par le service est plus long, en fonction de la taille du fichier.

Atténuations :
- [Stockez l’image dans le Stockage Blob Azure Premium](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Par exemple :
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Chargez un fichier plus petit.
    - Consultez les instructions relatives aux [données d’entrée pour la détection des visages](../concepts/face-detection.md#input-data) et aux [données d’entrée pour la reconnaissance faciale](../concepts/face-recognition.md#input-data).
    - Pour la détection des visages, quand vous utilisez le modèle de détection `DetectionModel.Detection01`, la réduction de la taille du fichier image accélère la vitesse de traitement. Quand vous utilisez le modèle de détection `DetectionModel.Detection02`, la réduction de la taille du fichier image n’accélère la vitesse de traitement que si la taille du fichier image est inférieure à 1920 x 1080.
    - Pour la reconnaissance faciale, la réduction de la taille des visages à 200 x 200 pixels n’affecte pas la justesse du modèle de reconnaissance.
    - Les performances des méthodes `DetectWithUrlAsync` et `DetectWithStreamAsync` dépendent également du nombre de visages dans une image. Le service Visage peut retourner jusqu’à 100 visages pour une image. Les visages sont classés par taille du rectangle du visage dans l’ordre décroissant.
    - Si vous devez appeler plusieurs méthodes du service, envisagez de les appeler en parallèle si la conception de votre application le permet. Par exemple, si vous devez détecter les visages dans deux images pour effectuer une comparaison des visages :
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Connexion lente entre vos ressources de calcul et le service Visage

Si votre ordinateur dispose d’une connexion lente au service Visage, le temps de réponse des méthodes du service est impacté.

Atténuations :
- Quand vous créez votre abonnement au service Visage, veillez à choisir la région la plus proche de l’endroit où votre application est hébergée.
- Si vous devez appeler plusieurs méthodes du service, appelez-les en parallèle si la conception de votre application le permet. Pour obtenir un exemple, consultez la section précédente.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à limiter la latence lors de l’utilisation du service Visage. Découvrez à présent comment effectuer un scale-up pour passer des objets PersonGroup et FaceList à des objets LargePersonGroup et LargeFaceList, respectivement.

> [!div class="nextstepaction"]
> [Exemple : Utilisez la fonctionnalité à grande échelle](how-to-use-large-scale.md)

## <a name="related-topics"></a>Rubriques connexes

- [Documentation de référence (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentation de référence (kit SDK .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)