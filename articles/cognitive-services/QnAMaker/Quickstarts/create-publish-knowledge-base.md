---
title: 'Démarrage rapide : Créer, entraîner et publier une base de connaissances - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment créer une base de connaissances QnA Maker à partir de votre propre contenu, comme des Questions fréquentes (FAQ) ou des manuels de produits. La base de connaissances QnA Maker de cet exemple est créée à partir d’une simple page web de Questions fréquentes (FAQ) pour répondre à des questions sur la récupération de clé BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 26c7996d6a955a183121a59254131dcb1dc6706f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844260"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Démarrage rapide : Créer, entraîner et publier votre base de connaissances QnA Maker

Vous pouvez créer une base de connaissances QnA Maker à partir de votre propre contenu, tel que des Questions fréquentes (FAQ) ou des manuels. Cet article inclut un exemple de création d’une base de connaissances QnA Maker à partir d’une simple page web de Forum aux questions, pour répondre à des questions sur la récupération de clé BitLocker.

Incluez une personnalité d’échanges de conversation pour rendre votre base de connaissances plus attrayante aux yeux de vos utilisateurs.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Configuration requise

> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Créer une base de connaissances QnA Maker

1. Connectez-vous au portail [QnAMaker.ai](https://QnAMaker.ai) avec vos informations d’identification Azure.

1. Sur le portail QnA Maker, sélectionnez **Create a knowledge base**.

1. Dans la page **Create** (Créer), sélectionnez **Create a QnA service** (Créer un service QnA). Vous êtes dirigé vers le [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) pour configurer un service QnA Maker dans votre abonnement.

1. Sur le portail Azure, créez la ressource. Rappelez-vous de l’ID Azure Active Directory, de l’abonnement et du nom de ressource QnA que vous avez sélectionnés au moment de créer la ressource.
1. Revenez au portail QnA Maker, actualisez la page web sur le portail pour poursuivre la création de votre base de connaissances.

   ![Capture d’écran de la sélection d’une base de connaissances du service QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nommez votre base de connaissances **My Sample QnA KB** (Mon exemple de base de connaissances QnA).

1. Ajoutez un exemple de document Word sous la forme d’une URL :

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. Sélectionnez `+ Add URL`.

1. Ajoutez un **échange _professionnel_** à votre base de connaissances.

1. Sélectionnez **Create your KB** (Créer votre base de connaissances).

    Le processus d’extraction prend quelques minutes pour lire le document et identifier les questions et réponses.

    Une fois que QnA Maker a créé la base de connaissances, la page **Knowledge base** (Base de connaissances) s’ouvre. Vous pouvez modifier le contenu de la base de connaissances dans cette page.

## <a name="add-a-new-question-and-answer-set"></a>Ajouter une nouvelle série de questions et réponses

1. Sur le portail QnA Maker, dans la page **Edit** (Modifier), sélectionnez **+ Add QnA pair** (Ajouter une paire QnA) dans la barre d’outils contextuelle.
1. Ajoutez la question suivante :

    `How many Azure services are used by a knowledge base?`

1. Ajoutez la réponse mise en forme avec la syntaxe _Markdown_ :

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Ajoutez la question sous forme de texte et la réponse mise en forme avec la syntaxe Markdown.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Le symbole Markdown, `*`, est utilisé pour les éléments à puce. Le symbole `\n` est utilisée pour une nouvelle ligne.

    La page **Modifier** affiche la syntaxe Markdown. Quand vous utiliserez le panneau **Test** ultérieurement, la syntaxe Markdown s’affichera correctement.

## <a name="save-and-train"></a>Enregistrer et entraîner

Dans le coin supérieur droit, sélectionnez **Save and train** (Enregistrer et entraîner) pour enregistrer vos modifications et entraîner le modèle QnA Maker. Les modifications ne sont conservées que si elles sont enregistrées.

## <a name="test-the-knowledge-base"></a>Tester la base de connaissances

1. Dans le portail QnA Maker, en haut à droite, sélectionnez **Test** pour vérifier que les modifications apportées ont pris effet.
1. Entrez un exemple de requête d’utilisateur dans la zone de texte.

    `How many Azure services are used by a knowledge base?`

    ![ Entrez un exemple de requête d’utilisateur dans la zone de texte. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Sélectionnez **Inspect** pour examiner la réponse plus en détail. La fenêtre de test est utilisée pour tester les changements que vous apportez à la base de connaissances avant de la publier.

1. Sélectionnez à nouveau **Test** (Tester) pour fermer le panneau **Test**.

## <a name="publish-the-knowledge-base"></a>Publier la base de connaissances

Quand vous publiez une base de connaissances, son contenu passe de l’index `test` à un index `prod` dans la Recherche Azure.

![Capture d’écran du déplacement du contenu de votre base de connaissances](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Dans le portail QnA Maker, sélectionnez **Publish** (Publier). Ensuite, pour confirmer, sélectionnez **Publish** dans la page.

    Le service QnA Maker est désormais correctement publié. Vous pouvez utiliser le point de terminaison dans votre code d’application ou de bot.

    ![Capture d’écran de la réussite de la publication](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Créer un bot

Après la publication, vous pouvez créer un bot à partir de la page **Publish** (Publier) :

* Vous pouvez créer plusieurs bots rapidement, tous pointant vers la même base de connaissances pour différentes régions ou différents plans tarifaires pour les bots individuels.
* Si vous ne voulez qu’un seul bot pour la base de connaissances, utilisez le lien **Afficher tous vos bots sur le portail Azure** pour voir la liste de vos bots actuel.

Quand vous apportez des modifications à la base de connaissances et que vous republiez, aucune autre action n’est nécessaire avec le bot. Il est déjà configuré pour fonctionner avec la base de connaissances et il fonctionne avec toutes les modifications futures de la base de connaissances. Chaque fois que vous publiez une base de connaissances, tous les bots qui y sont connectés sont automatiquement mis à jour.

1. Dans le portail QnA Maker, dans la page **Publish**, sélectionnez **Create bot**. Ce bouton apparaît seulement une fois que vous avez publié la base de connaissances.

    ![Capture d’écran de la création d’un bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Un nouvel onglet de navigateur s’ouvre pour le portail Azure, avec la page de création d’Azure Bot Service. Configurez le service bot Azure. Le bot et QnA Maker peuvent partager le plan du service Web App, mais ils ne peuvent pas partager l’application web. Cela signifie que le **nom de l’application** pour le bot doit être différent du nom de l’application pour le service QnA Maker.

    * **À faire**
        * Changer le descripteur du bot, s’il n’est pas unique.
        * Sélectionner le langage du SDK. Une fois le bot créé, vous pouvez télécharger le code dans votre environnement de développement local et poursuivre le processus de développement.
    * **À ne pas faire**
        * Modifiez les paramètres suivants sur le portail Azure au moment de créer le bot. Ils sont préremplis pour votre base de connaissances existante :
           * Clé d’authentification QnA
           * Plan et emplacement App Service


1. Une fois le bot créé, ouvrez la ressource **Service Bot**.
1. Sous **Gestion du bot**, sélectionnez **Tester dans le Web Chat**.
1. À l’invite de chat **Tapez votre message**, entrez :

    `Azure services?`

    Le chatbot répond avec une réponse provenant de votre base de connaissances.

    ![Entrez une requête d’utilisateur dans le web chat de test.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Qu’avez-vous fait ?

Vous avez créé une base de connaissances, ajouté une URL publique à la base de connaissances, ajouté votre propre série de questions/réponses, formé, testé puis publié la base de connaissances.

Après avoir publié la base de connaissances, vous avez créé un bot et testé celui-ci.

Tout cela a été fait en quelques minutes sans avoir à écrire de code ni à supprimer le contenu.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nettoyez les ressources de framework QnA Maker et Bot dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations :

* [Format Markdown dans les réponses](../reference-markdown-format.md)
* [Sources de données](../concepts/knowledge-base.md) QnA Maker
* [Paramètres de configuration de ressources de bot](../tutorials/create-qna-bot.md)

> [!div class="nextstepaction"]
> [Ajouter des questions avec des métadonnées](add-question-metadata-portal.md)
