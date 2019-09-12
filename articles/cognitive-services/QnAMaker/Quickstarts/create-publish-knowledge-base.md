---
title: 'Démarrage rapide : Créer, entraîner et publier une base de connaissances - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Vous pouvez créer une base de connaissances QnA Maker à partir de votre propre contenu, tel que des Questions fréquentes (FAQ) ou des manuels. La base de connaissances QnA Maker de cet exemple est créée à partir d’une simple page web de Questions fréquentes (FAQ) pour répondre à des questions sur la récupération de clé BitLocker.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 229956b41f7f26637e6a6aa62e341c4b06b8a429
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376362"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Démarrage rapide : Créer, entraîner et publier votre base de connaissances QnA Maker

Vous pouvez créer une base de connaissances QnA Maker à partir de votre propre contenu, tel que des Questions fréquentes (FAQ) ou des manuels. Cet article inclut un exemple de création d’une base de connaissances QnA Maker à partir d’une simple page web de Forum aux questions, pour répondre à des questions sur la récupération de clé BitLocker.

## <a name="prerequisite"></a>Configuration requise

> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-qna-maker-knowledge-base"></a>Créer une base de connaissances QnA Maker

1. Connectez-vous au portail [QnAMaker.ai](https://QnAMaker.ai) avec vos informations d’identification Azure.

1. Sur le portail QnA Maker, sélectionnez **Create a knowledge base**.

   ![Capture d’écran du portail QnA Maker](../media/qna-maker-create-kb.png)

1. Dans la page **Create** (Créer), à l’étape 1, sélectionnez **Create a QnA service** (Créer un service QnA). Vous êtes dirigé vers le [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) pour configurer un service QnA Maker dans votre abonnement. Si le délai d’attente du portail Azure est atteint, sélectionnez **Réessayer** sur le site. Une fois connecté, votre tableau de bord Azure apparaît.

1. Après avoir créé un service QnA Maker dans Azure, revenez à qnamaker.ai/create. Sélectionnez votre service QnA Maker dans les listes déroulantes à l’étape 2. Si vous avez créé un service QnA Maker, n’oubliez pas d’actualiser la page.

   ![Capture d’écran de la sélection d’une base de connaissances du service QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. À l’étape 3, nommez votre base de connaissances **My Sample QnA KB**.

1. Pour ajouter du contenu à votre base de connaissances, sélectionnez trois types de sources de données. À l’étape 4, sous **Populate your KB** (Remplissez votre base de connaissances), ajoutez l’URL de [FAQ sur la récupération BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) dans la zone **URL**.

   ![Capture d’écran de l’ajout de sources de données](../media/qnamaker-quickstart-kb/add-datasources.png)

1. À l’étape 5, sélectionnez **Create your KB** (Créer votre base de connaissances).

1. Pendant que QnA Maker crée la base de connaissances, une fenêtre contextuelle s’affiche. Le processus d’extraction prend quelques minutes pour lire la page HTML et identifier les questions et réponses.

1. Une fois que QnA Maker a créé la base de connaissances, la page **Knowledge base** (Base de connaissances) s’ouvre. Vous pouvez modifier le contenu de la base de connaissances dans cette page.

## <a name="edit-the-knowledge-base"></a>Modifier la base de connaissances

1. Dans le portail QnA Maker, dans la section **Edit** (Modifier), sélectionnez **Add QnA pair** (Ajouter une paire QnA) pour ajouter une nouvelle ligne à la base de connaissances. Sous **Question**, entrez **Salut.** Sous **Answer**, entrez **Bonjour. Posez-moi des questions sur BitLocker.**

    ![Capture d’écran du portail QnA Maker](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Dans le coin supérieur droit, sélectionnez **Save and train** (Enregistrer et entraîner) pour enregistrer vos modifications et entraîner le modèle QnA Maker. Les modifications ne sont conservées que si elles sont enregistrées.

## <a name="test-the-knowledge-base"></a>Tester la base de connaissances

1. Dans le portail QnA Maker, en haut à droite, sélectionnez **Test** pour vérifier que les modifications apportées ont pris effet. Tapez `hi there` dans la zone, puis sélectionnez Entrée. Vous devriez voir la réponse que vous avez créée.

1. Sélectionnez **Inspect** pour examiner la réponse plus en détail. La fenêtre de test est utilisée pour tester vos modifications de la base de connaissances avant leur publication.

    ![Capture d’écran du volet de test](../media/qnamaker-quickstart-kb/inspect.png)

1. Sélectionnez **Test** à nouveau pour fermer la fenêtre contextuelle **Test**.

## <a name="publish-the-knowledge-base"></a>Publier la base de connaissances

Quand vous publiez une base de connaissances, ses contenus de questions/réponses se déplacent de l’index de test vers un index de production dans Recherche Azure.

![Capture d’écran du déplacement du contenu de votre base de connaissances](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Dans le portail QnA Maker, dans le menu à côté de **Edit**, sélectionnez **Publish**. Ensuite, pour confirmer, sélectionnez **Publish** dans la page.

1. Le service QnA Maker est désormais correctement publié. Vous pouvez utiliser le point de terminaison dans votre code d’application ou de bot.

    ![Capture d’écran de la réussite de la publication](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Créer un bot

Après la publication, vous pouvez créer un bot à partir de la page **Publish** (Publier) : 

* Vous pouvez créer plusieurs bots rapidement, tous pointant vers la même base de connaissances pour différentes régions ou différents plans tarifaires pour les bots individuels. 
* Si vous ne voulez qu’un seul bot pour la base de connaissances, utilisez le lien **Afficher tous vos bots sur le portail Azure** pour voir la liste de vos bots actuel. 

Quand vous apportez des modifications à la base de connaissances et que vous republiez, aucune autre action n’est nécessaire avec le bot. Il est déjà configuré pour fonctionner avec la base de connaissances et il fonctionne avec toutes les modifications futures de la base de connaissances. Chaque fois que vous publiez une base de connaissances, tous les bots qui y sont connectés sont automatiquement mis à jour.

1. Dans le portail QnA Maker, dans la page **Publish**, sélectionnez **Create bot**. Ce bouton apparaît seulement une fois que vous avez publié la base de connaissances.

    ![Capture d’écran de la création d’un bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Un nouvel onglet de navigateur s’ouvre pour le portail Azure, avec la page de création d’Azure Bot Service. Configurez le service bot Azure. Pour plus d’informations sur ces paramètres de configuration, consultez [Créer un bot QnA avec Azure Bot Service v4](../tutorials/create-qna-bot.md).
    
    * Ne modifiez pas les paramètres suivants dans le portail Azure lors de la création du bot. Ils sont préremplis pour votre base de connaissances existante : 
        * Clé d’authentification QnA
        * Plan et emplacement App Service
        * Stockage Azure
    * Le bot et QnA Maker peuvent partager le plan du service Web App, mais ils ne peuvent pas partager l’application web. Cela signifie que le **nom de l’application** doit être différent du nom de l’application que vous avez utilisé quand vous avez créé le service QnA Maker. 


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une base de connaissances](../How-To/create-knowledge-base.md)
