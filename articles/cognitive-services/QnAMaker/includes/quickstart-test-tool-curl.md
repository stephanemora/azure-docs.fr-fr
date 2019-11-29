---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424171"
---
Ce démarrage rapide basé sur cURL vous montre pas à pas comment obtenir une réponse de votre base de connaissances.

## <a name="prerequisites"></a>Prérequis

* Dernière version de [**cURL**](https://curl.haxx.se/).
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md) et d’une [base de connaissances avec des questions et des réponses](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publier pour obtenir un point de terminaison

Quand vous êtes prêt à générer une réponse à une question à partir de votre base de connaissances, [publiez](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) votre base de connaissances.

## <a name="use-production-endpoint-with-curl"></a>Utiliser un point de terminaison de production cURL

Une fois votre base de connaissances publiée, la page **Publier** affiche les paramètres de requête HTTP pour générer une réponse. L’onglet **CURL** affiche les paramètres nécessaires à la génération d’une réponse à partir de l’outil de ligne de commande, [CURL](https://www.getpostman.com).

[![Publier les résultats](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Pour générer une réponse avec CURL, effectuez les étapes suivantes :

1. Copiez le texte sous l’onglet CURL. 
1. Ouvrez une ligne de commande ou un terminal et collez le texte.
1. Rectifiez la question pour la rendre pertinente par rapport à base de connaissances. Veillez à ne pas supprimer le JSON contenant la question.
1. Entrez la commande. 
1. La réponse comprend les informations pertinentes. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Utiliser le point de terminaison de mise en lots avec cURL

Si vous voulez obtenir une réponse du point de terminaison de mise en lots, utilisez la propriété de corps `isTest`.

```json
isTest:true
```


