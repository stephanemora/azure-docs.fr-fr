---
title: 'Tutoriel : Créer, publier et répondre dans QnA Maker'
description: Ce tutoriel montre comment créer une base de connaissances à partir des questions et réponses d’une page web publique de questions fréquentes (FAQ). Enregistrez, entraînez et publiez la base de connaissances. Une fois la base de connaissances publiée, envoyez une question et recevez une réponse avec une commande cURL. Ensuite, créez un bot et testez-le avec la même question.
ms.topic: tutorial
ms.date: 01/28/2020
ms.openlocfilehash: 07a92b0f24aa95fc4a5188f54c7194fd342409fb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844154"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Tutoriel : Dans le portail QnA Maker, créer une base de connaissances

Créez une base de connaissances avec des questions et réponses à partir d’un forum aux questions web public. Enregistrez, entraînez et publiez la base de connaissances. Une fois la base de connaissances publiée, envoyez une question et recevez une réponse avec une commande cURL. Ensuite, créez un bot et testez-le avec la même question.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une base de connaissances dans le portail QnA Maker.
> * Passer en revue, enregistrer et entraîner la base de connaissances.
> * Publier la base de connaissances.
> * Utiliser cURL pour interroger la base de connaissances.
> * Créer un bot.


> [!NOTE]
> La version programmatique de ce tutoriel est disponible avec une solution complète dans le dépôt GitHub [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Conditions préalables requises

Ce tutoriel nécessite un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md) existant.

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

