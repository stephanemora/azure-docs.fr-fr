---
title: 'Démarrage rapide : Analyser des images pour détecter le contenu répréhensible (C#) - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Comment analyser le contenu d’une image pour trouver des matériaux répréhensibles à l’aide du kit SDK Azure Content Moderator pour .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: bd281191deb1a82d4b1b5247a011d9867f0c9bc4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879290"
---
# <a name="quickstart-analyze-image-content-for-objectionable-material-in-c"></a>Démarrage rapide : Analyser le contenu d’une image pour trouver des matériaux répréhensibles dans C#

Cet article fournit des informations et des exemples de code qui vont vous aider à prendre en main le [kit SDK Content Moderator pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Vous allez apprendre à rechercher du contenu raciste ou pour adultes, du texte extractible et des visages humains afin de modérer des matériaux potentiellement répréhensibles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

- Une clé d’abonnement Content Moderator. Suivez les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Content Moderator et obtenir votre clé.
- N’importe quelle édition de [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> Ce guide utilise un abonnement Content Moderator gratuit. Pour plus d’informations sur ce qui est proposé avec chaque niveau d’abonnement, consultez la page [Tarification et limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

1. Dans Visual Studio, créez un nouveau projet **Application de console (.NET Framework)** et nommez-le **ImageModeration**. 
1. S’il existe d’autres projets dans votre solution, sélectionnez celui-ci comme projet de démarrage unique.
1. Récupérez les packages NuGet requis. Cliquez avec le bouton droit sur votre projet dans l’Explorateur de solutions et sélectionnez **Gérer les packages NuGet** ; ensuite, recherchez et installez les packages suivants :
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>Ajouter du code pour la modération des images

Vous allez ensuite copier et coller le code de ce guide dans votre projet pour implémenter un scénario de modération de contenu de base.

### <a name="include-namespaces"></a>Inclure des espaces de noms

Ajoutez les instructions `using` suivantes en haut de votre fichier *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Créer le client Content Moderator

Ajoutez le code suivant à votre fichier *Program.cs* afin de créer un fournisseur de client Content Moderator pour votre abonnement. Ajoutez le code ainsi que la classe **Program** dans le même espace de noms. Vous devrez mettre à jour les champs **AzureRegion** et **CMSubscriptionKey** avec les valeurs de votre identificateur de région et de votre clé d’abonnement.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>Configurer les cibles d’entrée et de sortie

Ajoutez les champs statiques suivants à la classe **Program** dans _Program.cs_. Ils spécifient les fichiers pour le contenu d’image d’entrée et le contenu JSON de sortie.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

Vous devez créer le fichier d’entrée *ImageFiles.txt* et mettre à jour son chemin d’accès en conséquence (les chemins d’accès relatifs sont relatifs au répertoire d’exécution). Ouvrez le fichier _ImageFiles.txt_ et ajoutez les URL des images à modérer. Ce guide de démarrage rapide utilise les URL et l’exemple d’entrée suivants.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Créer une classe pour traiter les résultats

Ajoutez le code suivant dans *Program.cs* ainsi que la classe **Program** dans le même espace de noms. Vous utilisez une instance de cette classe pour enregistrer les résultats de modération de chacune des images vérifiées.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>Définir la méthode d’évaluation d’image

Ajoutez la méthode suivante à la classe **Program**. Cette méthode évalue une seule image de trois façons différentes et renvoie les résultats d’évaluation. Si vous souhaitez en savoir plus sur le rôle de ces opérations individuelles, suivez le lien dans la section [Étapes suivantes](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>Charger les images d’entrée

Ajoutez le code suivant à la méthode **Main** dans la classe **Program**. Ceci permet de configurer le programme afin qu’il récupère les données d’évaluation pour chaque URL d’image dans le fichier d'entrée. Les données sont ensuite écrites dans un fichier de sortie unique.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>Exécuter le programme

Le programme écrit des données de chaîne JSON dans le fichier _ModerationOutput.txt_. L’exemple d’images utilisé dans ce démarrage rapide donne le résultat suivant. Notez que chaque image contient différentes sections pour `ImageModeration`, `FaceDetection` et `TextDetection`, qui correspondent aux trois appels d’API dans votre méthode **EvaluateImage**.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez développé une application .NET unique qui utilise le service Content Moderator pour retourner des informations pertinentes sur un exemple d’image donné. Ensuite, apprenez-en davantage sur la signification des différents indicateurs et classifications, afin que vous puissiez décider de quelles données vous avez besoin et la manière votre application doit les gérer.

> [!div class="nextstepaction"]
> [Guide pour la modération des images](image-moderation-api.md)
