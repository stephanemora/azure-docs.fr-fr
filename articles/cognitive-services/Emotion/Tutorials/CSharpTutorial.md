---
title: 'Tutoriel : Reconnaissance des émotions sur un visage dans une image - API Émotion, C#'
titlesuffix: Azure Cognitive Services
description: Explorez une application Windows de base permettant de reconnaître les émotions exprimées par les visages dans une image.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 953111a65aecc05ce03bd58212494e1f251e5f16
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234031"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>Tutoriel : Reconnaissance des émotions sur un visage dans une image.

> [!IMPORTANT]
> L’API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant accessible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/). 

Explorez une application Windows de base qui utilise l’API Émotion pour reconnaître les émotions exprimées par les visages présents sur une image. L’exemple ci-dessous vous permet d’envoyer une URL d’image ou un fichier stocké localement. Vous pouvez utiliser cet exemple open source comme modèle pour générer votre propre application pour Windows avec l’API Émotion et WPF (Windows Presentation Foundation), qui fait partie de .NET Framework.

## <a name="Prerequisites">Composants requis</a>
#### <a name="platform-requirements"></a>Plateforme requise  
L’exemple ci-dessous a été développé pour .NET Framework à l’aide de [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>S’abonner à l’API Émotion et obtenir une clé d’abonnement  
Avant de créer l’exemple, vous devez vous abonner à l’API Émotion qui fait partie de Microsoft Cognitive Services. Consultez la page sur les [abonnements](https://azure.microsoft.com/try/cognitive-services/). Les clés primaire et secondaire peuvent toutes deux être utilisées dans ce didacticiel. Assurez-vous de suivre les meilleures pratiques pour garder votre clé API secrète et sûre.  

#### <a name="get-the-client-library-and-example"></a>Obtenir la bibliothèque de client et l’exemple  
Vous pouvez télécharger la bibliothèque de client de l’API Émotion via un [Kit de développement logiciel (SDK)](https://www.github.com/microsoft/cognitive-emotion-windows). Le fichier zip téléchargé doit être extrait dans le dossier de votre choix ; de nombreux utilisateurs choisissent le dossier de Visual Studio 2015.
## <a name="Step1">Étape 1 : Ouvrir l’exemple</a>
1.  Démarrez Microsoft Visual Studio 2015 et cliquez sur **Fichier**, sélectionnez **Ouvrir**, puis **Projet/Solution**.
2.  Accédez au dossier où vous avez enregistré les fichiers de l’API Émotion téléchargés. Cliquez sur **Émotion**, puis sur **Windows** et sur le dossier **Sample-WPF**.
3.  Double-cliquez pour ouvrir le fichier Visual Studio 2015 Solution (.sln) nommé **EmotionAPI-WPF-Samples.sln**. Cela permet d’ouvrir la solution dans Visual Studio.

## <a name="Step2">Étape 2 : Générer l’exemple</a>
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Gérer les packages NuGet**.

  ![Ouvrir le gestionnaire de package NuGet](../Images/EmotionNuget.png)

2.  La fenêtre du **gestionnaire de package NuGet** s’ouvre. Commencez par sélectionner **Parcourir** dans le coin supérieur gauche, puis tapez « Newtonsoft.Json » dans la zone de recherche. Sélectionnez le package **Newtonsoft.Json** et cliquez sur **Installer**.  

  ![Accéder au package NuGet](../Images/EmotionNugetBrowse.png)  

3.  Appuyez sur Ctrl+Maj+B ou cliquez sur **Générer** dans le menu du ruban, puis sélectionnez **Générer la solution**.

## <a name="Step3">Étape 3 : Exécuter l’exemple</a>
1.  Une fois la génération terminée, appuyez sur **F5** ou cliquez sur **Démarrer** dans le menu du ruban pour exécuter l’exemple.
2.  Recherchez la fenêtre de l’API Émotion avec la **zone de texte** qui indique « **Paste your subscription key here to start** » (Collez votre clé d’abonnement ici pour démarrer). Collez votre clé d’abonnement dans la zone de texte comme indiqué dans la capture d’écran ci-dessous. Vous pouvez choisir de conserver votre clé d’abonnement sur votre PC ou ordinateur portable en cliquant sur le bouton « Save Key » (Enregistrer la clé). Quand vous souhaitez supprimer la clé d’abonnement du système, cliquez sur « Supprimer la clé » pour la supprimer de votre PC ou ordinateur portable.

  ![Interface de la fonctionnalité Émotion](../Images/EmotionKey.png)

3.  Sous « **Sélectionner un scénario** », cliquez pour utiliser l’un des deux scénarios, « **Detect emotion using a stream**» (Détecter une émotion à l’aide d’un flux) ou « **Detect emotion using a URL** » (Détecter une émotion à l’aide d’une URL), puis suivez les instructions à l’écran. Microsoft reçoit les images que vous chargez et peut les utiliser pour améliorer l’API Émotion et les services associés. En envoyant une image, vous confirmez que vous avez respecté notre [Code de conduite du développeur](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Voici des exemples d’images à utiliser avec cet exemple d’application. Vous pouvez trouver ces images dans [le référentiel GitHub de l’API Visage](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) dans le dossier **Données**. Notez que l’utilisation de ces images est fournie sous licence, sous un contrat d’usage raisonnable, ce qui signifie que vous pouvez les utiliser à des fins de test dans cet exemple, mais que vous ne pouvez pas les republier.

## <a name="Review">Réviser et apprendre</a>
Maintenant que vous avez une application en cours d’exécution, examinons comment cet exemple d’application s’intègre à Microsoft Cognitive Services. Il sera ainsi plus facile de continuer la génération sur cette application ou de développer votre propre application à l’aide de l’API Émotion Microsoft.

Cet exemple d’application se sert de la bibliothèque de client de l’API Émotion, un simple wrapper de client C# pour l’API Émotion Microsoft. Quand vous avez créé l’exemple d’application comme décrit ci-dessus, vous avez obtenu la bibliothèque de client à partir d’un package NuGet. Vous pouvez examiner le code source de la bibliothèque de client dans le dossier intitulé « [Bibliothèque cliente](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary) » sous **Émotion**, **Windows**, **Bibliothèque cliente**, qui fait partie du référentiel de fichiers téléchargés indiqué plus haut dans [Prérequis](#Prerequisites).

Vous pouvez également découvrir comment utiliser le code de la bibliothèque de client dans **l’Explorateur de solutions** : sous **EmotionAPI-WPF_Samples**, développez **DetectEmotionUsingStreamPage.xaml** pour rechercher **DetectEmotionUsingStreamPage.xaml.cs**, qui est utilisé pour accéder à un fichier stocké localement ou développez **DetectEmotionUsingURLPage.xaml** pour rechercher  **DetectEmotionUsingURLPage.xaml.cs**, qui est utilisé lors du chargement d’une URL d’image. Double-cliquez sur les fichiers .xaml.cs pour les ouvrir dans de nouvelles fenêtres dans Visual Studio.

En examinant de quelle façon la bibliothèque de client Émotion est utilisée dans l’exemple d’application, observez les deux extraits de code de **DetectEmotionUsingStreamPage.xaml.cs** et **DetectEmotionUsingURLPage.xaml.cs**. Chaque fichier contient des commentaires de code qui indiquent « KEY SAMPLE CODE STARTS HERE » (L’EXEMPLE DE CODE COMMENCE ICI) et « KEY SAMPLE CODE ENDS HERE » (L’EXEMPLE DE CODE SE TERMINE ICI) pour vous aider à localiser les extraits de code reproduits ci-après.

L’API Émotion est en mesure d’utiliser une URL d’image ou des données d’images binaires (sous la forme d’un flux d’octets) comme entrée. Les deux options sont examinées ci-dessous. Dans les deux cas, vous commencez par rechercher une directive using, qui vous permet d’utiliser la bibliothèque de client Émotion.
```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Use the following namespace for EmotionServiceClient
            // -----------------------------------------------------------------------
            using Microsoft.ProjectOxford.Emotion;
            using Microsoft.ProjectOxford.Emotion.Contract;
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs

Cet extrait de code montre comment utiliser la bibliothèque de client pour envoyer votre clé d’abonnement et une URL de photo au service de l’API Émotion.

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs

L’exemple de code ci-dessous vous montre comment envoyer votre clé et une image stockée en local à l’API Émotion.


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
