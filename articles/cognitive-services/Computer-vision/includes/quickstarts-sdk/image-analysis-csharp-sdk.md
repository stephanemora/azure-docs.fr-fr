---
title: "Démarrage rapide : Bibliothèque de client Analyse d'images pour .NET"
description: Utilisez ce guide de démarrage rapide pour vous familiariser avec la bibliothèque de client Analyse d'images pour .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 0af6c97d6179a645b078f2335ff38f48890c42a3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728167"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client Analyse d'images pour rechercher des étiquettes, une description textuelle, des visages, du contenu pour adultes ou autre dans une image.

[Documentation de référence](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Exemples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* L’[IDE Visual Studio](https://visualstudio.microsoft.com/vs/) ou la version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

#### <a name="visual-studio-ide"></a>[IDE Visual Studio](#tab/visual-studio)

À l’aide de Visual Studio, créez une application .NET Core. 

### <a name="install-the-client-library"></a>Installer la bibliothèque de client 

Une fois que vous avez créé un projet, installez la bibliothèque de client en cliquant avec le bouton droit sur la solution de projet dans l’**Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir**, cochez **Inclure la préversion** et recherchez `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Sélectionnez la version `7.0.0`, puis **Installer**. 

#### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `computer-vision-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client Vision par ordinateur pour .NET à l’aide de la commande suivante :

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

À partir du répertoire de projet, ouvrez le fichier *Program.cs* dans votre éditeur ou votre IDE favori. Ajoutez les directives `using` suivantes :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

Dans la classe **Program** de l’application, créez des variables pour le point de terminaison et la clé Azure de votre ressource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. La clé et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](../../../cognitive-services-security.md) de Cognitive Services.

Dans la méthode `Main` de l’application, ajoutez des appels pour les méthodes utilisées dans ce guide de démarrage rapide. Vous les créerez ultérieurement.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [J’ai configuré le client](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes prennent en charge certaines des fonctionnalités principales du kit SDK Analyse d'images pour .NET.

|Nom|Description|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Cette classe contient des méthodes supplémentaires pour **ComputerVisionClient**.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Cette énumération définit les différents types d’analyse d’image qui peuvent être effectués dans le cadre d’une opération d’analyse standard. Vous spécifiez un ensemble de valeurs VisualFeatureTypes en fonction de vos besoins. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Analyse d'images pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Analyser une image](#analyze-an-image)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Ce guide de démarrage rapide suppose que vous avez [créé des variables d’environnement](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour votre clé et votre point de terminaison Vision par ordinateur nommées `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

Dans une nouvelle méthode de la classe **Programme**, instanciez un client avec votre point de terminaison et la clé. Créez un objet **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#analyze-an-image) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analyser une image

Le code suivant définit une méthode `AnalyzeImageUrl`, qui utilise l’objet client pour analyser une image distante et afficher les résultats. La méthode retourne une description textuelle, une catégorisation, une liste d’étiquettes, les visages détectés, les indicateurs de contenu pour adultes, les couleurs principales et le type d’image.

> [!TIP]
> Vous pouvez également analyser une image locale. Consultez les méthodes [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient), comme **AnalyzeImageInStreamAsync**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) pour obtenir des scénarios impliquant des images locales.

### <a name="set-up-test-image"></a>Configurer une image de test

Dans votre classe **Program**, enregistrez une référence à l’URL de l’image que vous souhaitez analyser.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>Spécifier les caractéristiques visuelles

Définissez votre nouvelle méthode d’analyse d’image. Ajoutez le code ci-dessous pour spécifier les caractéristiques visuelles que vous souhaitez extraire dans votre analyse. Pour obtenir une liste complète, consultez la section sur l’énumération **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

### <a name="call-the-analyze-api"></a>Appeler l’API Analyser

La méthode **AnalyzeImageAsync** retourne un objet **ImageAnalysis** qui contient toutes les informations extraites.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

Les sections suivantes montrent comment analyser ces informations en détail.

Insérez un des blocs de code suivants dans votre méthode **AnalyzeImageUrl** pour analyser les données des fonctionnalités visuelles que vous avez demandées ci-dessus. N’oubliez pas d’ajouter un crochet fermant à la fin.

```csharp
}
```

### <a name="get-image-description"></a>Obtenir une description d’image

Le code suivant obtient la liste des légendes générées pour l’image. Pour plus d’informations, consultez [Décrire des images](../../concept-describing-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenir une catégorie d’image

Le code suivant obtient la catégorie détectée de l’image. Pour plus d’informations, consultez [Classer des images](../../concept-categorizing-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Obtenir des étiquettes d’image

Le code suivant obtient l’ensemble d’étiquettes détectées dans l’image. Pour plus d’informations, consultez [Étiquettes de contenu](../../concept-tagging-images.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Détecter des objets

Le code suivant détecte les objets courants présents dans l’image et les affiche sur la console. Pour plus d’informations, consultez [Détection d’objets](../../concept-object-detection.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Détecter les marques

Le code suivant détecte les marques et logos d’entreprise dans l’image, et les affiche sur la console. Pour plus d’informations, consultez [Détection des marques](../../concept-brand-detection.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Détecter des visages

Le code suivant retourne les visages détectés dans l’image avec les coordonnées de leur rectangle et sélectionne les attributs du visage. Pour plus d’informations, consultez [Détection des visages](../../concept-detecting-faces.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Détecter des contenus pour adultes, choquants ou sordides

Le code suivant imprime la présence détectée de contenu pour adultes dans l’image. Pour plus d’informations, consultez [Contenu pour adultes choquant ou sordide](../../concept-detecting-adult-content.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenir le modèle de couleurs d’une image

Le code suivant imprime les attributs de couleur détectés dans l’image, comme les couleurs dominantes et la couleur d’accentuation. Pour plus d’informations, consultez [Modèles de couleurs](../../concept-detecting-color-schemes.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenir le contenu spécifique à un domaine

Analyse d'images peut utiliser des modèles spécialisés pour effectuer une analyse approfondie des images. Pour plus d’informations, consultez [Contenu spécifique à un domaine](../../concept-detecting-domain-content.md). 

Le code suivant analyse des données relatives aux célébrités détectées dans l’image.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

Le code suivant analyse des données relatives aux monuments détectés dans l’image.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obtenir le type d’image

Le code suivant affiche des informations sur le type d’image, qu’il s’agisse d’une image clipart ou d’un dessin au trait.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [J’ai analysé une image](?success=analyze-image#run-the-application) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



## <a name="run-the-application"></a>Exécution de l'application

#### <a name="visual-studio-ide"></a>[IDE Visual Studio](#tab/visual-studio)

Exécutez l’application en cliquant sur le bouton **Déboguer** en haut de la fenêtre de l’IDE.

#### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à installer la bibliothèque de client Analyse d’image et à effectuer des appels d’analyse d’image de base. À présent, découvrez-en plus sur les fonctionnalités de l’API Analyser.


> [!div class="nextstepaction"]
>[Appeler l’API Analyser](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Vue d’ensemble de l’analyse d’image](../../overview-image-analysis.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
