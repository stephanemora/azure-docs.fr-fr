---
title: 'Démarrage rapide : Bibliothèque de client Form Recognizer pour .NET'
description: Bien démarrer avec la bibliothèque cliente Form Recognizer pour .NET afin d’entraîner, extraire, analyser et obtenir des données structurées en sortie à l’aide de ce guide de démarrage rapide.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399419"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Démarrage rapide : Bibliothèque de client Form Recognizer pour .NET

Prise en main de la bibliothèque de client Form Recognizer pour .NET. Form Recognizer est un service cognitif qui utilise la technologie de machine learning pour identifier et extraire des données de paires clé-valeur et de tables à partir de documents de formulaire. Il restitue ensuite des données structurées qui incluent les relations du fichier d’origine. Suivez les étapes suivantes pour installer le package du SDK et essayer l’exemple de code pour les tâches de base.

Utilisez la bibliothèque de client Form Recognizer pour .NET. pour :

* [Effectuer l’apprentissage d’un modèle Form Recognizer personnalisé](#train-a-custom-model)
* [Obtenir la liste des clés extraites](#get-a-list-of-extracted-keys)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Obtenir une liste de modèles personnalisés](#get-a-list-of-custom-models)
* [Supprimer un modèle personnalisé](#delete-a-custom-model)

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/).
* Un blob Stockage Azure qui contient un jeu de données d’apprentissage. Consultez [Créer un jeu de données d’apprentissage pour un modèle personnalisé](../build-training-data-set.md) pour obtenir des conseils et des options pour constituer vos données d’apprentissage. Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier **Train** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451).
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configuration

### <a name="create-a-form-recognizer-azure-resource"></a>Créer une ressource Azure Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

Une fois que vous avez obtenu une clé et un point de terminaison, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour ces derniers, nommées respectivement `FORM_RECOGNIZER_KEY` et `FORM_RECOGNIZER_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `formrecognizer-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Générez ensuite l’application avec :

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

À partir du répertoire de projet, ouvrez le fichier _Program.cs_ dans votre éditeur ou votre IDE favori. Ajoutez les instructions `using` suivantes :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Ajoutez ensuite le code suivant dans la méthode **Main** de l’application. Vous définirez cette tâche asynchrone ultérieurement.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client Form Recognizer pour .NET avec la commande suivante :

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent les fonctionnalités principales du kit de développement logiciel (SDK) Form Recognizer.

|Nom|Description|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Cette classe est nécessaire pour toutes les fonctionnalités Form Recognizer. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Vous utilisez cette classe pour effectuer l’apprentissage d’un modèle Form Recognizer avec vos propres données d’apprentissage. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Cette classe fournit les résultats d’une opération d’apprentissage de modèle personnalisée, y compris l’ID de modèle, que vous pouvez ensuite utiliser pour analyser des formulaires. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Cette classe fournit les résultats d’une opération d’analyse de modèle personnalisée. Elle comprend une liste d’instances **ExtractedPage**. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Cette classe représente toutes les données extraites d’un document de formulaire unique.|

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Form Recognizer pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Effectuer l’apprentissage d’un modèle Form Recognizer personnalisé](#train-a-custom-model)
* [Obtenir la liste des clés extraites](#get-a-list-of-extracted-keys)
* [Analyser les formulaires avec un modèle personnalisé](#analyze-forms-with-a-custom-model)
* [Obtenir une liste de modèles personnalisés](#get-a-list-of-custom-models)
* [Supprimer un modèle personnalisé](#delete-a-custom-model)

## <a name="define-variables"></a>Définir des variables

Avant de définir les méthodes, ajoutez les définitions de variables suivantes au début de votre classe **Program**. Vous devez remplir certaines des variables vous-même. 

* Pour récupérer l’URL SAS de vos données d’apprentissage, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur, puis sélectionnez **Obtenir une signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Si vous avez besoin d’un exemple de formulaire à analyser, vous pouvez utiliser l’un des fichiers disponibles dans le dossier **Test** de l’[exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451). Tous les formulaires utilisés dans le cadre de ce guide sont des formulaires PDF.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Authentifier le client

Sous la méthode `Main`, définissez la tâche référencée dans `Main`. Ici, vous allez authentifier l’objet client à l’aide des variables d’abonnement que vous avez définies ci-dessus. Vous définirez les autres méthodes ultérieurement.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

La méthode suivante utilise votre objet client Form Recognizer pour effectuer l’apprentissage d’un nouveau modèle de reconnaissance sur les documents stockés dans votre conteneur d’objets blob Azure. Elle utilise une méthode d’assistance pour afficher des informations sur le modèle nouvellement formé (représenté par un objet [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview)) et retourne l’ID du modèle.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

La méthode d’assistance suivante affiche des informations sur une opération de modèle Form Recognizer.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Obtenir la liste des clés extraites

Une fois l’entraînement terminé, le modèle personnalisé conserve une liste des clés qu’il a extraites des documents d’entraînement. Il s’attend à ce que les documents de formulaire ultérieurs contiennent ces clés et il extraira leurs valeurs correspondantes dans l’opération d’analyse. Utilisez la méthode suivante pour récupérer la liste des clés extraites et pour les afficher sur la console. C’est un bon moyen de vérifier que le processus d’entraînement a été efficace.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analyser les formulaires avec un modèle personnalisé

Cette méthode utilise le client Form Recognizer et un ID de modèle pour analyser un document de formulaire PDF et en extraire des données de clé/valeur. Elle utilise une méthode d’assistance pour afficher les résultats (représentés par un objet [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)).

> [!NOTE]
> La méthode suivante analyse un formulaire PDF. Pour obtenir des méthodes similaires qui analysent les formulaires JPEG et PNG, consultez l’exemple de code complet sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

La méthode d’assistance suivante affiche des informations sur une opération d’analyse.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Obtenir une liste de modèles personnalisés

Vous pouvez retourner une liste de tous les modèles formés qui appartiennent à votre compte, et vous pouvez récupérer des informations sur le moment où ils ont été créés. La liste des modèles est représentée par un objet [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Supprimer un modèle personnalisé

Si vous voulez supprimer le modèle personnalisé de votre compte, utilisez la méthode suivante :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `dotnet run` à partir de votre répertoire d’application.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

De plus, si vous avez entraîné un modèle personnalisé que vous voulez supprimer de votre compte, exécutez la méthode de [Supprimer un modèle personnalisé](#delete-a-custom-model).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé la bibliothèque de cliente Form Recognizer pour effectuer l’apprentissage d’un modèle personnalisé et analyser des formulaires. Découvrez ensuite les astuces pour créer un jeu de données d’apprentissage plus performant et produire des modèles plus précis.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](../build-training-data-set.md)

* [Qu’est-ce que Form Recognizer ?](../overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
