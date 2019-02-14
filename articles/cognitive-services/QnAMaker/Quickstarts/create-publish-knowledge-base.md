---
title: Créer, entraîner et publier avec une base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: Vous pouvez créer une base de connaissances QnA Maker à partir de votre propre contenu, tel que des Questions fréquentes (FAQ) ou des manuels. La base de connaissances QnA Maker dans cet exemple est créée à partir d’une simple page web de FAQ afin de répondre aux questions sur la récupération de clé BitLocker.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 2ac6e6fcd73abddcee668b8f73184b923aeab5d3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876995"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Créer, entraîner et publier votre base de connaissances QnA Maker

Vous pouvez créer une base de connaissances QnA Maker à partir de votre propre contenu, tel que des Questions fréquentes (FAQ) ou des manuels. La base de connaissances QnA Maker dans cet exemple est créée à partir d’une simple page web de FAQ afin de répondre aux questions sur la récupération de clé BitLocker.

## <a name="prerequisite"></a>Configuration requise

> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-qna-maker-knowledge-base"></a>Créer une base de connaissances QnA Maker

1. Connectez-vous à QnAMaker.ai avec vos informations d’identification Azure.

2. Sur le site web QnA Maker, sélectionnez **Create a knowledge base** (Créer une base de connaissances).

   ![Créer une base de connaissances](../media/qna-maker-create-kb.png)

3. Dans la page **Create** (Créer), à l’étape 1, sélectionnez **Create a QnA service** (Créer un service QnA). Vous êtes dirigé vers le [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) pour configurer un service QnA Maker dans votre abonnement. Si le délai d’attente du portail Azure est atteint, sélectionnez **Réessayer** sur le site. Une fois connecté, votre tableau de bord Azure apparaît.

4. Après avoir créé un service QnA Maker dans Azure, revenez à qnamaker.ai/create. Sélectionnez votre service QnA dans les listes déroulantes à l’étape 2. Si vous avez créé un service QnA, n’oubliez pas d’actualiser la page.

   ![Sélectionner une base de connaissances de service QnA](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. À l’étape 3, nommez votre base de connaissances **Mon exemple de base de connaissances QnA**.

6. Pour ajouter du contenu à votre base de connaissances, sélectionnez trois types de sources de données. À l’étape 4, sous **Populate your KB** (Remplissez votre base de connaissances), ajoutez l’URL de [FAQ sur la récupération BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) dans la zone **URL**.

   ![Sélectionner une base de connaissances de service QnA](../media/qnamaker-quickstart-kb/add-datasources.png)

7. À l’étape 5, sélectionnez **Create your KB** (Créer votre base de connaissances).

8. Pendant la création de la base de connaissances, une fenêtre contextuelle s’affiche. Le processus d’extraction prend quelques minutes pour lire la page HTML et identifier les questions et réponses.

9. Une fois la base de connaissances créée, la page **Knowledge base** (Base de connaissances) s’ouvre. Vous pouvez modifier le contenu de la base de connaissances dans cette page.

10. Dans le coin supérieur droit, sélectionnez **Add QnA pair** (Ajouter une paire QnA) pour ajouter une nouvelle ligne dans la section **Editorial** de la base de connaissances. Sous **Question**, entrez **Salut.** Sous **Answer**, entrez **Bonjour. Posez-moi des questions sur bitlocker.**

   ![Ajouter une paire QnA](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. Dans le coin supérieur droit, sélectionnez **Save and train** (Enregistrer et entraîner) pour enregistrer vos modifications et entraîner le modèle QnA Maker. Les modifications ne sont conservées que si elles sont enregistrées.

12. Dans le coin supérieur droit, sélectionnez **Test** pour vérifier que les modifications apportées ont pris effet. Tapez `hi there` dans la zone, puis sélectionnez Entrée. Vous devriez voir la réponse que vous avez créée.

13. Sélectionnez **Inspect** pour examiner la réponse plus en détail. La fenêtre de test sert à tester vos modifications de la base de connaissances avant leur publication.

   ![Panneau de test](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Sélectionnez **Test** à nouveau pour fermer la fenêtre contextuelle **Test**.

15. Dans le menu en regard de **Edit** (Modifier), sélectionnez **Publish** (Publier). Ensuite, pour confirmer, sélectionnez **Publish** dans la page.

16. Le service QnA Maker est désormais correctement publié. Vous pouvez utiliser le point de terminaison dans votre code d’application ou de bot.

   ![Publish](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une base de connaissances](../How-To/create-knowledge-base.md)
