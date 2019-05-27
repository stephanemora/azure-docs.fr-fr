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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 0aab2250d6692d7db99c55b23604c08f5fe619a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235660"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Suivi de l’utilisation vous invite à créer plusieurs tours d’une conversation

Permet de gérer l’activer plusieurs, connu sous le nom des invites de suivi et le contexte _tour multi_, pour votre robot d’une question à un autre.

Regardez la vidéo suivante de la démonstration pour voir comment cela fonctionne.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Qu’est une conversation tour multi ?

Certaines questions ne peuvent pas être traitées dans un seul coup. Lorsque vous concevez vos conversations de (conversationnel) d’application client, un utilisateur peut poser une question qui doit être ou non filtrées affinées afin de déterminer la réponse correcte. Ce flux via les questions est possible à l’aide de l’utilisateur avec **vous invite à entrer un suivi**.

Lorsque l’utilisateur demande la question, QnA Maker renvoie la réponse _et_ les invites de suivi. Cela vous permet de présenter les questions de suivi en tant que choix. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversation multi-d’activer exemple avec conversationnel

Un agent conversationnel gère la conversation avec l’utilisateur, question en question, pour déterminer la réponse finale.

![Au sein du flux conversationnel, gérer l’état de la conversation dans un système de la boîte de dialogue Activer multi en fournissant des invites dans les réponses présentés sous la forme d’options pour poursuivre la conversation.](../media/conversational-context/conversation-in-bot.png)

