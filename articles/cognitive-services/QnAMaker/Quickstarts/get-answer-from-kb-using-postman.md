---
title: 'Démarrage rapide : Utiliser Postman pour obtenir une réponse à partir d’une base de connaissances - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce démarrage rapide vous aide à obtenir par programmation une réponse à partir de votre base de connaissances à l’aide de Postman.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: d12051821faa60940bf2acc569c6552561b4b3ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267938"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Démarrage rapide : Obtenir une réponse à partir d’une base de connaissances à l’aide de Postman

Ce démarrage rapide basé sur Postman vous aide à obtenir une réponse à partir de votre base de connaissances.

## <a name="prerequisites"></a>Prérequis

* Dernière version de [**Postman**](https://www.getpostman.com/).
* Vous devez avoir un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md) et une [base de connaissances avec des questions et réponses](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publier pour obtenir un point de terminaison

Lorsque vous êtes prêt à générer une réponse à une question à partir de votre base de connaissances, [publiez](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) votre base de connaissances.

## <a name="use-production-endpoint-with-postman"></a>Utiliser un point de terminaison de production avec Postman

Lorsque votre base de connaissances est publiée, la page **Publier** affiche les paramètres de requête HTTP pour générer une réponse. L’affichage par défaut montre les paramètres requis pour générer une réponse à partir de [Postman](https://www.getpostman.com).

Les nombres jaunes figurant dans l’image suivante indiquent les paires nom/valeur à utiliser dans les étapes suivantes.

[![Ppublier les résultats](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Pour générer une réponse avec Postman, procédez comme suit :

1. Ouvrez Postman. Si vous êtes invité à choisir un bloc de construction, sélectionnez le bloc de construction **Basic Request** (Requête de base). Définissez le **nom de la requête** comme `Generate QnA Maker answer` et la **collection** comme `Generate QnA Maker answers`. Si vous ne souhaitez pas enregistrer dans une collection, sélectionnez le bouton **Annuler**.
1. Dans l’espace de travail, sélectionnez la méthode HTTP de **POST**.

    [![IDans Postman, définissez la méthode POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Pour l’URL, concaténez la valeur HOST (n° 2 dans l’image) et la valeur Post (n° 1 dans l’image) pour créer l’URL complète. Un exemple d’URL complète ressemble à : 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![IDans Postman, définissez l’URL complète](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Sélectionnez l’onglet **En-têtes** sous l’URL, puis sélectionnez **Modification en bloc**. 

1. Copiez les en-têtes (n° 3 et n° 4 dans l’image) dans la zone de texte.

    [![IDans Postman, définissez les en-têtes](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Sélectionnez l’onglet **Corps** .
1. Sélectionnez le format **brut** et entrez le JSON (n° 5 dans l’image) qui représente la question.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![IDans Postman, définissez la valeur JSON du corps](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Sélectionnez le bouton **Envoyer**.
1. La réponse contient la réponse, ainsi que d’autres informations qui peuvent être importantes pour l’application cliente. 

    [![IDans Postman, définissez la valeur JSON du corps](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Utiliser le point de terminaison de mise en lots

Si vous souhaitez obtenir une réponse du point de terminaison de mise en lots, ajoutez l’URL avec le paramètre booléen querystring `isTest` avec la valeur `true`.

`?isTest=true`

## <a name="next-steps"></a>Étapes suivantes

La page de publication fournit également des informations pour [générer une réponse](get-answer-from-kb-using-curl.md) avec cURL. 

> [!div class="nextstepaction"]
> [Utiliser des métadonnées lors de la génération d’une réponse](../How-to/metadata-generateanswer-usage.md)
