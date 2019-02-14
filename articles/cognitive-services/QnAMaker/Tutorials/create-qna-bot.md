---
title: Bot QnA - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: Ce tutoriel vous guide tout au long de la création d’un bot QnA avec Azure Bot Service v3 sur le portail Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker`
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 930d2d7279f23974e937fd0d858891d0bcaa1291
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869158"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>Tutoriel : Créer un bot QnA avec Azure Bot Service v3

Ce tutoriel vous guide tout au long de la création d’un bot QnA avec Azure Bot Service v3 sur le [portail Azure](https://portal.azure.com) sans écrire de code. La connexion d’une base de connaissances publiée (KB) à un bot est aussi simple que la modification des paramètres d’une application de bot. 

> [!Note] 
> Cette rubrique s’applique à la version 3 du Kit de développement logiciel (SDK) Bot. Vous trouverez la version 4 [ici](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs). 

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un Azure Bot Service avec le modèle QnA Maker
> * Dialoguer avec le bot pour vérifier que le code fonctionne 
> * Connecter votre base de connaissances publiée au bot
> * Tester le bot en posant une question

Pour cet article, vous pouvez utiliser le [service](../how-to/set-up-qnamaker-service-azure.md) gratuit QnA Maker.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’une base de connaissances publiée pour ce tutoriel. Si ce n’est pas le cas, suivez les étapes de [Créer une base de connaissances](../How-To/create-knowledge-base.md) pour créer un service QnA Maker avec des questions et des réponses.

## <a name="create-a-qna-bot"></a>Créer un bot QnA

1. Dans le portail Azure, sélectionnez **Créer une ressource**.

    ![création de service bot](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. Dans la zone de recherche, recherchez **Web App Bot**.

    ![sélection de service bot](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. Dans **Service Bot**, fournissez les informations requises :

    - Dans **Nom de l’application**, entrez le nom de votre bot. Le nom est utilisé comme sous-domaine lors du déploiement de votre bot sur le cloud (par exemple, mynotesbot.azurewebsites.net).
    - Sélectionnez l’abonnement, le groupe de ressources, le plan App Service et l’emplacement.

4. Pour utiliser les modèles v3, sélectionnez la version de SDK **SDK v3** la langage de SDK **C#** ou **Node.js**.

    ![paramètres de SDK de bot](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. Sélectionnez le modèle **Question et réponse** pour le champ de modèle Bot, puis enregistrez les paramètres de modèle en cliquant sur **Sélectionner**.

    ![enregistrer la sélection de modèle du service bot](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. Passez en revue vos paramètres, puis sélectionnez **Créer**. Le service de bot est alors créé et déployé sur Azure.

    ![créer un bot](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Vérifiez que le service bot a été déployé.

    - Sélectionnez **Notifications** (l’icône représentant une cloche qui se trouve le long du bord supérieur du portail Azure). La notification passera de **Le déploiement a commencé** à **Déploiement réussi**.
    - Lorsque la notification devient **Déploiement réussi**, sélectionnez **Accéder à la ressource** sur cette notification.

## <a name="chat-with-the-bot"></a>Discuter avec le bot

En sélectionnant **Accéder à la ressource**, vous accéderez aux ressources du bot.

Sélectionnez **Tester dans la Discussion Web** pour ouvrir le volet Discussion Web. Tapez « hi » dans Discussion Web.

![Chat web de bot QnA](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

Le bot répond « Please set QnAKnowledgebaseId and QnASubscriptionKey in App Settings. Cette réponse confirme que votre bot QnA a reçu le message, mais qu’aucune base de connaissances QnA Maker n’y est associée pour le moment. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>Connecter votre base de connaissances QnA Maker au bot

1. Ouvrez les **paramètres d’application** et modifiez les champs **QnAKnowledgebaseId**, **QnAAuthKey** et **QnAEndpointHostName** afin qu’ils contiennent les valeurs de votre base de connaissances QnA Maker.

    ![paramètres d'application](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. Obtenez votre ID de la base de connaissances, l’URL de l’hôte et la clé de point de terminaison à partir de l’onglet Paramètres de votre base de connaissances dans le portail QnA Maker.

    - Connectez-vous à [QnA Maker](https://qnamaker.ai)
    - Accédez à votre base de connaissances
    - Sélectionnez l’onglet **Paramètres**
    - **Publiez** votre base de connaissances, si ce n’est pas déjà fait

    ![Valeurs QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

## <a name="test-the-bot"></a>Tester le bot

Dans le portail Azure, sélectionnez **Test dans le chat web** pour tester le bot. 

![Bot QnA Maker](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

Votre bot QnA répond à partir de votre base de connaissances.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez terminé avec le bot de ce tutoriel, supprimez le bot dans le portail Azure. Les services de bot incluent :

* Le plan App Service
* Le service Recherche
* Le service Cognitive
* Le service Plan
* Ils peuvent également inclure le service Application Insights ainsi que le stockage des données Application Insights

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concept : base de connaissances](../concepts/knowledge-base.md)

## <a name="see-also"></a>Voir aussi

- [Gérer votre base de connaissances](https://qnamaker.ai)
- [Activer votre bot dans différents canaux](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
