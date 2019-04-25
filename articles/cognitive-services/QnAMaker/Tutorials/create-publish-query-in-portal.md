---
title: Créer, publier et répondre dans QnA Maker
titleSuffix: Azure Cognitive Services
description: Créez une base de connaissances avec des questions et réponses à partir d’un forum aux questions web public. Enregistrez, entraînez et publiez la base de connaissances. Une fois la base de connaissances publiée, envoyez une question et recevez une réponse avec une commande CURL. Ensuite, créez un bot et testez-le avec la même question.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: diberry
ms.openlocfilehash: 299dd61055503f0b5a11cbe97e137e4760edadda
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266937"
---
# <a name="tutorial-from-qna-maker-portal-create-a-knowledge-base"></a>Didacticiel : Dans le portail QnA Maker, créer une base de connaissances

Créez une base de connaissances avec des questions et réponses à partir d’un forum aux questions web public. Enregistrez, entraînez et publiez la base de connaissances. Une fois la base de connaissances publiée, envoyez une question et recevez une réponse avec une commande Curl. Ensuite, créez un bot et testez-le avec la même question. 

Ce tutoriel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Créer une base de connaissances dans le portail QnA Maker
> * Réviser, enregistrer et former la base de connaissances
> * Publier la base de connaissances
> * Utiliser Curl pour interroger la base de connaissances
> * Créer un bot
> 
> [!NOTE]
> La version programmatique de ce didacticiel est disponible avec une solution complète dans le dépôt GitHub [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Prérequis

Ce tutoriel nécessite un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md) existant. 

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances 

