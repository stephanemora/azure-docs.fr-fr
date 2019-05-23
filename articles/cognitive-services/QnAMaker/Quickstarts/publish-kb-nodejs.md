---
title: Publier une base de connaissances, REST, Node.js
titleSuffix: QnA Maker - Azure Cognitive Services
description: Ce démarrage rapide Node.js vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 915d5223b99f530978ade41f69420766cf591021
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787905"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Démarrage rapide : Publier une base de connaissances dans QnA Maker à l’aide de Node.js

Ce guide de démarrage rapide basé sur REST vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Publier](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) : cette API ne nécessite aucune information dans le corps de la requête.

## <a name="prerequisites"></a>Prérequis

* [Node.js 6+](https://nodejs.org/en/download/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. 
* ID de la base de connaissances QnA Maker se trouvant dans l’URL du paramètre de chaîne de requête kbid, comme indiqué ci-dessous.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](create-new-kb-nodejs.md).


> [!NOTE] 
> Les fichiers solution complets sont disponibles dans le référentiel GitHub [**Azure-Samples/cognitive-services-qnamaker-nodejs**](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short).

## <a name="create-a-knowledge-base-nodejs-file"></a>Créer un fichier Node.js de base de connaissances

Créez un fichier appelé `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `publish-knowledge-base.js`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>Ajouter les constantes nécessaires

Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez les valeurs par les vôtres.

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Ajouter une requête POST pour publier la base de connaissances

Après les constantes nécessaires, ajoutez le code suivant, qui adresse une requête HTTPS à l’API QnA Maker afin de publier une base de connaissances et reçoit la réponse :

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

L’appel API renvoie un état 204 si la publication réussit sans aucun contenu dans le corps de la réponse. Le code ajoute du contenu pour les réponses 204.

Toutes les autres réponses sont renvoyées inchangées.

## <a name="run-the-program"></a>Exécuter le programme

Générez et exécutez le programme. Il envoie automatiquement la requête à l’API QnA Maker visant à publier la base de connaissances, puis la réponse est affichée dans la fenêtre de console.

Une fois votre base de connaissances publiée, vous pouvez l’interroger à partir du point de terminaison à l’aide d’une application client ou d’un chatbot. 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

Une fois la base de connaissances publiée, il faut que l’[URL du point de terminaison génère une réponse](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
