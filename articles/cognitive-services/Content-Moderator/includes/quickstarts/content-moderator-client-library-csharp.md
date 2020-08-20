---
title: 'Démarrage rapide : Bibliothèque de client .NET Content Moderator'
titleSuffix: Azure Cognitive Services
description: Bien démarrer avec la bibliothèque cliente Content Moderator pour .NET avec ce guide de démarrage rapide.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: b064f6515c8ff4b3be4219cc6516ef25dabf0679
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510094"
---
Bien démarrer avec la bibliothèque de client Content Moderator pour .NET Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Content Moderator est un service cognitif qui vérifie le texte, les images et le contenu vidéo à la recherche d’éléments potentiellement dangereux, offensants ou indésirables. Lorsque des éléments de ce type sont détectés, le service applique les étiquettes appropriées (indicateurs) au contenu. L’application peut ensuite gérer le contenu marqué afin de se conformer aux réglementations ou pour maintenir l’environnement souhaité pour les utilisateurs.

Utilisez la bibliothèque de client Content Moderator pour .NET aux fins suivantes :

* [Modérer du texte](#moderate-text)
* [Modérer les images](#moderate-images)
* [Créer une révision](#create-a-review)

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Exemples](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configuration

### <a name="create-a-content-moderator-azure-resource"></a>Créer une ressource Azure Content Moderator

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Content Moderator en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Une fois que vous avez obtenu une clé à partir de votre ressource, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL de point de terminaison, nommées respectivement `CONTENT_MODERATOR_SUBSCRIPTION_KEY` et `CONTENT_MODERATOR_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core dans votre éditeur de texte ou IDE favori. 

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `content-moderator-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

À partir du répertoire de projet, ouvrez le fichier *Program.cs* dans votre éditeur ou votre IDE favori. Ajoutez les instructions `using` suivantes :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Dans la classe **Program**, créez des variables pour l’emplacement et la clé du point de terminaison de votre ressource en tant que variables d’environnement.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Si vous avez créé les variables d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder aux variables.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client Content Moderator pour .NET avec la commande suivante :

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client .NET Content Moderator.

|Nom|Description|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Cette classe est nécessaire pour toutes les fonctionnalités Content Moderator. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Cette classe fournit les fonctionnalités permettant d’analyser des images pour y rechercher du contenu pour adultes, des informations personnelles ou des visages.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Cette classe fournit les fonctionnalités d’analyse de texte pour le langage, les blasphèmes, les erreurs et les informations personnelles.|
|[Révisions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Cette classe fournit les fonctionnalités des API de révision, notamment les méthodes de création de travaux, les workflows personnalisés et les révisions humaines.|

## <a name="code-examples"></a>Exemples de code


Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Content Moderator pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Modérer du texte](#moderate-text)
* [Modérer les images](#moderate-images)
* [Créer une révision](#create-a-review)

## <a name="authenticate-the-client"></a>Authentifier le client

Dans une nouvelle méthode, instanciez les objets clients avec le point de terminaison et la clé. Il n’est pas indispensable d’avoir un client différent pour chaque scénario, mais cela peut vous aider à organiser votre code.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Modérer du texte

Le code suivant utilise un client Content Moderator pour analyser un corps de texte et imprimer les résultats dans la console. À la racine de la classe **Program**, définissez les fichiers d’entrée et de sortie :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Ensuite, à la racine de votre projet, ajoutez un fichier *TextFile.txt*. Ajoutez votre propre texte à ce fichier, ou utilisez l’exemple de texte suivant :

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Ajoutez l’appel de méthode suivant à votre méthode `Main` :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Définissez ensuite la méthode de modération du texte dans votre classe **Program** :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Modérer des images

Le code suivant utilise un client Content Moderator, ainsi qu’un objet [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet), pour analyser les images distantes et y rechercher du contenu pour adultes.

> [!NOTE]
> Vous pouvez également analyser le contenu d’une image locale. Pour connaître les méthodes et les opérations qui fonctionnent avec les images locales, consultez la [documentation de référence](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

### <a name="get-sample-images"></a>Obtenir des exemples d’images

Définissez vos fichiers d’entrée et de sortie :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Créez ensuite le fichier d’entrée (*ImageFiles.txt*) à la racine de votre projet. Dans ce fichier, ajoutez les URL des images pour analyser&mdash;une URL sur chaque ligne. Vous pouvez utiliser les exemples d’image suivants :

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Transmettez vos fichiers d’entrée et de sortie dans l’appel de méthode suivant de la méthode `Main`. Vous définirez cette méthode lors d’une prochaine étape.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

### <a name="define-helper-class"></a>Définir une classe d’assistance

Ajoutez la définition de classe suivante dans la classe **Program**. Cette classe interne gère les résultats de la modération d’images.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Définir la méthode de modération des images

La méthode suivante parcourt les URL d’image d’un fichier texte, crée une instance **EvaluationData** et analyse l’image à la recherche de contenu pour adultes, de texte et de visages humains. Ensuite, elle ajoute la dernière instance **EvaluationData** à une liste et affiche la liste complète des données retournées dans la console.

#### <a name="iterate-through-image-urls"></a>Parcourir les URL d’images

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analyser le contenu

Pour plus d’informations sur les attributs d’image que Content Moderator peut filtrer, consultez le guide [Concepts relatifs à la modération d’image](../../image-moderation-api.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Écrire les résultats de la modération dans un fichier

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Créer une révision

Vous pouvez utiliser la bibliothèque de client .NET Content Moderator pour alimenter le contenu dans l’[outil de révision](https://contentmoderator.cognitive.microsoft.com) et permettre à des modérateurs humains de l’examiner. Pour en savoir plus sur l’outil de révision, consultez le [Guide conceptuel de l’outil de révision du code](../../review-tool-user-guide/human-in-the-loop.md).

La méthode de cette section utilise la classe [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) pour créer une révision, récupérer son ID et vérifier ses détails après avoir reçu une entrée humaine via le portail web de l’outil de révision. Elle journalise toutes ces informations dans un fichier texte de sortie. Appelez la méthode à partir de votre méthode `Main` :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Obtenir des exemples d’images

Déclarez le tableau suivant à la racine de votre classe **Program**. Cette variable référence l’exemple d’image à utiliser pour créer la révision.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Obtenir les informations d’identification de révision

Connectez-vous à l’[outil de révision](https://contentmoderator.cognitive.microsoft.com) et récupérez le nom de votre équipe. Ensuite, affectez-le à la variable appropriée dans la classe **Program**. Si vous le souhaitez, vous pouvez configurer un point de terminaison de rappel pour recevoir des mises à jour sur l’activité de la révision.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Définir une classe d’assistance

Ajoutez la définition de classe suivante dans la classe **Program**. Cette classe sera utilisée pour représenter une instance de révision unique soumise à l’outil de révision.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Définir une méthode d’assistance

Ajoutez la méthode suivante à la classe **Program**. Cette méthode écrit les résultats des requêtes de révision dans le fichier texte de sortie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Définir la méthode de création de la révision

Vous êtes maintenant prêt à définir la méthode qui va gérer la création et l’interrogation de la révision. Ajoutez une méthode (**CreateReviews**) et définissez les variables locales suivantes.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Publier des révisions sur l’outil de révision

Ensuite, ajoutez le code suivant pour parcourir les exemples d’images donnés, ajouter des métadonnées et les envoyer à l’outil de révision dans un même lot. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

L’objet retourné par l’appel de l’API contient des valeurs d’ID uniques pour chaque image chargée. Le code suivant analyse ces ID, puis les utilise pour interroger Content Moderator afin d’obtenir l’état de chaque image du lot.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Obtenir les informations d’une révision

Le code suivant demande au programme d’attendre une entrée utilisateur. Lorsque vous arrivez à cette étape de l’exécution, vous pouvez accéder à l’[outil de révision](https://contentmoderator.cognitive.microsoft.com), vérifier que l’exemple d’image a été chargé et interagir avec celui-ci. Pour plus d’informations sur la façon d’interagir avec une révision, consultez le [Guide pratique sur les révisions](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Lorsque vous avez terminé, vous pouvez appuyer sur n’importe quelle touche pour continuer le programme et récupérer les résultats du processus de révision.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Si vous avez utilisé un point de terminaison de rappel dans ce scénario, il doit recevoir un événement au format suivant :

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque .NET Content Moderator pour effectuer des tâches de modération. Pour plus d’informations sur la modération des images ou d’autres éléments multimédias, consultez le guide conceptuel.

> [!div class="nextstepaction"]
> [Concepts liés à la modération d’image](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Qu’est-ce qu’Azure Content Moderator ?](../../overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
