---
title: 'Démarrage rapide : Utiliser Postman pour obtenir une réponse à partir d’une base de connaissances - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce démarrage rapide vous aide à obtenir par programmation une réponse à partir de votre base de connaissances à l’aide de Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270890"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Démarrage rapide : Obtenir une réponse à partir d’une base de connaissances à l’aide de Postman

Ce démarrage rapide basé sur Postman vous aide à obtenir une réponse à partir de votre base de connaissances.

## <a name="prerequisites"></a>Prérequis

* Dernière version de [**Postman**](https://www.getpostman.com/).
* Vous devez avoir un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md) et une [base de connaissances avec des questions et réponses](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publier pour obtenir un point de terminaison

Lorsque vous êtes prêt à générer une réponse à une question à partir de votre base de connaissances, [publiez](../How-to/publish-knowledge-base.md) votre base de connaissances.

## <a name="use-production-endpoint-with-postman"></a>Utiliser un point de terminaison de production avec Postman

Lorsque votre base de connaissances est publiée, la page **Publier** affiche les paramètres de requête HTTP pour générer une réponse. L’affichage par défaut montre les paramètres requis pour générer une réponse à partir de [Postman](https://www.getpostman.com).

[![Publier les résultats](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Pour générer une réponse avec Postman, procédez comme suit :

1. Ouvrez Postman. 
1. Sélectionnez le bloc de construction pour créer une requête de base.
1. Définissez le **nom de la requête** comme `Generate QnA Maker answer` et la **collection** comme `Generate QnA Maker answers`.
1. Dans l’espace de travail, sélectionnez la méthode HTTP de **POST**.
1. Pour l’URL, concaténez la valeur HOST et la valeur Post pour créer l’URL complète. 

    [![Dans Postman, définir la méthode sur Post et l’URL complète](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Sélectionnez l’onglet **En-têtes** sous l’URL, puis sélectionnez **Modification en bloc**. 
1. Copiez les en-têtes dans la zone de texte.

    [![Dans Postman, définir les en-têtes](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Sélectionnez l’onglet **Corps** .
1. Sélectionnez le format **brut** et entrez le JSON qui représente la question.

    [![Dans Postman, définir la valeur JSON du corps](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Sélectionnez le bouton **Envoyer**.
1. La réponse contient la réponse, ainsi que d’autres informations qui peuvent être importantes pour l’application cliente. 

    [![Dans Postman, définir la valeur JSON du corps](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Utiliser le point de terminaison de mise en lots avec cURL

Si vous souhaitez obtenir une réponse du point de terminaison de mise en lots, utilisez le paramètre booléen querystring `isTest` avec la valeur `true`.

`isTest=true`

## <a name="next-steps"></a>Étapes suivantes

La page de publication fournit également des informations pour [générer une réponse](get-answer-from-kb-using-curl.md) avec cURL. 

> [!div class="nextstepaction"]
> [Utiliser des métadonnées lors de la génération d’une réponse](../How-to/metadata-generateanswer-usage.md)