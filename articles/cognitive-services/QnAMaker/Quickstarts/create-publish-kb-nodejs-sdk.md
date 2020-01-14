---
title: 'Démarrage rapide : Bibliothèque de client QnA Maker pour Node.js'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment commencer avec la bibliothèque cliente QnA Maker pour Node.js. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: a605b5145e393352e8cd6fe18ac97ea749caf4ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447586"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Démarrage rapide : Bibliothèque de client QnA Maker pour Node.js

Découvrez comment utiliser la bibliothèque de client QnA Maker pour Node.js. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.  QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré, comme des documents de questions fréquentes (FAQ), des URL et des manuels de produit.

Utilisez la bibliothèque de client QnA Maker pour Node.js afin de :

* Créer une base de connaissances
* Gérer une base de connaissances
* Publier une base de connaissances

[Documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Exemples Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Configuration

### <a name="create-a-qna-maker-azure-resource"></a>Créer une ressource Azure QnA Maker

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour QnA Maker en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local.

Après avoir obtenu une clé à partir de votre ressource, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la ressource, et nommez-les `QNAMAKER_SUBSCRIPTION_KEY` et `QNAMAKER_HOST`. Utilisez les valeurs de clé et de point de terminaison qui se trouvent dans les pages **Clés** et **Présentation** de la ressource du portail Azure.

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y.

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init -y` pour créer une application de nœud avec un fichier `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `ms-rest-azure` et `azure-cognitiveservices-qnamaker` :

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Le fichier `package.json` de votre application est mis à jour avec les dépendances.


## <a name="object-model"></a>Modèle objet

Le client QnA Maker est un objet [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) qui s’authentifie auprès d’Azure à l’aide de ServiceClientCredentials, qui contient votre clé.

Une fois le client créé, utilisez la propriété [Knowledge base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) pour créer, gérer et publier votre base de connaissances.

Gérez votre base de connaissances en envoyant un objet JSON. Pour les opérations immédiates, une méthode retourne généralement un objet JSON indiquant l’état. Pour les opérations de longue durée, la réponse est l’ID d’opération. Appelez la méthode [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) avec l’ID d’opération pour déterminer l’[état de la requête](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client QnA Maker pour Node.js :

* [Créer une base de connaissances](#create-a-knowledge-base)
* [Mettre à jour une base de connaissances](#update-a-knowledge-base)
* [Publier une base de connaissances](#publish-a-knowledge-base)
* [Supprimer une base de connaissances](#delete-a-knowledge-base)
* [Obtenir l’état d’une opération](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Ajouter les dépendances

Créez un fichier appelé `index.js`. Ajoutez la bibliothèque QnA Maker et la bibliothèque REST Azure au fichier.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

|Variable d’environnement|Variable Node.js|Exemple|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|La clé est une chaîne de 32 caractères qui est disponible sur le portail Azure, dans la ressource QnA Maker, dans la page Démarrage rapide. Il ne s’agit pas de la clé du point de terminaison de prédiction.|
|`QNAMAKER_HOST`|`endpoint`| Votre point de terminaison de création, au format `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, comprend votre **nom de ressource**. Il ne s’agit pas de l’URL employée pour interroger le point de terminaison de prédiction.|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Authentifier le client

Ensuite, créez un objet ServiceClientCredentials avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-). Utilisez l’objet client pour obtenir un objet [knowledge base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest).


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
* Pour les **fichiers**, utilisez l'objet [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest).
* Pour les **URL**, utilisez une liste de chaînes.

Appelez la méthode [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--), puis passer l’ID d’opération retourné à la méthode [Operations.getDetails](#get-status-of-an-operation) pour interroger l’état.

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Veillez à inclure la fonction [`wait_for_operation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de créer une base de connaissances.

## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en passant l’ID de celle-ci et un [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contenant des objets DTO [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) et [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) à la méthode [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). Utilisez la méthode [Operation.getDetail](#get-status-of-an-operation) pour déterminer si la mise à jour a réussi.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Veillez à inclure la fonction [`wait_for_operation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de mettre à jour une base de connaissances.

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur un point de terminaison.

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) avec comme paramètre l’ID de la base de connaissances.

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) est retournée. Utilisez l'[ID de l’opération](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine.

L’appel _setTimeout_ dans le bloc de code suivant est utilisé pour simuler le code asynchrone. Remplacez cela par la logique de nouvelle tentative.

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node index.js` à partir de votre répertoire d’application.


Tous les extraits de code de cet article sont [disponibles](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) et peuvent être exécutés en tant que fichier unique.

```console
node index.js
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Tutoriel : Créer et interroger une base de connaissances](../tutorials/create-publish-query-in-portal.md)

* [Qu’est-ce que l’API QnA Maker ?](../Overview/overview.md)
* [Modifier une base de connaissances](../how-to/edit-knowledge-base.md)
* [Obtenir une analyse de l'utilisation](../how-to/get-analytics-knowledge-base.md)