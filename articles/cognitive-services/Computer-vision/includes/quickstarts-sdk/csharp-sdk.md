---
title: 'Démarrage rapide : Bibliothèque de client Reconnaissance optique de caractères pour .NET'
description: Dans ce guide de démarrage rapide, vous allez vous familiariser avec la bibliothèque de client Reconnaissance optique de caractères pour .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 58da3353f94a9caafdbd70ad56789ab138cfd6f4
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284769"
---
<a name="HOLTop"></a>

Utilisez la bibliothèque de client OCR pour lire du texte imprimé et manuscrit à partir d'une image.

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

Une fois que vous avez créé un projet, installez la bibliothèque de client en cliquant avec le bouton droit sur la solution de projet dans l’**Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir**, cochez **Inclure la préversion** et recherchez `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Sélectionnez la version `6.0.0-preview.1`, puis **Installer**. 

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
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

À partir du répertoire de projet, ouvrez le fichier *Program.cs* dans votre éditeur ou votre IDE favori.

### <a name="find-the-subscription-key-and-endpoint"></a>Rechercher la clé d’abonnement et le point de terminaison

Accédez au portail Azure. Si la ressource Vision par ordinateur que vous avez créée dans la section **Prérequis** a été déployée, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. Votre clé d’abonnement et votre point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 

Dans la classe **Program** de l’application, créez des variables pour votre clé et votre point de terminaison Vision par ordinateur. Collez votre clé d’abonnement et votre point de terminaison dans le code suivant à l’endroit indiqué. Votre point de terminaison Vision par ordinateur a le format `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> N’oubliez pas de supprimer la clé d’abonnement de votre code une fois que vous avez terminé, et ne la publiez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md).

Dans la méthode `Main` de l’application, ajoutez des appels pour les méthodes utilisées dans ce guide de démarrage rapide. Vous les créerez ultérieurement.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [J’ai configuré le client](?success=set-up-client#object-model) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK OCR .NET.

|Nom|Description|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Cette classe contient des méthodes supplémentaires pour **ComputerVisionClient**.|

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client OCR pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans une nouvelle méthode de la classe **Program**, instanciez un client avec votre point de terminaison et votre clé d’abonnement. Créez un objet **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** avec votre clé d’abonnement, et utilisez-le avec votre point de terminaison pour créer un objet **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [J’ai authentifié le client](?success=authenticate-client#read-printed-and-handwritten-text) [J’ai rencontré un problème](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Lire du texte imprimé et manuscrit

Le service OCR peut lire du texte visible dans une image et le convertir en flux de caractères. Pour plus d'informations sur la reconnaissance de texte, consultez la [Présentation de la reconnaissance optique de caractères (OCR)](../../overview-ocr.md). Le code de cette section utilise la dernière [version du SDK Vision par ordinateur pour Read 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) et définit une méthode, `BatchReadFileUrl`, qui utilise l’objet client pour détecter et extraire du texte dans l’image.

> [!TIP]
> Vous pouvez également extraire du texte d’une image locale. Consultez les méthodes [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient), comme **ReadInStreamAsync**. Ou consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) pour obtenir des scénarios impliquant des images locales.

### <a name="set-up-test-image"></a>Configurer une image de test

Dans votre classe **Program**, enregistrez une référence à l’URL de l’image dont vous souhaitez extraire du texte. Cet extrait de code comprend des exemples d’images pour le texte imprimé et manuscrit.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Appeler l’API Lire

Définissez la nouvelle méthode de lecture du texte. Ajoutez le code ci-dessous afin d’appeler la méthode **ReadAsync** pour l’image donnée. Cela permet de retourner un ID d’opération et de démarrer un processus asynchrone pour lire le contenu de l’image.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Obtenir les résultats de la lecture

Récupérez ensuite l’ID d’opération retourné à partir de l’appel de **ReadAsync**, puis utilisez-le pour interroger le service et obtenir les résultats de l’opération. Le code suivant vérifie l'opération jusqu'à ce que les résultats soient renvoyés. Il affiche ensuite les données textuelles extraites sur la console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Afficher les résultats de la lecture

Ajoutez le code suivant pour analyser et afficher les données textuelles récupérées, puis achevez la définition de la méthode.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

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

> [!div class="nextstepaction"]
>[Référence de l’API Vision par ordinateur (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/computervision)

* [Vue d’ensemble de la reconnaissance OCR](../../overview-ocr.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