Dans l’image précédente, l’utilisateur a entré `My account`. La base de connaissances a 3 paires QnA liés. L’utilisateur doit sélectionner l’une des trois options pour affiner la réponse. Dans la base de connaissances, la question (1), a trois invites suivis, présentées dans l’agent conversationnel sous forme de trois choix (#2). 

Lorsque l’utilisateur sélectionne un choix (3), la liste suivante de raffinage choix (n° 4) est présentée. Cela peut se poursuivre (5) jusqu'à ce que la réponse correcte et finale (6) est déterminée.

L’image précédente a **activer tour multi** sélectionnée pour affiche des invites. 

### <a name="using-multi-turn-in-a-bot"></a>À l’aide de la tour multiples dans un robot

Vous devez modifier votre application cliente pour gérer la conversation contextuelle. Vous devez ajouter [code à votre robot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) pour voir les invites.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Créer une conversation multi tour à partir de la structure d’un document

Lorsque vous créez une base de connaissances, vous verrez une case à cocher facultative pour activer l’extraction Activez multi. 

![Lorsque vous créez une base de connaissances, vous verrez une case à cocher facultative pour activer l’extraction Activez multi.](../media/conversational-context/enable-multi-turn.png)

Si vous sélectionnez cette option, lorsque vous importez un document, la conversation activer multi peut être implicite à partir de la structure. Si cette structure existe, QnA Maker crée les paires de QnA suivis invite pour vous. 

Structure de désactiver multi peut uniquement être déduit à partir d’URL, PDF, DOCX fichiers. 

L’image suivante d’un Microsoft Surface [fichier PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) est destiné à être utilisé comme un manuel. En raison de la taille de ce fichier PDF, la ressource Azure QnA Maker nécessite la recherche de niveau tarifaire du B (15 index) ou supérieur. 

![! [Si vous importez un document, contextuelle conversation peut être déduite à partir de la structure. Si cette structure existe, QnA Maker crée les paires de QnA invite suivis, dans le cadre de l’importation du document.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Lorsque vous importez le document PDF, QnA Maker détermine les invites de suivi à partir de la structure pour créer des flux de conversation. 

1. Dans **étape 1**, sélectionnez **créer une base de connaissances** à partir de la barre de navigation supérieure.
1. Dans **étape 2**, créez ou utilisez un service QnA existant. Veillez à utiliser un service QnA avec un service de recherche de B (15 index) ou une version ultérieure, car le fichier PDF de manuel Surface est trop grand pour un niveau plus faible.
1. Dans **étape 3**, entrez un nom pour votre base de connaissances, tel que `Surface manual`.
1. Dans **étape 4**, sélectionnez **activer extraction multi tour à partir des URL, des fichiers .pdf ou .docx.** Sélectionnez l’URL du manuel de Surface

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Sélectionnez le **créer votre base de connaissances** bouton. 

    Une fois la base de connaissances est créé, une vue des paires questions et réponses s’affiche.

## <a name="show-questions-and-answers-with-context"></a>Afficher les questions et réponses avec contexte

Réduire les paires questions et réponses affichées pour seulement ceux dotés de conversations contextuelles. 

1. Sélectionnez **afficher les options**, puis sélectionnez **Show contexte (version préliminaire)** . La liste affiche les paires questions et réponses contenant des invites de suivi. 

    ![Filtrer les question et répondre des paires par conversations contextuelles](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Le contexte de la tour multi s’affiche dans la première colonne.

    ![! [Lorsque vous importez le document PDF, QnA Maker détermine des invites de suivi à partir de la structure pour créer des flux de conversation. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Dans l’image précédente, #1 indique un texte en gras dans la colonne, ce qui signifie la question actuelle. La question parent est le premier élément dans la ligne. Des questions ci-dessous sont les paires questions et réponses liés. Ces éléments peuvent être sélectionnés, donc vous pouvez passer immédiatement à d’autres éléments de contexte. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Ajouter une paire de QnA existant en tant que l’invite de suivi

La question initiale de `My account` a suivi invites comme `Accounts and signing in`. 

![La question « Mon compte » d’origine retourne correctement les « Comptes et la signature » répondre et possède déjà les invites suivis liés.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Ajouter une invite de suivi à une paire de QnA existante qui n’est pas actuellement liée. Étant donné que la question n’est pas liée à n’importe quelle paire QnA, le paramètre actuel de la vue doit changer.

1. Pour lier une paire de QnA existante comme une invite de suivi, sélectionnez la ligne de la paire questions et réponses. Pour la surface d’exposition manuelle, recherchez `Sign out` pour réduire la liste.
1. Dans la ligne de `Signout`, sélectionnez **invite suivi ajouter** à partir de la **réponse** colonne.
1. Dans le **invite un suivi (version préliminaire)** fenêtre contextuelle, entrez les informations suivantes :

    |Champ|Valeur|
    |--|--|
    |Texte affiché|`Turn off the device`. Il s’agit de texte personnalisé que vous choisissez d’afficher dans l’invite de suivi.|
    |Contexte uniquement|Sélectionné. Cette réponse sera retournée uniquement si la question Spécifie le contexte.|
    |Lien vers la réponse|Entrez `Use the sign-in screen` pour rechercher la paire de QnA existante.|


1.  Une correspondance est retournée. Sélectionnez cette réponse en tant que le suivi, puis sélectionnez **enregistrer**. 

    ![Rechercher le lien de l’invite de suivi à la boîte de dialogue de réponse pour une réponse existante, en utilisant le texte de la réponse.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Une fois que vous avez ajouté l’invite de suivi, pensez à sélectionner **enregistrer et effectuer l’apprentissage** dans le volet de navigation supérieur.
  
### <a name="edit-the-display-text"></a>Modifier le texte d’affichage 

Quand une invite de suivi est créée, et une paire de QnA existante est sélectionnée comme le **lien vers la réponse**, vous pouvez entrer de nouveau **afficher du texte**. Ce texte ne remplace pas la question existante, et elle n’ajoute pas d’une autre nouvelle question. Elle est séparée de ces valeurs. 

1. Pour modifier le texte affiché, recherchez et sélectionnez la question dans le **contexte** champ.
1. Sur la ligne de cette question, sélectionnez l’invite de suivi dans la colonne de la réponse. 
1. Sélectionnez le texte d’affichage que vous souhaitez modifier, puis sélectionnez **modifier**.

    ![Sélectionnez le texte d’affichage que vous souhaitez modifier, puis sélectionnez Modifier.](../media/conversational-context/edit-existing-display-text.png)

1. Le **invite de suivi** fenêtre contextuelle vous permet de modifier le texte existant. 
1. Lorsque vous avez terminé modifiant le texte d’affichage, sélectionnez **enregistrer**. 
1. Pensez à sélectionner **enregistrer et effectuer l’apprentissage** dans le volet de navigation supérieur.


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

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Ajouter la nouvelle paire de QnA comme invite suivi

Ajouter une nouvelle paire de QnA à la base de connaissances. La paire de QnA doit être liée à une question existante comme une invite de suivi.

1. À partir de la barre d’outils de la base de connaissances, recherchez et sélectionnez la paire de QnA existante pour `Accounts and Signing In`. 

1. Dans le **réponse** colonne pour cette question, sélectionnez **invite suivi ajouter**. 
1. Le **invite un suivi (version préliminaire)** , créer une nouvelle invite de suivi en entrant les valeurs suivantes : 

    |Champ de texte|Valeur|
    |--|--|
    |**Texte affiché**|`Create a Windows Account`. Il s’agit de texte personnalisé que vous choisissez d’afficher dans l’invite de suivi.|
    |**Context-only**|Sélectionné. Cette réponse sera retournée uniquement si la question Spécifie le contexte.|
    |**Lien pour répondre à**|Entrez le texte suivant en tant que la réponse :<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Lorsque vous enregistrez et Formez la base de données, ce texte est converti en |
    |||

    ![Créer nouveau QnA invite](../media/conversational-context/create-child-prompt-from-parent.png)


1. Sélectionnez **créer** puis sélectionnez **enregistrer**. 

    Cela créé une nouvelle paire de questions-réponses et lié la question sélectionnée en tant qu’une invite de suivi. Le **contexte** colonne, pour ces deux questions, indique une relation de message suivie. 

1. Modifier le **afficher les options** à [afficher le contexte](#show-questions-and-answers-with-context).

    La nouvelle question montre comment il est lié.

    ![Créer une nouvelle invite de suivi ](../media/conversational-context/new-qna-follow-up-prompt.png)

    La question parent montre la nouvelle question comme l’un de ses choix.

    ![! [La colonne de contexte, pour ces deux questions, indique une relation de message suivie.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Une fois que vous avez ajouté l’invite de suivi, pensez à sélectionner **enregistrer et effectuer l’apprentissage** dans le volet de navigation supérieur.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Activer multi tour lorsque vous demande si un suivi de test

Lorsque la question avec suivi de test vous invite à entrer dans le **Test** volet, sélectionnez **activer tour multi**, puis entrez votre question. La réponse inclut les invites de suivi.

![Lorsque vous testez la question dans le volet de Test, la réponse inclut les invites de suivi.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Si vous n’activez pas multi tour, la réponse est retournée mais invites suivis ne sont pas retournés.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Requête JSON pour retourner des invites de suivi et de réponse initiale

Utiliser le vide `context` objet pour demander la réponse à la question de l’utilisateur et inclure des invites de suivi. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

Le `prompts` tableau fournit le texte dans le `displayText` propriété et le `qnaId` flux de valeur, donc vous pouvez afficher ces réponses en tant que les choix suivants affichés dans la conversation, puis envoyer la valeur sélectionnée pour QnA Maker dans la requête suivante. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Requête JSON pour retourner la réponse non initiale et les invites de suivi

Remplir le `context` objet à inclure le contexte précédent.

Dans la requête JSON suivante, la question actuelle est `Use Windows Hello to sign in` et la question précédente a été `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Interroger la base de connaissances avec l’ID de QnA

Dans la réponse de la question initiale qui lui sont associés et les invites de suivi `qnaId` est retourné. Maintenant que vous avez l’ID, vous pouvez le passer à de de l’invite suivi corps de requête. Si le corps de demande contient le `qnaId`et l’objet de contexte (qui contient les propriétés de QnA précédentes), puis GenerateAnswer retournera la question exacte par ID, au lieu d’utiliser l’algorithme de classement pour trouver une réponse par le texte de la question. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Affichage des invites et l’envoi de contexte dans l’application cliente 

Vous avez ajouté des invites dans votre base de connaissances et testé le flux dans le volet de test. Maintenant, vous devez utiliser ces invites dans l’application cliente. Pour Bot Framework, les invites ne démarreront pas automatiquement s’affiche dans les applications clientes. Vous pouvez afficher les invites comme les actions suggérées ou des boutons dans le cadre de la réponse à la requête d’un utilisateur dans le client applications en incluant ce [exemple Bot Framework](https://aka.ms/qnamakermultiturnsample) dans votre code. L’application cliente doit stocker l’ID de QnA actuel et la requête de l’utilisateur et les transmettre dans le [objet de contexte de l’API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) pour la prochaine requête utilisateur. 

## <a name="display-order-supported-in-api"></a>Ordre d’affichage pris en charge dans l’API

Le [afficher du texte et l’ordre d’affichage](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), retournée dans la réponse JSON, est pris en charge pour la modification par le [API de mise à jour](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

FIX - devez accéder au parent, alors que les colonnes de la réponse, puis modifier la réponse. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les conversations contextuelles à partir de la [exemple de boîte de dialogue](https://aka.ms/qnamakermultiturnsample) ou en savoir plus [bot conceptuel de conception pour activer plusieurs conversations](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrer une base de connaissances](../Tutorials/migrate-knowledge-base.md)
