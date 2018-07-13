---
title: Tutoriel sur l’API Vision par ordinateur C# | Microsoft Docs
description: Explorez une application Windows de base qui utilise l’API Vision par ordinateur dans Microsoft Cognitive Services. Effectuez une reconnaissance OCR, créez des miniatures et utilisez des fonctionnalités visuelles dans une image.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368849"
---
# <a name="computer-vision-api-c35-tutorial"></a>Tutoriel sur l’API Vision par ordinateur C&#35;

Explorez une application Windows de base qui utilise l’API Vision par ordinateur pour effectuer une reconnaissance optique des caractères (OCR), créer des miniatures avec un rognage intelligent ainsi que détecter, classer, baliser et décrire des fonctionnalités visuelles, dont des visages, dans une image. L’exemple ci-dessous vous permet d’envoyer une URL d’image ou un fichier stocké localement. Vous pouvez utiliser cet exemple open source comme modèle pour générer votre propre application pour Windows avec l’API Vision et WPF (Windows Presentation Foundation), qui fait partie du .NET Framework.

### <a name="prerequisites"></a>Prérequis

#### <a name="platform-requirements"></a>Exigences relatives à la plateforme

L’exemple ci-dessous a été développé pour le .NET Framework à l’aide de [Visual Studio 2015 Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>S’abonner à l’API Vision par ordinateur et obtenir une clé d’abonnement 

Avant de créer l’exemple, vous devez vous abonner à l’API Vision par ordinateur qui fait partie de Microsoft Cognitive Services (auparavant connu sous le nom de Project Oxford). Pour plus d’informations sur l’abonnement et la gestion des clés, consultez [Abonnements](https://azure.microsoft.com/try/cognitive-services/). Les clés primaire et secondaire peuvent toutes deux être utilisées dans ce tutoriel. 

> [!NOTE]
> Le tutoriel est conçu pour utiliser des clés d’abonnement dans la région **westcentralus**. Les clés d’abonnement générées dans la version d’essai Vision par ordinateur utilisent la région **westcentralus** pour fonctionner correctement. Si vous avez généré vos clés d’abonnement à l’aide de votre compte Azure via [https://azure.microsoft.com/](https://azure.microsoft.com/), vous devez spécifier la région **westcentralus**. Les clés générées en dehors de la région**westcentralus** ne fonctionneront pas.

#### <a name="get-the-client-library-and-example"></a>Obtenir la bibliothèque cliente et l’exemple

Vous pouvez cloner l’exemple d’application et la bibliothèque cliente de l’API Vision par ordinateur sur votre ordinateur via [SDK](https://www.github.com/microsoft/cognitive-vision-windows). Ne les téléchargez pas en tant que fichier ZIP.

### <a name="Step1">Étape 1 : Installer l’exemple</a>

Dans GitHub Desktop, ouvrez Sample-WPF\VisionAPI-WPF-Samples.sln.

### <a name="Step2">Étape 2 : Générer l’exemple</a>

* Appuyez sur Ctrl+Maj+B ou cliquez sur Générer dans le menu du ruban, puis sélectionnez Générer la solution.

### <a name="Step3">Étape 3 : Exécuter l’exemple</a>

1. Une fois la génération terminée, appuyez sur **F5** ou cliquez sur **Démarrer** dans le menu du ruban pour exécuter l’exemple.
2. Recherchez la fenêtre d’interface utilisateur de l’API Vision par ordinateur avec la zone d’édition de texte qui indique « Paste your subscription key here to start » (Collez votre clé d’abonnement ici pour démarrer).
Vous pouvez choisir de conserver votre clé d’abonnement sur votre PC ou ordinateur portable en cliquant sur le bouton Save Key (Enregistrer la clé). Quand vous souhaitez supprimer la clé d’abonnement du système, cliquez sur Delete Key (Supprimer la clé) pour la supprimer de votre PC ou ordinateur portable.

    ![Clé d’abonnement Vision](../Images/Vision_UI_Subscription.PNG)

3. Sous Sélectionner un scénario, cliquez pour utiliser l’un des six scénarios, puis suivez les instructions à l’écran. Microsoft reçoit les images que vous chargez et peut les utiliser pour améliorer l’API Vision par ordinateur et les services associés. En envoyant une image, vous confirmez que vous avez respecté notre [Code de conduite du développeur](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Interface de l’analyse d’image](../Images/Analyze_Image_Example.PNG)

4. Voici des exemples d’images à utiliser avec cet exemple d’application. Vous pouvez trouver ces images dans le dépôt GitHub Windows de l’API Visage, dans le [dossier de données](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Notez que l’utilisation de ces images est concédée sous licence selon l’accord [LICENSE-IMAGE](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Passer en revue et apprendre</a>

Maintenant que vous avez une application en cours d’exécution, examinons comment cet exemple d’application s’intègre à la technologie Cognitive Services. Il sera ainsi plus facile de continuer la génération sur cette application ou de développer votre propre application à l’aide de l’API Vision par ordinateur Microsoft.

Cet exemple d’application se sert de la bibliothèque cliente de l’API Vision par ordinateur, un simple wrapper de client C# pour l’API Vision par ordinateur Microsoft. Quand vous avez créé l’exemple d’application comme décrit ci-dessus, vous avez obtenu la bibliothèque cliente à partir d’un package NuGet. Vous pouvez examiner le code source de la bibliothèque cliente dans le dossier intitulé **Bibliothèque cliente** sous **Vision**, **Windows**, **Bibliothèque cliente**, qui fait partie du dépôt de fichiers téléchargés indiqué plus haut dans Prérequis.

Vous pouvez également déterminer comment utiliser le code de la bibliothèque cliente dans l’Explorateur de solutions : sous **VisionAPI-WPF_Samples**, développez **AnalyzePage.xaml** pour localiser **AnalyzePage.xaml.cs**, qui est utilisé pour l’envoi d’une image au point de terminaison d’analyse de l’image. Double-cliquez sur les fichiers .xaml.cs pour les ouvrir dans de nouvelles fenêtres dans Visual Studio.

En passant en revue la façon dont la bibliothèque cliente Vision est utilisée dans notre exemple d’application, examinons deux extraits de code dans **AnalyzePage.xaml.cs**. Le fichier contient des commentaires de code qui indiquent « KEY SAMPLE CODE STARTS HERE » et « KEY SAMPLE CODE ENDS HERE » pour vous aider à localiser les extraits de code reproduits ci-après.

Le point de terminaison d’analyse est en mesure d’utiliser une URL d’image ou des données d’images binaires (sous forme d’un flux d’octets) comme entrée. Recherchez tout d’abord une directive using, qui vous permet d’utiliser la bibliothèque cliente Vision.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(…)** Cet extrait de code montre comment utiliser la bibliothèque cliente pour envoyer votre clé d’abonnement et une image stockée localement au point de terminaison d’analyse du service API Vision par ordinateur.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(…)** Cet extrait de code montre comment utiliser la bibliothèque cliente pour envoyer votre clé d’abonnement et une URL de photo au point de terminaison d’analyse du service API Vision par ordinateur.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Autres pages et points de terminaison** Le mode d’interaction avec les autres points de terminaison exposés par le service API Vision par ordinateur peut être examiné en passant en revue les autres pages dans l’exemple ; par exemple, le point de terminaison OCR est indiqué en tant que partie du code contenu dans OCRPage.xaml.cs 

### <a name="Related">Rubriques connexes</a>
 * [Bien démarrer avec l’API Visage](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


