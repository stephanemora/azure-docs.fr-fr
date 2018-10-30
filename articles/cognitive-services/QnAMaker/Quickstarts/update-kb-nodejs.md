---
title: 'Démarrage rapide : mettre à jour une base de connaissances - REST, Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce démarrage rapide vous aide à mettre à jour une base de connaissances QnA Maker existante par programmation.  Ce code JSON vous permet de mettre à jour une base de connaissances en ajoutant de nouvelles sources de données, en modifiant ou supprimant des sources de données.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 1b92589fe01f171b732c04057dc290f0f32cb31a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647814"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-nodejs"></a>Guide de démarrage rapide : Mettre à jour une base de connaissances dans QnA Maker à l’aide de Node.js

Ce démarrage rapide vous aide à mettre à jour une base de connaissances QnA Maker existante par programmation.  Ce code JSON vous permet de mettre à jour une base de connaissances en ajoutant de nouvelles sources de données, en modifiant ou supprimant des sources de données.

Cette API correspond aux actions de modification, puis d’utilisation du bouton **Save and train** (Enregistrer et effectuer l’apprentissage) dans le portail QnA Maker.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) : le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Prérequis

* [Node.js 6+](https://nodejs.org/en/download/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 
* ID de la base de connaissances QnA Maker se trouvant dans l’URL du paramètre de chaîne de requête kbid, comme indiqué ci-dessous.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Créer un fichier Node.js de base de connaissances

Créez un fichier appelé `update-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `update-knowledge-base.js`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Ajouter les constantes nécessaires
Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez la valeur de la variable `subscriptionKey` par votre clé QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Ajouter l’ID de base de connaissances

Après les constantes précédentes, ajoutez l’ID de base de connaissances et ajoutez-le au chemin d’accès :

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>Ajouter la définition du modèle de mise à jour de base de connaissances

Après les constantes, ajoutez la définition de mise à jour de base de connaissances suivante. La définition de mise à jour comprend trois sections :

* add
* update
* delete

Chaque section peut être utilisée dans la même requête unique envoyée à l’API. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>Ajouter des fonctions de prise en charge

Ajoutez ensuite les fonctions de prise en charge suivantes.

1. Ajoutez la fonction suivante pour imprimer JSON dans un format lisible :

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. Ajoutez les fonctions suivantes pour gérer la réponse HTTP afin d’obtenir l’état de l’opération de création :

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>Ajouter une requête PATCH pour mettre à jour la base de connaissances

Ajoutez les fonctions suivantes pour exécuter une requête HTTP PATCH afin de mettre à jour la base de connaissances. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

Cet appel d’API renvoie une réponse JSON qui inclut l’ID d’opération. L’ID d’opération est nécessaire pour demander l’état si l’opération n’est pas terminée.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>Ajouter une requête GET pour déterminer l’état de l’opération

Vérifiez l’état de l’opération.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

Cet appel d’API renvoie une réponse JSON qui inclut l’état de l’opération : 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Ajouter la méthode update_kb

La méthode suivante met à jour la base de connaissances et répète la vérification de l’état. Étant donné que la création de la base de connaissances peut prendre un certain temps, vous devez répéter les appels pour vérifier l’état jusqu’à ce que l’état indique une réussite ou un échec.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>Exécuter le programme

Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Il envoie la requête à l’API QnA Maker pour mettre à jour la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est imprimée sur la fenêtre de console.

```bash
node update-knowledge-base.js
```

Une fois que votre base de connaissances est mise à jour, vous pouvez l’afficher sur votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)