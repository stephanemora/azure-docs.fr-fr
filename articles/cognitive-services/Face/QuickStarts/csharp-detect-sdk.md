---
title: 'Démarrage rapide : Détecter des visages dans une image avec le SDK .NET Visage Azure'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez utiliser le SDK Visage Azure avec C# pour détecter des visages dans une image.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: pafarley
ms.openlocfilehash: 4fbbde167a8c895a71ab3614e8c3ecbce26604a9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578149"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Démarrage rapide : Détecter des visages dans une image à l’aide du SDK .NET Visage

Dans ce guide de démarrage rapide, vous allez utiliser le SDK du service Visage avec C# pour détecter des visages humains dans une image. Pour obtenir un exemple opérationnel du code dans ce guide de démarrage rapide, consultez le projet Face dans le dépôt [Cognitive Services Vision csharp quickstarts](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) sur GitHub.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

- Clé d’abonnement à l’API Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service API Visage et obtenir votre clé.
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

1. Dans Visual Studio, créez un projet **Application console (.NET Framework)** et nommez-le **FaceDetection**. 
1. Si votre solution comporte d’autres projets, sélectionnez celui-ci comme projet de démarrage unique.
1. Récupérez les packages NuGet requis. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, puis sélectionnez **Gérer les packages NuGet**. Cliquez sur l’onglet **Parcourir**, sélectionnez **Inclure la préversion**, puis recherchez et installez le package suivant :
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-face-detection-code"></a>Ajoutez le code de détection de visage

Ouvrez le fichier *Program.cs* du nouveau projet. Ici, vous allez ajouter le code nécessaire pour charger des images et détecter des visages.

### <a name="include-namespaces"></a>Inclure des espaces de noms

Ajoutez les instructions `using` suivantes en haut de votre fichier *Program.cs*.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Ajouter des champs essentiels

Ajoutez les champs suivants à la classe **Program** . Ces données spécifient comment se connecter au service Visage et où obtenir les données d’entrée. Vous devez mettre à jour le champ `subscriptionKey` avec la valeur de votre clé d’abonnement, et éventuellement changer la chaîne `faceEndpoint` pour qu’elle contienne l’identificateur de région approprié. Vous devez également définir les valeurs `localImagePath` et/ou `remoteImageUrl` avec des chemins qui pointent vers de vrais fichiers image.

Le champ `faceAttributes` est simplement un tableau de certains types d’attributs. Il spécifie les informations à récupérer sur les visages détectés.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>Créer et utiliser le client Visage

Ajoutez ensuite le code suivant à la méthode **Main** de la classe **Program**. Ce code configure un client de l’API Visage.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

Toujours dans la méthode **Main**, ajoutez le code suivant pour utiliser le client Visage récemment créé pour détecter des visages dans une image locale et distante. Vous définissez ensuite les méthodes de détection. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>Détecter des visages

Ajoutez la méthode suivante à la classe **Program**. Elle utilise le client du service Visage pour détecter des visages dans une image distante référencée par une URL. Notez qu’elle utilise le champ `faceAttributes`. Les objets **DetectedFace** ajoutés à `faceList` ont les attributs spécifiés (dans ce cas, l’âge et le sexe).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

De même, ajoutez la méthode **DetectLocalAsync**. Elle utilise le client du service Visage pour détecter des visages dans une image locale référencée par un chemin de fichier.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Récupérer et afficher des attributs de visage

Définissez ensuite la méthode **GetFaceAttributes**. Elle retourne une chaîne avec des informations pertinentes sur les attributs.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Enfin, définissez la méthode **DisplayAttributes** pour écrire les données des attributs de visage dans la console de sortie.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>Exécution de l'application

Une réponse correcte affiche le sexe et l’âge pour chaque visage de l’image. Par exemple : 

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une application console .NET simple qui utilise le service d’API Visage pour détecter des visages dans des images locales et distantes. Suivez à présent un tutoriel plus approfondi pour voir comment présenter de manière intuitive les informations sur les visages à l’utilisateur.

> [!div class="nextstepaction"]
> [Tutoriel : Créer une application WPF pour détecter et analyser les visages dans une image](../Tutorials/FaceAPIinCSharpTutorial.md)
