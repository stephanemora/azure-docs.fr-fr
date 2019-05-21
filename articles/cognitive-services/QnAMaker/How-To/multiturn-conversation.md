---
title: Activer plusieurs conversations
titleSuffix: Azure Cognitive Services
description: Utiliser des invites et le contexte pour gérer l’activer plusieurs, appelé multi successives, pour votre robot d’une question à un autre. Activez multi est la possibilité d’avoir une sauvegarde allers et retours conversation où le contexte de la question précédent a un impact sur la question et la réponse suivante.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954866"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Suivi de l’utilisation vous invite à créer plusieurs tours d’une conversation

Permet de gérer l’activer plusieurs, connu sous le nom des invites de suivi et le contexte _tour multi_, pour votre robot d’une question à un autre.

Écoutez le [vidéo de démonstration](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Qu’est une conversation tour multi ?

Certains types de conversation ne peut pas être effectuées dans un seul coup. Lorsque vous concevez vos conversations de (conversationnel) d’application client, un utilisateur peut poser une question qui doit être ou non filtrées affinées afin de déterminer la réponse correcte. Ce flux via les questions est possible à l’aide de l’utilisateur avec **vous invite à entrer un suivi**.

Lorsque l’utilisateur demande la question, QnA Maker renvoie la réponse _et_ les invites de suivi. Cela vous permet de présenter les questions de suivi en tant que choix. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversation multi-d’activer exemple avec conversationnel

Un agent conversationnel gère la conversation, question en question, avec l’utilisateur pour déterminer la réponse finale.

![Au sein du flux conversationnel, gérer l’état de la conversation dans un système de la boîte de dialogue Activer multi en fournissant des invites dans les réponses présentés sous la forme d’options pour poursuivre la conversation.](../media/conversational-context/conversation-in-bot.png)

Dans l’image précédente, question de l’utilisateur doit être affinées avant de renvoyer la réponse. Dans la base de connaissances, la question (1), a quatre invites suivis, présentées dans l’agent conversationnel sous forme de quatre choix (#2). 

Lorsque l’utilisateur sélectionne un choix (3), la liste suivante de raffinage choix (n° 4) est présentée. Cela peut se poursuivre (5) jusqu'à ce que la réponse correcte et finale (6) est déterminée.

Vous devez modifier votre application cliente pour gérer la conversation contextuelle.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Créer une conversation multi tour à partir de la structure du document
Lorsque vous créez une base de connaissances, vous verrez une case à cocher facultative pour activer l’extraction Activez multi. Si vous sélectionnez cette option, lorsque vous importez un document, la conversation activer multi peut être implicite à partir de la structure. Si cette structure existe, QnA Maker crée les paires de QnA suivis invite pour vous. Structure de désactiver multi peut uniquement être déduit à partir d’URL, PDF, DOCX fichiers. 

L’image suivante d’un Microsoft Surface [fichier PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) est destiné à être utilisé comme un manuel. 

![! [Si vous importez un document, contextuelle conversation peut être déduite à partir de la structure. Si cette structure existe, QnA Maker crée les paires de QnA invite suivis, dans le cadre de l’importation du document.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Lorsque vous importez le document PDF, QnA Maker détermine les invites de suivi à partir de la structure pour créer des flux de conversation. 

![! [Lorsque vous importez le document PDF, QnA Maker détermine des invites de suivi à partir de la structure pour créer des flux de conversation. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>Afficher les questions et réponses avec contexte

1. Réduire les paires questions et réponses affichées pour seulement ceux dotés de conversations contextuelles. Sélectionnez **afficher les options**, puis sélectionnez **Show contexte (version préliminaire)**. La liste sera vide jusqu'à ce que vous ajoutiez la première paire de questions / réponses avec une invite de suivi. 

    ![Filtrer les question et répondre des paires par conversations contextuelles](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Ajouter la nouvelle paire de QnA comme invite suivi

1. Sélectionnez **QnA ajouter paire**. 
1. Entrez le nouveau texte de question, `Give feedback.` avec une réponse de `What kind of feedback do you have?`.

1. Dans le **réponse** colonne pour cette question, sélectionnez **invite suivi ajouter**. 
1. Le **invite un suivi (version préliminaire)** fenêtre contextuelle vous permet de rechercher une question existante ou entrez une nouvelle question. Créer une nouvelle invite en entrant les valeurs suivantes : 

    |Champ de texte|Valeur|
    |--|--|
    |**Texte affiché**|`Feedback on an QnA Maker service`|
    |**Lien pour répondre à**|`How would you rate QnA Maker??`|
    |||

    ![Créer nouveau QnA invite](../media/conversational-context/create-child-prompt-from-parent.png)

1. Vérifiez **contexte uniquement**. Le **contexte seule** option indique que le texte de l’utilisateur sera compris _uniquement_ si donné en réponse à la question précédente. Pour ce scénario, le texte d’invite n’est pas judicieux tout comme une question autonome, il convient uniquement à partir du contexte de la question précédente.
1. Sélectionnez **créer** puis sélectionnez **enregistrer**. 

    Cela créé une nouvelle paire de questions-réponses et lié la question sélectionnée en tant qu’une invite de suivi. Le **contexte** colonne, pour ces deux questions, indique une relation de message suivie. 

    ![! [La colonne de contexte, pour ces deux questions, indique une relation de message suivie.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Sélectionnez **invite suivi ajouter** pour le `Give feedback` question pour ajouter une autre invite de suivi. Cette opération ouvre le **invite un suivi (version préliminaire)** fenêtre contextuelle.

1. Créer une nouvelle invite en entrant les valeurs suivantes :

    |Champ de texte|Valeur|
    |--|--|
    |**Texte affiché**|`Feedback on an existing feature`|
    |**Lien pour répondre à**|`Which feature would you like to give feedback on?`|
    |||

1. Vérifiez **contexte uniquement**. Le **contexte seule** option indique que le texte de l’utilisateur sera compris _uniquement_ si donné en réponse à la question précédente. Pour ce scénario, le texte d’invite n’est pas judicieux tout comme une question autonome, il convient uniquement à partir du contexte de la question précédente.

1. Sélectionnez **Enregistrer**. 

    Cela créé une nouvelle question et lié la question comme une question subsidiaire invite au `Give feedback` question.
    
    À ce stade, la question supérieure a deux invites suivis liés à la question précédente, `Give feedback`.

    ![! [À ce stade, la question supérieure a deux invites suivis liés à la question précédente, « Commentaires ».] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Sélectionnez **enregistrer et effectuer l’apprentissage** pour former la base de connaissances avec les nouvelles questions. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Ajouter une paire de QnA existant en tant que l’invite de suivi

1. Si vous souhaitez lier une paire de QnA existante comme une invite de suivi, sélectionnez la ligne de la paire questions et réponses.
1. Sélectionnez **invite suivi ajouter** dans cette ligne.
1. Dans le **invite un suivi (version préliminaire)** fenêtre contextuelle, entrez le texte de réponse dans la zone de recherche. Toutes les correspondances sont retournés. Sélectionnez la réponse que vous souhaitez utiliser comme le suivi et vérifiez **contexte seule**, puis sélectionnez **enregistrer**. 

    ![Rechercher le lien de l’invite de suivi à la boîte de dialogue de réponse pour une réponse existante, en utilisant le texte de la réponse.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    Une fois que vous avez ajouté l’invite de suivi, pensez à sélectionner **enregistrer et effectuer l’apprentissage**.
  
<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Vous invite à entrer le QnA jeu pour obtenir toutes les suites de test

Lorsque la question avec suivi de test vous invite à entrer dans le **Test** volet, sélectionnez **activer tour multi**, puis entrez votre question. La réponse inclut les invites de suivi.

![Lorsque vous testez la question dans le volet de Test, la réponse inclut les invites de suivi.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Requête JSON pour retourner des invites de suivi et de réponse initiale

Utiliser le vide `context` objet pour demander la réponse à la question de l’utilisateur et inclure des invites de suivi. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Réponse JSON à retourner des invites de suivi et de réponse initiale

La section précédente a demandé une réponse et les invites de suivi pour `Accounts and signing in`. La réponse inclut l’invite d’informations, situées dans `answers[0].context`, inclure le texte à afficher à l’utilisateur. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

Le `prompts` tableau fournit le texte dans le `displayText` propriété et la `qnaId` valeur donc vous pouvez afficher ces réponses en tant que les choix suivants affichés dans le flux des messages. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Requête JSON pour retourner la réponse non initiale et les invites de suivi

Remplir le `context` objet à inclure le contexte précédent.

Dans la requête JSON suivante, la question actuelle est `Use Windows Hello to sign in` et la question précédente a été `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Réponse JSON pour retourner la réponse non initiale et les invites de suivi

QnA Maker _GenerateAnswer_ réponse JSON inclut les invites de suivi dans le `context` propriété du premier élément dans le `answers` objet :

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Affichage des invites et l’envoi de contexte dans l’application cliente 

Si vous avez ajouté des invites dans votre base de connaissances et que vous avez testé le flux dans le volet de test, les invites ne démarre pas automatiquement s’affiche dans les applications clientes. Vous pouvez afficher les invites comme les actions suggérées ou des boutons dans le cadre de la réponse à la requête d’un utilisateur dans le client applications en incluant ce [exemple Bot Framework](https://aka.ms/qnamakermultiturnsample) dans votre code. L’application cliente doit stocker l’ID de QnA actuel et la requête de l’utilisateur et les transmettre dans le [objet de contexte de l’API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) pour la prochaine requête utilisateur.

## <a name="display-order-supported-in-api"></a>Ordre d’affichage pris en charge dans l’API

L’ordre d’affichage, retourné dans la réponse JSON est pris en charge pour la modification par l’API uniquement. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les conversations contextuelles à partir de la [exemple de boîte de dialogue](https://aka.ms/qnamakermultiturnsample) ou en savoir plus [bot conceptuel de conception pour activer plusieurs conversations](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrer une base de connaissances](../Tutorials/migrate-knowledge-base.md)
