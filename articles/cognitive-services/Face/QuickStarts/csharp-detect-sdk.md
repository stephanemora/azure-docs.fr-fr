---
title: 'Démarrage rapide : Détecter les visages dans une image - SDK, C#'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez détecter les visages d’une image à l’aide de la bibliothèque de client C# Windows de l’API Visage de Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: b5fd10aaab28303079d3eef9c4c5c25207bb672e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344980"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>Démarrage rapide : Détecter les visages dans une image à l’aide de C&#35; - Visage

Dans ce guide de démarrage rapide, vous allez détecter des visages humains dans une image à l’aide de la bibliothèque de client Windows de l’API Visage.

## <a name="prerequisites"></a>Prérequis

* Vous avez besoin d’une clé d’abonnement pour exécuter l’exemple. Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* N’importe quelle édition de [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Le package NuGet de la bibliothèque de client [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Il n’est pas nécessaire de télécharger le package. Vous trouverez ci-dessous des instructions d’installation.

## <a name="detectwithurlasync-method"></a>Méthode DetectWithUrlAsync

> [!TIP]
> Récupérez le dernier code en tant que solution Visual Studio à partir de [Github](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

Les méthodes `DetectWithUrlAsync` et `DetectWithStreamAsync` permettent de wrapper [l’API de détection des visages](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) pour les images locales et distantes, respectivement. Vous pouvez utiliser ces méthodes pour détecter les visages dans une image et retourner les attributs des visages, notamment :

* ID du visage : ID unique utilisé dans plusieurs scénarios d’API Visage.
* Rectangle du visage : valeurs gauche, haut, largeur et hauteur indiquant l’emplacement du visage dans l’image.
* Points de repère : tableau des 27 points de repère du visage pointant vers les positions importantes des composants du visage.
* Les attributs du visage, notamment l’âge, le sexe, l’intensité du sourire, la posture de la tête et la pilosité faciale.

Pour exécuter l’exemple, effectuez les étapes suivantes :

1. Dans Visual Studio, créez une application de console Visual C#.
1. Installez le package NuGet de la bibliothèque de client de l’API Visage.
    1. Dans le menu supérieur, cliquez sur **Outils**, sélectionnez **Gestionnaire de packages NuGet**, puis **Gérer les packages NuGet de la solution**.
    1. Cliquez sur l’onglet **Parcourir**, puis sélectionnez **Inclure la préversion**.
    1. Dans la zone **Rechercher**, tapez « Microsoft.Azure.CognitiveServices.Vision.Face».
    1. Sélectionnez **Microsoft.Azure.CognitiveServices.Vision.Face** lorsqu’il s’affiche, cochez la case en regard du nom de votre projet, puis cliquez sur **Installer**.
1. Remplacez le contenu de *Program.cs* par le code suivant.
1. Remplacez `<Subscription Key>` par votre clé d’abonnement valide.
1. Changez `faceEndpoint` pour la région Azure associée à vos clés d’abonnement, si nécessaire.
1. Si vous le souhaitez, remplacez <`LocalImage>` par le chemin et le nom d’une image locale (qui sera ignoré si non défini).
1. Si vous le souhaitez, définissez `remoteImageUrl` sur une autre image.
1. Exécutez le programme.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>Réponse DetectWithUrlAsync

Une réponse correcte affiche le sexe et l’âge pour chaque visage de l’image.

Pour obtenir un exemple de sortie JSON brute, consultez [Guides de démarrage rapide des API : Détecter des visages dans une image avec C#](CSharp.md).

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer une application Windows WPF qui utilise le service Visage pour détecter des visages dans une image. L’application dessine un cadre autour de chaque visage et affiche une description du visage dans la barre d’état.

> [!div class="nextstepaction"]
> [Tutoriel : Créer une application WPF pour détecter et encadrer des visages dans une image](../Tutorials/FaceAPIinCSharpTutorial.md)
