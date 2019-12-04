---
title: Ressources du développeur - Language Understanding
titleSuffix: Azure Cognitive Services
description: Les développeurs ont à leur disposition des API REST et des kits SDK pour implémenter Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: 1cde28bff48bc239e22fe47a882e3218bd2d938b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278562"
---
# <a name="developer-resources-for-language-understanding"></a>Ressources du développeur pour implémenter Language Understanding

Les développeurs peuvent utiliser des API REST et des kits SDK pour implémenter Language Understanding. 

## <a name="azure-resource-management"></a>Gestion des ressources Azure

Utilisez la couche de gestion d’Azure Cognitive Services pour créer, modifier, lister et supprimer la ressource Language Understanding ou Cognitive Services.

Recherchez de la documentation de référence basée sur l’outil :

* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Demandes de création et de prédiction Language Understanding

Le service Language Understanding est accessible à partir d’une ressource Azure que vous devez créer. Il existe deux ressources : les ressources de création et les ressources du point de terminaison de prédiction. Ces deux ressources vous permettent de contrôler vos ressources LUIS. 

Apprenez-en davantage sur le [point de terminaison de prédiction V3](luis-migration-api-v3.md).

### <a name="rest-apis"></a>API REST

Les API de création et de point de terminaison de prédiction sont disponibles à partir d’API REST :

|Type|Version|
|--|--|
|Création|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[V3 en préversion](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Prédiction|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>Kits SDK basés sur le langage

|Langage |Documentation de référence|Package|Exemples|Démarrages rapides|
|--|--|--|--|--|
|C#|[Création](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Prédiction](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet - création](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet - prédiction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Exemples du kit SDK .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Créer et gérer une application](sdk-csharp-quickstart-authoring-app.md)<br>[Interroger le point de terminaison de prédiction](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Création et prédiction](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Kit SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Création](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Prédiction](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Création et prédiction à l’aide de REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Création et prédiction](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven - création](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven - prédiction](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Création](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Prédiction](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Création et prédiction](luis-get-started-get-intent-from-rest.md)
|Node.js|[Création](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Prédiction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM - création](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM - prédiction](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Création](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Prédiction](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Création et prédiction à l’aide de REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Création et prédiction](sdk-python-quickstart-authoring-app.md)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Création](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Création](sdk-python-quickstart-authoring-app.md)<br>[Prédiction à l’aide de REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

LUIS (Language Understanding) propose un [conteneur](luis-container-howto.md) qui permet de fournir des versions locales et autonomes de votre application. 

### <a name="export-and-import-formats"></a>Formats d'exportation et d'importation

Language Understanding vous permet de gérer votre application et ses modèles au format JSON, au format `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)), et sous forme de package compressé pour le conteneur Language Understanding. 

L'importation et l'exportation de ces formats sont disponibles à partir des API et du portail LUIS. Le portail permet l'importation et l'exportation à partir de la liste Applications et de la liste Versions. 

## <a name="other-tools-and-sdks"></a>Autres outils et kits SDK

Bot Framework est disponible en tant que [kit SDK](https://github.com/Microsoft/botframework) dans différents langages et en tant que service avec [Azure Bot Service](https://dev.botframework.com/). 

Bot Framework fournit [plusieurs outils](https://github.com/microsoft/botbuilder-tools) pour vous aider à utiliser Language Understanding, notamment :

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) : créez des modèles de compréhension de la langue LUIS à l’aide de fichiers Markdown.
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) : créez et gérez vos applications Luis.ai.
* [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch) : gérez des applications parentes et enfants.
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) : générez automatiquement des classes C#/Typescript de support pour vos intentions et entités LUIS.
* [Bot emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) : application de bureau permettant aux développeurs de bots de tester et de déboguer les bots créés à l’aide du kit SDK Bot Framework.


## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [codes d’erreur HTTP](luis-reference-response-codes.md) courants
* [Documentation de référence](https://docs.microsoft.com/azure/index#pivot=sdkstools) pour l'ensemble des API et des kits de développement logiciel (SDK)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) et [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Conteneurs cognitifs](../cognitive-services-container-support.md)