---
title: Bot QnA avec Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 74c7bc5c601cd36a8dd2454506745406bc00dac0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031286"
---
# <a name="create-a-qna-bot-with-azure-bot-service-v3"></a>Créer un bot QnA avec Azure Bot Service v3
Ce tutoriel vous guide tout au long de la création d’un bot QnA avec Azure Bot Service v3 sur le portail Azure.

## <a name="prerequisite"></a>Configuration requise
Avant la création, suivez les étapes de [Créer une base de connaissances](../How-To/create-knowledge-base.md) pour créer un service QnA Maker avec des questions et des réponses.

Le bot répond aux questions à partir de la base de connaissances que vous avez créée, via QnAMakerDialog.

## <a name="create-a-qna-bot"></a>Créer un bot QnA
1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer** une nouvelle ressource dans le panneau de menu, puis sélectionnez **See all** (Tout afficher).

    ![création de service bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Dans la zone de recherche, recherchez **Web App Bot**.

    ![sélection de service bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Dans le panneau **Bot Service**, indiquez les informations requises.

    - Dans **Nom de l’application**, entrez le nom de votre bot. Le nom est utilisé comme sous-domaine lors du déploiement de votre bot sur le cloud (par exemple, mynotesbot.azurewebsites.net).
    - Sélectionnez l’abonnement, le groupe de ressources, le plan App Service et l’emplacement.

4. Pour obtenir des instructions de création d’un bot QnA avec le SDK v4, consultez [Modèle de bot QnA v4](https://aka.ms/qna-bot-v4). Pour utiliser les modèles v3, sélectionnez la version de SDK **SDK v3** la langage de SDK **C#** ou **Node.js**.

    ![paramètres de SDK de bot](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Sélectionnez le modèle **Question et réponse** pour le champ de modèle Bot, puis enregistrez les paramètres de modèle en cliquant sur **Sélectionner**.

    ![sélection de service bot](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Passez en revue vos paramètres, puis sélectionnez **Créer**. Cela crée et déploie le service bot avec QnAMakerDialog vers Azure.

    ![sélection de service bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Vérifiez que le service bot a été déployé.

    - Sélectionnez **Notifications** (l’icône représentant une cloche qui se trouve le long du bord supérieur du portail Azure). La notification passera de **Le déploiement a commencé** à **Déploiement réussi**.
    - Lorsque la notification devient **Déploiement réussi**, sélectionnez **Accéder à la ressource** sur cette notification.

## <a name="chat-with-the-bot"></a>Discuter avec le bot
En sélectionnant **Accéder à la ressource**, vous accéderez au panneau des ressources du bot.

Une fois le bot enregistré, cliquez sur **Test dans le chat web** pour ouvrir le volet de chat web. Tapez « hello » dans le chat web.

![Chat web de bot QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Le bot répond « Please set QnAKnowledgebaseId and QnASubscriptionKey in App Settings. Learn how to get them at https://aka.ms/qnaabssetup ». Cette réponse confirme que votre bot QnA a reçu le message, mais qu’aucune base de connaissances QnA Maker n’y est associée pour le moment. Vous le ferez à l’étape suivante.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Connecter votre base de connaissances QnA Maker au bot

1. Ouvrez les **paramètres d’application** et modifiez les champs **QnAKnowledgebaseId**, **QnAAuthKey** et **QnAEndpointHostName** afin qu’ils contiennent les valeurs de votre base de connaissances QnA Maker.

    ![paramètres d'application](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. Obtenez votre ID de la base de connaissances, l’URL de l’hôte et la clé de point de terminaison à partir de l’onglet Paramètres de votre base de connaissances dans https://qnamaker.ai.
    - Connectez-vous à [QnA Maker](https://qnamaker.ai)
    - Accédez à votre base de connaissances
    - Cliquez sur l’onglet **Paramètres**
    - **Publiez** votre base de connaissances, si ce n’est pas déjà fait

    ![Valeurs QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> Si vous souhaitez connecter la préversion de la base de connaissances au bot QnA, définissez la valeur de **Ocp-Apim-Subscription-Key** sur **QnAAuthKey**. Laissez le champ **QnAEndpointHostName** vide.

## <a name="test-the-bot"></a>Tester le bot
Dans le portail Azure, cliquez sur **Test dans le chat web** pour tester le bot. 

![Bot QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Votre bot QnA répond maintenant à partir de votre base de connaissances.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Intégrer QnA Maker et LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>Voir aussi

- [Gérer votre base de connaissances](https://qnamaker.ai)
- [Activer votre bot dans différents canaux](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
