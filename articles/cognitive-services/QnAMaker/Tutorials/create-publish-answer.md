---
title: Créez, publiez, répondez
titleSuffix: QnA Maker - Azure Cognitive Services
description: Ce didacticiel basé sur REST vous aide à créer et publier par programmation une base de connaissances, puis à répondre à une question à partir de la base de connaissances.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: d209d73d67af96e99589dddcb71b6b50214356ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877276"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Didacticiel : Créer une base de connaissances puis répondre à une question à l’aide de C#

Ce didacticiel vous aide à créer et publier par programmation une base de connaissances (KB), puis à répondre à une question d’un client à partir de celle-ci. 

> [!div class="checklist"]
> * Créer une base de connaissances 
> * Vérifier l’état de création
> * Entraîner et publier la base de connaissances
> * Obtenir des informations sur les points de terminaison
> * Utiliser Curl pour interroger la base de connaissances


Ce démarrage rapide fait appel aux API QnA Maker :

* [Créer une base de connaissances (kb)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Obtenir des détails sur la base de connaissances](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [Obtenir des points de terminaison sur la base de connaissances](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [Publier](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Prérequis

* La dernière [**édition de Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 

> [!NOTE] 
> Les fichiers solution complets sont disponibles dans le dépôt GitHub [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Créer un projet de base de connaissances

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de Program.cs, remplacez l’instruction unique _using_ par les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Ajouter une classe KBDetails
Ajoutez cette classe KBDetails entre les crochets de l’espace de noms. Cette classe permet à la bibliothèque NewtonSoft de désérialiser la réponse JSON dans une classe C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires

En haut de la classe Program, ajoutez l’instruction suivante pour accéder à QnA Maker :

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Ajouter la définition de base de connaissances

Après les constantes, ajoutez la définition du modèle de base de connaissances suivante.

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Ajouter les fonctions et les structures de prise en charge
Ajoutez le bloc de code suivant dans la classe Program :

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Ajouter une requête POST pour créer la base de connaissances

Le code suivant envoie une requête HTTPS à l’API QnA Maker afin de créer une base de connaissances et reçoit la réponse :

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Cet appel d’API renvoie une réponse JSON qui inclut l’ID d’opération. Plus tard, le programme utilisera l’ID d’opération pour déterminer si la base de connaissances a bien été créée. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Ajouter une requête GET pour déterminer l’état de création

Vérifiez l’état de l’opération de création.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Cet appel d’API renvoie une réponse JSON qui inclut l’état de l’opération : 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

Répétez l’appel jusqu’à ce que l’opération réussisse ou échoue : 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Ajouter une méthode CreateKB

La méthode suivante encapsule les appels afin de créer la base de connaissances et de vérifier l’état.  L’**ID d’opération** _create_ (créer) est renvoyé dans le champ **Emplacement** de l’en-tête de réponse POST. Il est ensuite utilisé dans le routage de la requête GET. Étant donné que la création de la base de connaissances peut prendre un certain temps, vous devez répéter les appels pour vérifier l’état jusqu’à ce que l’état indique une réussite ou un échec. Lorsque l’opération réussit, l’ID de base de connaissances est renvoyé dans **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Ajouter une méthode de publication

Une fois la base de connaissances créée, publiez-la. Vous vous attendez peut-être à un appel d’une API d’apprentissage, mais cela n’est pas nécessaire avec cette version. 

Le code suivant envoie une requête HTTPS à l’API QnA Maker afin de publier une base de connaissances et reçoit la réponse :

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

L’appel API renvoie un état 204 si la publication réussit sans aucun contenu dans le corps de la réponse. Le code de démarrage rapide ajoute du texte pour les réponses 204 afin que vous puissiez voir le résultat.

Toutes les autres réponses sont renvoyées inchangées.

## <a name="generating-an-answer"></a>Générer une réponse
Pour accéder à la base de connaissances afin d’envoyer une question et recevoir la meilleure réponse, le programme a besoin d’obtenir l’_hôte du point de terminaison_ à partir de l’API de détails de la base de connaissances et la _clé du point de terminaison principal_ à partir de l’API des points de terminaison. Ces méthodes sont décrites dans les sections suivantes, ainsi que celle permettant de générer une réponse. 

Le tableau suivant explique comment utiliser les données pour construire un URI :

|Générer un modèle d’URI de réponse|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

Le _point de terminaison principal_ est transféré en tant qu’en-tête afin d’authentifier la requête de génération d’une réponse :

|Nom de l’en-tête|Valeur de l’en-tête|
|--|--|
|Authorization|`Endpoint` + **point de terminaison principal**<br>Exemple : `Endpoint xxxxxxx`<br>Remarquez l’espace entre le texte de `Endpoint` et la valeur du point de terminaison principal. 

Le corps de la requête doit transférer le JSON approprié :

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Obtenir les détails de la base de connaissances
Ajoutez la méthode suivante pour obtenir les détails de la base de connaissances. Ces détails contiennent le nom d’hôte de la base de connaissances. Le nom d’hôte correspond au nom du service web Azure QnA Maker que vous avez saisi lors de la création de la ressource QnA Maker. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Cet appel d’API renvoie une réponse JSON : 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Obtenir les points de terminaison de base de connaissances
Ajoutez la méthode suivante pour obtenir les points de terminaison principaux de QnA Maker. Ces points de terminaison ne sont pas liés à la base de connaissances ; ils sont valides pour toutes les bases de connaissances associées aux clés de ressource QnA Maker dans le portail Azure.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Cet appel d’API renvoie une réponse JSON : 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Obtenir une réponse
Ajoutez la méthode suivante pour obtenir une réponse à la question de l’utilisateur. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Cet appel d’API renvoie une réponse JSON : 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Méthode principale
La méthode principale montre les appels synchrones pour créer, publier et générer la réponse. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Générez et exécutez le programme. 

Une fois votre base de connaissances créée, vous pouvez l’afficher dans votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). Une fois que vous savez comment utiliser l’API de génération de réponses, vous pouvez l’utiliser avec n’importe quelle infrastructure de langage ou de requête HTTP. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