1. Se connecter au portail [QnA Maker](https://www.qnamaker.ai).

1. Sélectionnez **Créer une base de connaissances** dans le menu supérieur.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignorez la première étape, car vous allez utiliser votre service QnA Maker existant.

1. Sélectionnez vos paramètres existants :

    |Paramètre|Objectif|
    |--|--|
    |ID Microsoft Azure Active Directory|Cet ID est associé au compte que vous utilisez pour vous connecter au portail Azure et au portail QnA Maker. |
    |Nom de l’abonnement Azure|Le compte de facturation où vous avez créé la ressource QnA Maker.|
    |Azure QnA Service|Votre ressource QnA Maker existante.|
    |Langage|Langue utilisée pour toutes les bases de connaissances en service. Ne mélangez pas les langues dans une même ressource. Cela risque de nuire à la qualité des réponses.|

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Entrez le nom de votre base de connaissances, `My Tutorial kb`.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Remplissez votre base de connaissances avec les paramètres suivants :

    |Nom du paramètre|Valeur de paramètre|Objectif|
    |--|--|--|
    |Activer l’extraction multitour|Activé|Permet d’activer les invites de suivi.|
    |Texte de réponse par défaut|`No good answer was found.`|Retourné quand il n’existe aucune correspondance avec la question.|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Le contenu de la FAQ à cette URL est mis en forme avec une question suivie d’une réponse. QnA Maker peut interpréter ce format pour extraire les questions et les réponses associées.|
    |Fichier |_non utilisé dans ce tutoriel_|Cela télécharge les fichiers des questions et réponses. |
    |Personnalité d’échanges de conversation|Convivial|Cela donne une [personnalité](../Concepts/best-practices.md#chit-chat) amicale et décontractée aux questions et réponses communes. Vous pouvez modifier ces questions et réponses plus tard. |

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Sélectionnez **Créer votre base de connaissances** pour terminer le processus de création.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Réviser, enregistrer et former la base de connaissances

1. Réviser les questions et réponses. La première page contient les questions et réponses de l’URL.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Sélectionnez la dernière page de questions et réponses à partir du bas de la table. La page affiche les questions et réponses de la personnalité d’échanges de conversation.

1. Dans la barre d’outils au-dessus de la liste des questions et réponses, sélectionnez l’icône **View options** (Afficher les options), puis **Show metadata** (Afficher les métadonnées). Des balises de métadonnées apparaissent alors pour chaque question et réponse. Les questions d’échanges de conversation ont les métadonnées **editorial: chit-chat** déjà définies. Ces métadonnées sont retournées à l’application cliente avec la réponse sélectionnée. L’application cliente, par exemple un bot conversationnel, peut utiliser ces métadonnées filtrées pour déterminer un traitement ou des interactions supplémentaires avec l’utilisateur.

    ![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Sélectionnez **Enregistrer et effectuer l’apprentissage** dans la barre de menus supérieure.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publier pour obtenir les points de terminaison de la base de connaissances

Sélectionnez le bouton **Publier** dans le menu supérieur. Sur la page de publication, sélectionnez **Publier**.

![Capture d’écran du portail QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Une fois la base de connaissances publiée, le point de terminaison s’affiche.

![Capture d’écran des paramètres du point de terminaison](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Ne fermez pas cette page **Publish** (Publier). Vous en aurez besoin plus tard dans ce tutoriel pour créer un bot.

## <a name="use-curl-to-query-for-an-faq-answer"></a>Utiliser cURL pour demander une réponse du Forum aux questions

1. Sélectionnez l’onglet **Curl**.

    ![Capture d’écran de l’onglet cURL](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copiez le texte de l’onglet **cURL**, et exécutez-le dans un terminal prenant en charge cURL ou sur une ligne de commande . La valeur de l’en-tête d’autorisation inclut le texte `Endpoint`, suivi d’un espace de fin, puis de la clé.

1. Remplacez `<Your question>` par `How large can my KB be?`. Cette question se rapproche de la question, `How large a knowledge base can I create?`, mais ce ne sont pas exactement les mêmes. QnA Maker applique un traitement en langage naturel pour déterminer si deux questions sont les mêmes.

1. Exécutez la commande cURL et recevez la réponse JSON comprenant le score et la réponse.

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Utiliser cURL pour demander une réponse d’échange de conversation (chit-chat)

1. Dans le terminal prenant en charge cURL, remplacez `How large can my KB be?` par une instruction de fin de conversation de bot provenant de l’utilisateur, comme `Thank you`.

1. Exécutez la commande cURL et recevez la réponse JSON comprenant le score et la réponse.

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

    Étant donné que la question de `Thank you` correspond exactement à une question d’échanges de conversation, QnA Maker est totalement confiant avec un score de 100. QnA Maker a également retourné toutes les questions associées, ainsi que la propriété de métadonnées contenant les informations de la balise de métadonnées d’échange de conversation.

## <a name="use-curl-to-query-for-the-default-answer"></a>Utiliser cURL pour demander la réponse par défaut

Toutes les questions pour lesquelles QnA Maker n’a pas de certitude reçoivent la réponse par défaut. Cette réponse peut être configurée dans le portail Azure.

1. Dans le terminal prenant en charge de cURL, remplacez `Thank you` par `x`.

1. Exécutez la commande cURL et recevez la réponse JSON comprenant le score et la réponse.

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

    QnA Maker a retourné un score de `0`, ce qui signifie « pas de certitude ». Il a également retourné la réponse par défaut.

## <a name="create-a-knowledge-base-bot"></a>Créer un bot de base de connaissances

Pour plus d’informations, consultez [Créer un bot conversationnel avec cette base de connaissances](create-qna-bot.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé avec le bot de base de connaissances, supprimez le groupe de ressources `my-tutorial-rg` pour supprimer toutes les ressources Azure créées dans le processus relatif au bot.

Quand vous en avez fini avec la base de connaissances, dans le portail QnA Maker, sélectionnez **My knowledge bases** (Mes bases de connaissances). Ensuite, sélectionnez la base de connaissances **My Tutorial kb**, puis sélectionnez l’icône de suppression à droite de cette ligne.

## <a name="next-steps"></a>Étapes suivantes

Consultez [Sources de données prises en charge](../Concepts/content-types.md) pour plus d’informations sur les formats de fichiers de pris en charge.

En savoir plus sur les [personnalités](../Concepts/best-practices.md#chit-chat) d’échanges de conversation.

Pour plus d’informations sur la réponse par défaut, consultez [Aucune correspondance trouvée](../Concepts/confidence-score.md#no-match-found).

> [!div class="nextstepaction"]
> [Créer un bot conversationnel avec cette base de connaissances](create-qna-bot.md)