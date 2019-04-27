---
title: Bot QnA - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: Créer un agent conversationnel QnA à partir de la page de publication pour une base de connaissances existante. Ce robot utilise le Kit de développement de Bot Framework v4. Vous n’avez pas besoin d’écrire du code pour générer le bot, tout le code est fourni pour vous.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599125"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>Didacticiel : Créez un robot QnA avec Azure Bot Service v4

Créer un agent conversationnel QnA à partir de la **publier** page pour une base de connaissances existante. Ce robot utilise le Kit de développement de Bot Framework v4. Vous n’avez pas besoin d’écrire du code pour générer le bot, tout le code est fourni pour vous.

**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un Service de robot Azure à partir d’une base de connaissances existante
> * Dialoguer avec le bot pour vérifier que le code fonctionne 

## <a name="prerequisites"></a>Conditions préalables

Vous devez disposer d’une base de connaissances publiée pour ce tutoriel. Si vous n’en avez pas, suivez les étapes de [créer et réponse à partir de la base de connaissances](create-publish-query-in-portal.md) didacticiel pour créer une base de connaissances QnA Maker avec questions et réponses.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>Créer un bot QnA

Créez un robot en tant qu’une application cliente pour la base de connaissances. 

1. Dans le portail QnA Maker, accédez à la **publier** page et publier votre base de connaissances. Sélectionnez **créer Bot**. 

    ![Dans le portail QnA Maker, accédez à la page Publish et publiez votre base de connaissances. Sélectionnez Create Bot.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Le portail Azure s’ouvre avec la configuration de la création de robot.

1.  Entrez les paramètres pour créer le robot :

    |Paramètre|Valeur|Objectif|
    |--|--|--|
    |Nom du robot|`my-tutorial-kb-bot`|C’est le nom de ressource Azure pour le bot.|
    |Abonnement|Consultez l’objectif.|Sélectionnez le même abonnement que vous avez utilisés pour créer les ressources de QnA Maker.|
    |Groupe de ressources|`my-tutorial-rg`|Le groupe de ressources utilisé pour toutes les ressources Azure liées de robot.|
    |Lieu|`west us`|Emplacement des ressources Azure du robot.|
    |Niveau tarifaire|`F0`|Le niveau gratuit pour le service de robot Azure.|
    |Nom de l’application|`my-tutorial-kb-bot-app`|Il s’agit d’une application web pour prendre en charge votre robot uniquement. Cela ne doit pas être le même nom d’application que votre service QnA Maker est déjà à l’aide de. Partage d’application web de QnA Maker avec toute autre ressource n’est pas pris en charge.|
    |Langue du Kit de développement logiciel|C#|Ceci est le langage de programmation sous-jacent utilisé par l’infrastructure de robot Kit de développement logiciel. Vos choix sont C# ou Node.js.|
    |Clé d’authentification QnA|**Ne modifiez pas**|Cette valeur est renseignée automatiquement.|
    |Plan App Service/Emplacement|**Ne modifiez pas**|Pour ce didacticiel, l’emplacement n’est pas important.|
    |Stockage Azure|**Ne modifiez pas**|Données de conversation sont stockées dans les tables de stockage Azure.|
    |Application Insights|**Ne modifiez pas**|La journalisation est envoyée à Application Insights.|
    |ID d’application Microsoft|**Ne modifiez pas**|Mot de passe et d’utilisateur active directory est requis.|

    ![Créer le robot de base de connaissances avec ces paramètres.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    Attendez quelques minutes jusqu'à ce que la notification de processus de création de robot signale une réussite.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>Discuter avec le bot

1. Dans le portail Azure, ouvrez la nouvelle ressource de robot à partir de la notification. 

    ![Dans le portail Azure, ouvrez la nouvelle ressource de robot à partir de la notification.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. À partir de **gestion de robot**, sélectionnez **Test dans Web Chat** et entrez : `How large can my KB be?`. Le robot doit répondre avec : 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![Tester le nouveau robot de base de connaissances.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Pour plus d’informations sur les robots de Azure, consultez [utilisez QnA Maker pour répondre aux questions](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>Liés à des robots de QnA Maker

* Le robot d’aide QnA Maker, utilisé dans le portail QnA Maker, est disponible en tant qu’un [exemple de robot](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot).
    ![Icône de bot d’aide QnA Maker est rouge robot](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [Soins de santé robots](https://docs.microsoft.com/HealthBot/qna_model_howto) utiliser QnA Maker comme l’une de leurs [modèles linguistiques](https://docs.microsoft.com/HealthBot/qna_model_howto).

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez terminé avec le bot de ce tutoriel, supprimez le bot dans le portail Azure. 

Si vous avez créé un nouveau groupe de ressources pour les ressources du robot, supprimez le groupe de ressources. 

Si vous n’avez pas créé un nouveau groupe de ressources, vous devez trouver les ressources associées au robot. Le moyen le plus simple consiste à rechercher par nom de l’application de robot et un robot. Les ressources de bot incluent :

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
