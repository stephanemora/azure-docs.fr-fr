---
title: 'Démarrage rapide : Créer une base de connaissances - REST, Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST vous guide dans la création, par programmation, d’un exemple de base de connaissances QnA Maker qui apparaîtra dans le tableau de bord Azure de votre compte d’API Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 4d9766a0147dfc88f440bf5f44e94405aab018f3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563037"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>Démarrage rapide : Publier une base de connaissances dans QnA Maker à l’aide de Node.js

Ce guide de démarrage rapide vous aide à créer et publier par programmation un exemple de base de connaissances QnA Maker. QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, de [sources de données](../Concepts/data-sources-supported.md). Le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 

Ce démarrage rapide fait appel aux API QnA Maker :
* [Créer la base de connaissances](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obtenir les détails de l’opération](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Prérequis

* [Node.js 6+](https://nodejs.org/en/download/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Créer un fichier Node.js de base de connaissances

Créez un fichier appelé `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `create-new-knowledge-base.js`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires
Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez la valeur de la variable `subscriptionKey` par votre clé QnA Maker.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Ajouter la définition du modèle de base de connaissances

Après les constantes, ajoutez la définition du modèle de base de connaissances suivante. Après la définition, le modèle est converti en une chaîne.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Ajouter des fonctions de prise en charge

Ajoutez ensuite les fonctions de prise en charge suivantes.

1. Ajoutez la fonction suivante pour imprimer JSON dans un format lisible :

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Ajoutez les fonctions suivantes pour gérer la réponse HTTP :

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Ajouter des fonctions pour créer la base de connaissances

Ajoutez les fonctions suivantes pour exécuter une requête HTTP POST afin de créer la base de connaissances. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

Cet appel d’API renvoie une réponse JSON qui inclut l’ID d’opération. Utilisez l’ID d’opération pour déterminer si la base de connaissances a bien été créée. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-functions-to-determine-creation-status"></a>Ajouter des fonctions pour déterminer l’état de création

Ajoutez la fonction suivante pour exécuter une requête HTTP GET afin de vérifier l’état de l’opération. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification. 

[!code-nodejs[Determine creation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=112-135 "Determine creation status")]

Répétez l’appel jusqu’à ce que l’opération réussisse ou échoue : 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```


## <a name="add-create-kb-function"></a>Ajouter une fonction create-kb

La fonction suivante est la fonction principale, crée la base de connaissances et répète les vérifications de l’état. L’**ID d’opération** _create_ (créer) est renvoyé dans le champ **Emplacement** de l’en-tête de réponse POST. Il est ensuite utilisé dans le routage de la requête GET. Étant donné que la création de la base de connaissances peut prendre un certain temps, vous devez répéter les appels pour vérifier l’état jusqu’à ce que l’état indique une réussite ou un échec.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Exécuter le programme

Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Il envoie la requête à l’API QnA Maker pour créer la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est imprimée sur la fenêtre de console.

```bash
node create-new-knowledge-base.js
```

Une fois votre base de connaissances créée, vous pouvez l’afficher dans votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
