---
title: 'Démarrage rapide : Bibliothèque de client QnA Maker pour .NET'
description: Ce guide de démarrage rapide montre comment bien démarrer avec la bibliothèque cliente QnA Maker pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: ce12b0d5739f3c17a324a663a777b70e61f167d1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204014"
---
Utilisez la bibliothèque de client QnA Maker pour .NET afin de :

* Créer une base de connaissances
* Mettre à jour une base de connaissances
* Publier une base de connaissances
* Obtenir une clé de point de terminaison publié
* Attendre l’exécution d’une tâche de longue durée
* Supprimer une base de connaissances

[Documentation de référence](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* L’[IDE Visual Studio](https://visualstudio.microsoft.com/vs/) ou la version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Une fois en possession de votre abonnement Azure, créez une [ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) sur le portail Azure pour obtenir vos clé et point de terminaison de création. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API QnA Maker. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-qna-maker-azure-resource"></a>Créer une ressource Azure QnA Maker

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour QnA Maker en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local.

Après avoir obtenu une clé et un point de terminaison pour votre ressource, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `QNAMAKER_SUBSCRIPTION_KEY`. Le nom de la ressource est utilisé comme sous-domaine personnalisé de l’URL du point de terminaison.

### <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core dans votre éditeur ou IDE favori.

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `qna-maker-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```dotnetcli
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

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Dans le répertoire de l’application, installez la bibliothèque de client QnA Maker pour .NET avec la commande suivante :

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.


## <a name="object-model"></a>Modèle objet

Le client QnA Maker est un objet [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient votre clé.

Une fois le client créé, utilisez la propriété [Knowledge base](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) pour créer, gérer et publier votre base de connaissances.

Gérez votre base de connaissances en envoyant un objet JSON. Pour les opérations immédiates, une méthode retourne généralement un objet JSON indiquant l’état. Pour les opérations de longue durée, la réponse est l’ID d’opération. Appelez la méthode [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) avec l’ID d’opération pour déterminer l’[état de la requête](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client QnA Maker pour .NET :

* [Créer une base de connaissances](#create-a-knowledge-base)
* [Mettre à jour une base de connaissances](#update-a-knowledge-base)
* [Télécharger une base de connaissances](#download-a-knowledge-base)
* [Publier une base de connaissances](#publish-a-knowledge-base)
* [Supprimer une base de connaissances](#delete-a-knowledge-base)
* [Obtenir l’état d’une opération](#get-status-of-an-operation)
* [Générer une réponse à partir de la base de connaissances](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier **Program.cs** dans votre éditeur ou votre IDE favori. Remplacez le code `using` existant par les directives `using` suivantes :

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifier le client pour la création de la base de connaissances

Dans la méthode **main**, créez une variable pour la clé Azure de votre ressource extraite à partir d'une variable d’environnement nommée `QNAMAKER_SUBSCRIPTION_KEY`. Si vous avez créé la variable d’environnement après le lancement de l’application, l’éditeur, l’IDE ou le shell l’exécutant doit être fermé et rechargé pour accéder à la variable. Les méthodes seront créées ultérieurement.

Ensuite, créez un objet [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet).

|Variable d’environnement|variable| Exemple|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|La clé est une chaîne de 32 caractères qui est disponible sur le portail Azure, dans la ressource QnA Maker, dans la page Démarrage rapide. Il ne s’agit pas de la clé du point de terminaison de prédiction.|
|`QNAMAKER_HOST`|`Endpoint`| Votre point de terminaison de création, au format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, comprend votre **nom de ressource**. Il ne s’agit pas de l’URL employée pour interroger le point de terminaison de prédiction.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Authentifier le runtime pour générer une réponse

Dans la méthode **main**, créez une variable pour l’authentification de votre ressource extraite à partir des variables d’environnement `QNAMAKER_ENDPOINT_HOSTNAME` et `QNAMAKER_ENDPOINT_KEY`. Quand vous publiez votre base de connaissances, ces valeurs sont retournées. Après la publication, vous pouvez trouver ces paramètres dans la page **Settings** du portail QnA Maker.

Créez un [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) pour interroger la base de connaissances afin de générer une réponse ou d’effectuer un entraînement par le biais de l’apprentissage actif.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey&highlight=3)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet).
* Pour les **fichiers**, utilisez l'objet [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet).
* Pour les **URL**, utilisez une liste de chaînes.

Appelez la méthode [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet), puis transmettez l’ID d’opération retourné à la méthode [MonitorOperation](#get-status-of-an-operation) pour interroger l’État.

La dernière ligne du code suivant retourne l’ID de la base de connaissances à partir de la réponse de MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=30)]

Veillez à inclure la fonction [`MonitorOperation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de créer une base de connaissances.

## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en transmettant l’ID de la base de connaissances et un [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) contenant des objets DTO [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) et [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) à la méthode [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet). Utilisez la méthode [MonitorOperation](#get-status-of-an-operation) pour déterminer si la mise à jour a réussi.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Veillez à inclure la fonction [`MonitorOperation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de mettre à jour une base de connaissances.

## <a name="download-a-knowledge-base"></a>Télécharger une base de connaissances

Utilisez la méthode [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) pour télécharger la base de données sous la forme d’une liste de [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Ceci n'est _pas_ équivalent à l’exportation à partir de la page **Paramètres** du portail QnA Maker, car le résultat de cette méthode n’est pas un fichier TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur un point de terminaison.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Générer une réponse à partir de la base de connaissances

Générez une réponse à partir d’une base de connaissances publiée à l’aide de la méthode [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet). Cette méthode accepte l’ID de la base de connaissances et [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Accédez à des propriétés supplémentaires de QueryDTO comme [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) et [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet), que vous pouvez utiliser dans votre chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) avec comme paramètre l’ID de la base de connaissances.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) est retournée. Utilisez l'[ID de l’opération](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine.

La _boucle_ et _Task.Delay_ dans le bloc de code suivant sont utilisés pour simuler la logique de nouvelle tentative. Vous devez les remplacer par votre propre logique de nouvelle tentative.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `dotnet run` à partir du répertoire de votre application.

Tous les extraits de code de cet article sont [disponibles](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) et peuvent être exécutés en tant que fichier unique.

```dotnetcli
dotnet run
```

Le [code source de ce guide de démarrage rapide](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) est disponible dans le dépôt GitHub d’exemples en C# pour QnA Maker.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)