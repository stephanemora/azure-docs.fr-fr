---
title: 'Guide de démarrage rapide : Publier une base de connaissances - REST, Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce démarrage rapide vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: e1e349f4ddbebdd9df38d7f0babf50d726241d4f
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648732"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Guide de démarrage rapide : Publier une base de connaissances dans QnA Maker à l’aide de Node.js

Ce démarrage rapide vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Publier](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) : cette API ne nécessite aucune information dans le corps de la requête.

## <a name="prerequisites"></a>Prérequis

* [Node.js 6+](https://nodejs.org/en/download/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 
* ID de la base de connaissances QnA Maker se trouvant dans l’URL du paramètre de chaîne de requête kbid, comme indiqué ci-dessous.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Créer un fichier Node.js de base de connaissances

Créez un fichier appelé `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `publish-knowledge-base.js`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Ajouter les constantes nécessaires

Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez la valeur de la variable `subscriptionKey` par votre clé QnA Maker. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Ajouter l’ID de base de connaissances

Après les constantes précédentes, ajoutez l’ID de base de connaissances et ajoutez-le au chemin d’accès :

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-supporting-functions"></a>Ajouter des fonctions de prise en charge

Ajoutez ensuite les fonctions de prise en charge suivantes.

1. Ajoutez la fonction suivante pour imprimer JSON dans un format lisible :

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=25-28 "Add supporting functions, step 1")]

2. Ajoutez les fonctions suivantes pour gérer la réponse HTTP afin d’obtenir l’état de l’opération de création :

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=30-52 "Add supporting functions, step 2")]

## <a name="add-the-publishkb-function-and-main-function"></a>Ajouter la fonction publish_kb et la fonction principale

Le code suivant envoie une requête HTTPS à l’API QnA Maker afin de publier une base de connaissances et reçoit la réponse :

[!code-nodejs[Add POST request to publish KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=54-71 "Add POST request to publish KB")]

[!code-nodejs[Add the publish_kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=73-91 "Add the publish_kb function and main function")]

## <a name="add-the-main-function"></a>Ajouter la fonction principale

Ajoutez la fonction suivante pour gérer la requête et la réponse :

[!code-nodejs[Add the main function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=94-97 "Add the main function")]

## <a name="run-the-program"></a>Exécuter le programme

Générez et exécutez le programme. Il envoie automatiquement la requête à l’API QnA Maker visant à publier la base de connaissances, puis la réponse est affichée dans la fenêtre de console.

Une fois votre base de connaissances publiée, vous pouvez l’interroger à partir du point de terminaison à l’aide d’une application client ou d’un chatbot. 

```bash
node publish-knowledge-base.js
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)