1. Se connecter au portail [QnA Maker](https://www.qnamaker.ai). 

1. Sélectionnez **Créer une base de connaissances** dans le menu supérieur.

    ![Étape 1 du processus de création de la base de connaissances](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignorer la première étape, car vous allez utiliser votre service QnA Maker existant. 

1. Dans l’étape suivante, sélectionnez vos paramètres existants :  

    |Paramètre|Objectif|
    |--|--|
    |ID Microsoft Azure Active Directory|Votre _ID Microsoft Azure Directory_ est associé au compte que vous utilisez pour vous connecter au portail Azure et au portail QnA Maker. |
    |Nom de l’abonnement Azure|Le compte de facturation dans lequel vous avez créé la ressource QnA Maker.|
    |Azure QnA Service|Votre ressource QnA Maker existante.|

    ![Étape 2 du processus de création de la base de connaissances](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Dans l’étape suivante, saisissez le nom de votre base de connaissances, `My Tutorial kb`.

    ![Étape 3 du processus de création de la base de connaissances](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Dans l’étape suivante, remplissez votre base de connaissances avec les paramètres suivants :  

    |Nom du paramètre|Valeur de paramètre|Objectif|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Le contenu de la FAQ à cette URL est mis en forme avec une question suivie d’une réponse. QnA Maker peut interpréter ce format pour extraire les questions et les réponses associées.|
    |Fichier |_non utilisé dans ce tutoriel_|Cela télécharge les fichiers des questions et réponses. |
    |Personnalité d’échanges de conversation|L’ami|Cela donne une personnalité amicale et décontractée aux questions et réponses communes. Vous pouvez modifier ces questions et réponses plus tard. |

    ![Étape 4 du processus de création de la base de connaissances](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Sélectionnez **Créer votre base de connaissances** pour terminer le processus de création.

    ![Étape 5 du processus de création de la base de connaissances](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Réviser la base de connaissances, enregistrer et effectuer l’apprentissage

1. Réviser les questions et réponses. La première page contient les questions et réponses de l’URL. 

    ![Enregistrer et entraîner](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Sélectionnez la dernière page de questions et réponses à partir du bas de la table. La page affiche les questions et réponses de la personnalité d’échanges de conversation. 

1. Sélectionnez l’icône de métadonnées dans la barre d’outils au-dessus de la liste de questions et réponses. Des balises de métadonnées apparaissent alors pour chaque question et réponse. Les questions d’échanges de conversation ont les métadonnées **editorial: chit-chat** déjà définies. Ces métadonnées sont retournées à l’application cliente avec la réponse sélectionnée. L’application cliente, par exemple un bot conversationnel, peut utiliser ces métadonnées filtrées pour déterminer un traitement ou des interactions supplémentaires avec l’utilisateur.

    ![Afficher les balises de métadonnées](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Sélectionnez **Enregistrer et effectuer l’apprentissage** dans la barre de menus supérieure.

## <a name="publish-to-get-kb-endpoints"></a>Publier pour obtenir des points de terminaison de base de connaissances

Sélectionnez le bouton **Publier** dans le menu supérieur. Une fois sur la page Publier, sélectionnez **Publier**, en regard du bouton **Annuler**.

![Publish](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Une fois la base de connaissances publiée, le point de terminaison s’affiche

![Publier des paramètres de point de terminaison de la page](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Ne fermez pas cette page **Publish** : vous allez l’utiliser pour créer un bot ultérieurement dans ce tutoriel. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Utilisez Curl pour demander une réponse du forum aux questions

1. Sélectionnez l’onglet **Curl**. 

    ![Commande Curl](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copiez le texte de l’onglet **Curl** et exécutez-le dans un terminal ou une ligne de commande prenant en charge Curl. La valeur de l’en-tête d’autorisation inclut le texte `Endpoint` avec un espace de fin puis la clé.

1. Remplacez `<Your question>` par `How large can my KB be?`. Cette question se rapproche de la question, `How large a knowledge base can I create?`, mais ce ne sont pas exactement les mêmes. QnA Maker applique un traitement en langage naturel pour déterminer si deux questions sont les mêmes.     

1. Exécutez la commande Curl et recevez la réponse JSON comprenant le score et la réponse. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker est relativement confiant avec un score de 42,81 %.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Utilisez curl pour demander une réponse d’échange de conversation (chit-chat)

1. Dans le terminal prenant en charge Curl, remplacez `How large can my KB be?` par une instruction de fin de conversation de bot provenant de l’utilisateur, comme `Thank you`.   

1. Exécutez la commande Curl et recevez la réponse JSON comprenant le score et la réponse. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Étant donné que la question de `Thank you` correspond exactement à une question d’échanges de conversation, QnA Maker est totalement confiant avec un score de 100. QnA Maker a également retourné toutes les questions associées, ainsi que la propriété de métadonnées contenant les informations de la balise de métadonnées d’échanges de conversation.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Utiliser Curl pour demander la réponse par défaut

Toutes les questions pour lesquelles QnA Maker n’est pas confiant reçoivent une réponse par défaut. Cette réponse peut être configurée dans le portail Azure. 

1. Dans le terminal prenant en charge de Curl, remplacez `Thank you` par `x`. 

1. Exécutez la commande Curl et recevez la réponse JSON comprenant le score et la réponse. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker a retourné un score de `0`, ce qui signifie « pas de confiance », mais il a aussi retourné la réponse par défaut. 

## <a name="create-a-knowledge-base-bot"></a>Créer un bot de base de connaissances

Pour plus d’informations, consultez [Créer un bot conversationnel avec cette base de connaissances](create-qna-bot.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez terminé avec le bot de base de connaissances, supprimez le groupe de ressources `my-tutorial-rg` pour supprimer toutes les ressources Azure créées dans le processus relatif au bot.

Quand vous avez terminé avec la base de connaissances, dans le portail QnA Maker, sélectionnez **My knowledge bases**, sélectionnez la base de connaissances **My Tutorial kb**, puis sélectionnez l’icône de suppression à l’extrême droite de cette ligne.  

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les formats de fichiers pris en charge, consultez [Sources de données prises en charge](../Concepts/data-sources-supported.md). 

En savoir plus sur les [personnalités](../Concepts/best-practices.md#chit-chat) d’échanges de conversation.

Pour plus d’informations sur la réponse par défaut, consultez [Aucune correspondance trouvée](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Créer un bot conversationnel avec cette base de connaissances](create-qna-bot.md)