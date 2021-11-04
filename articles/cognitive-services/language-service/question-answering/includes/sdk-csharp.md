---
title: 'Démarrage rapide : bibliothèque de client de réponses aux questions personnalisées pour .NET'
description: Ce guide de démarrage rapide montre comment bien démarrer avec la bibliothèque cliente QnA Maker pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.
ms.topic: include
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c9125eab0ec8ccfe7ee974a9935e92e443140b9e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029035"
---
Utilisez la bibliothèque de client de réponses aux questions personnalisées pour .NET afin de :

 * Créer une base de connaissances
 * Mettre à jour une base de connaissances
 * Publier une base de connaissances
 * Attendre l’exécution d’une tâche de longue durée
 * Télécharger une base de connaissances
 * Obtenir une réponse d’une base de connaissances
 * Supprimer une base de connaissances

[Documentation de référence](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* L’[IDE Visual Studio](https://visualstudio.microsoft.com/vs/) ou la version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Les questions et réponses personnalisées nécessitent une [ressource Langage](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) avec la fonctionnalité de réponses aux questions personnalisées activée pour générer une clé d’API et un point de terminaison. <!--TODO: Change link-->
    * Après le déploiement de votre ressource de langage, sélectionnez **Accéder à la ressource**. Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API QnA Maker. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.

## <a name="setting-up"></a>Configuration

### <a name="cli"></a>Interface de ligne de commande

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `question-answering-quickstart`. Cette commande crée un projet C# simple nommé « Hello World » avec un seul fichier source : *program.cs*.

```console
dotnet new console -n question-answering-quickstart
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

Dans le répertoire de l’application, installez la bibliothèque de réponses aux questions personnalisées pour .NET à l’aide de la commande suivante :

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

### <a name="using-directives"></a>using, directives

À partir du répertoire du projet, ouvrez le fichier *program.cs* et ajoutez ce qui suit en utilisant les directives `using` suivantes :

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

### <a name="subscription-key-and-resource-endpoints"></a>Clé d’abonnement et points de terminaison de ressource

Dans la méthode `Main` de l’application, ajoutez des variables et du code, comme indiqué dans la section ci-dessous, pour utiliser les tâches courantes dans ce guide de démarrage rapide.

<!-- TODO: Replace Link
- We use subscription key and authoring key interchangably. For more details on authoring key, follow [Keys](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).
-->

- Le format de la valeur de QNA_MAKER_ENDPOINT est `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Accédez au portail Azure, puis recherchez la ressource Analyse de texte que vous avez créée à l’étape des prérequis. Sélectionnez la page **Clés et point de terminaison**, sous **Gestion des ressources** pour trouver la clé de création (abonnement) et le point de terminaison.

    > [!div class="mx-imgBorder"]
    > ![Point de terminaison de création de questions-réponses personnalisées](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)


- Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

    [!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

## <a name="object-models"></a>Modèles objet

Les [réponses aux questions personnalisées](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) utilisent le modèle objet suivant :
* **[QnAMakerClient](#qnamakerclient-object-model)** est l’objet utilisé pour créer, gérer, publier, télécharger et interroger la base de connaissances.


[!INCLUDE [Get KBinformation](../../../qnamaker/includes/quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modèle objet QnAMakerClient

Le client de création est un objet [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient votre clé.

Une fois le client créé, utilisez la propriété [Knowledge base](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) pour créer, gérer et publier votre base de connaissances.

Gérez votre base de connaissances en envoyant un objet JSON. Pour les opérations immédiates, une méthode retourne généralement un objet JSON indiquant l’état. Pour les opérations de longue durée, la réponse est l’ID d’opération. Appelez la méthode [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) avec l’ID d’opération pour déterminer l’[état de la requête](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Modèle objet QnAMakerRuntimeClient

 La réponse à une question personnalisée ne nécessite pas l’utilisation de l’objet **QnAMakerRuntimeClient**. Vous appelez plutôt la méthode [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync).

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client QnA Maker pour .NET :

* [Authentifier le client de création](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Créer une base de connaissances](#create-a-knowledge-base)
* [Mettre à jour une base de connaissances](#update-a-knowledge-base)
* [Télécharger une base de connaissances](#download-a-knowledge-base)
* [Publier une base de connaissances](#publish-a-knowledge-base)
* [Supprimer une base de connaissances](#delete-a-knowledge-base)
* [Obtenir l’état d’une opération](#get-status-of-an-operation)
* [Générer une réponse à partir de la base de connaissances](#generate-an-answer-from-the-knowledge-base)

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifier le client pour la création de la base de connaissances

Instanciez un objet client avec votre clé, puis utilisez-le avec votre ressource pour construire le point de terminaison et créer un [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) avec votre point de terminaison et votre clé. Créez un objet [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials).

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto).
    * Pour utiliser les métadonnées et les invites de suivi, utilisez le contexte éditorial, car cette donnée est ajoutée au niveau de chaque paire question/réponse.
* Pour les **fichiers**, utilisez l'objet [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto). FileDTO contient le nom de fichier et l’URL publique d’accès au fichier.
* Pour les **URL**, utilisez une liste de chaînes pour représenter les URL disponibles publiquement.

L’étape de création comprend également les propriétés de la base de connaissances :
* `defaultAnswerUsedForExtraction` : données retournées quand aucune réponse n’est trouvée
* `enableHierarchicalExtraction` : créez automatiquement des relations d’invite entre les paires question/réponse extraites
* `language` : quand vous créez la première base de connaissances d’une ressource, définissez le langage à utiliser dans l’index Recherche Azure.

Appelez la méthode [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync), puis transmettez l’ID d’opération retourné à la méthode [MonitorOperation](#get-status-of-an-operation) pour interroger l’État.

La dernière ligne du code suivant retourne l’ID de la base de connaissances à partir de la réponse de MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

Veillez à inclure la fonction [`MonitorOperation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de créer une base de connaissances.

## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en transmettant l’ID de la base de connaissances et un [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) contenant des objets DTO [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) et [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) à la méthode [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync). Utilisez la méthode [MonitorOperation](#get-status-of-an-operation) pour déterminer si la mise à jour a réussi.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

Veillez à inclure la fonction [`MonitorOperation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de mettre à jour une base de connaissances.

## <a name="download-a-knowledge-base"></a>Télécharger une base de connaissances

Utilisez la méthode [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) pour télécharger la base de données sous la forme d’une liste de [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Ceci n’est _pas_ équivalent à l’exportation depuis la page **Paramètres** du portail QnA Maker, car le résultat de cette méthode n’est pas un fichier.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur votre point de terminaison. Cette étape est nécessaire pour interroger votre base de connaissances.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Générer une réponse à partir de la base de connaissances

Générez une réponse à partir d’une base de connaissances publiée à l’aide de la méthode [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync). Cette méthode accepte l’ID de la base de connaissances et [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Accédez à des propriétés supplémentaires de QueryDTO comme [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) et [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest), que vous pouvez utiliser dans votre chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

<!-- TODO: Replace Link
This is a simple example querying the knowledgebase. To understand advanced querying scenarios, review [other query examples](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).
-->

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) avec comme paramètre l’ID de la base de connaissances.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]


## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) est retournée. Utilisez l'[ID de l’opération](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine.

La boucle et `Task.Delay` dans le bloc de code suivant sont utilisés pour simuler la logique de nouvelle tentative. Vous devez les remplacer par votre propre logique de nouvelle tentative.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `dotnet run` à partir du répertoire de votre application.

```dotnetcli
dotnet run
```

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).
