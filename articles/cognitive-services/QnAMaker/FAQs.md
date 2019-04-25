---
title: FAQ - QnA Maker
titleSuffix: Azure Cognitive Services
description: La liste des questions plus fréquemment posées concernant le service QnA Maker vous aidera à adopter le service plus rapidement et de meilleurs résultats.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8104497a1808aa4e92d62f45e37525dba2f47742
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60233859"
---
# <a name="frequently-asked-questions-for-qna-maker"></a>Questions fréquentes (FAQ) pour QnA Maker

La liste des questions plus fréquemment posées concernant le service QnA Maker vous aidera à adopter le service plus rapidement et de meilleurs résultats.

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Utiliser le robot d’aide dans le portail QnA Maker

QnA Maker fournit un **aide** bot au sein du portail QnA Maker pour vous aider. Le robot d’aide est disponible sur chaque page web. Le bot utilise QnA Maker pour fournir des réponses et fournit le [ C# projet de code Bot Framework](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot) pour le robot afin de pouvoir obtenir rapidement opérationnel avec votre propre robot de réponse. 

![! [QnA Maker fournit un ** bot aide ** dans le portail QnA Maker pour vous aider à.] (. / media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Gestion de la base de connaissances

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>J’ai accidentellement supprimé une partie de mon QnA Maker, que dois-je faire ? 

Toutes les suppressions sont définitives et s’appliquent notamment aux paires questions et réponses, fichiers, URL, questions et réponses personnalisées, bases de connaissances ou ressources Azure. Assurez-vous d’exporter votre base de connaissances à partir de la page **Paramètres** avant de supprimer la moindre de ses parties. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Pourquoi mes URL/mes fichiers n’extraient-ils pas les paires de question-réponse ?

Il est possible que QnA Maker ne puisse pas extraire automatiquement du contenu question-réponse (QnA) à partir des URL de FAQ valides. Dans ce cas, vous pouvez coller le contenu QnA dans un fichier .txt et voir si l’outil peut l’ingérer. Vous pouvez également ajouter manuellement du contenu à votre base de connaissances par le biais du [portail QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Quelle taille maximale peut avoir une base de connaissances ?

La taille de la base de connaissances dépend de la référence SKU de Recherche Azure que vous choisissez lors de la création du service QnA Maker. Lisez plus d’informations [ici](./Tutorials/choosing-capacity-qnamaker-deployment.md).

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Pourquoi rien ne s’affiche dans la liste déroulante quand j’essaie de créer une base de connaissances ?

Vous n’avez encore jamais créé de services QnA Maker dans Azure. Lisez [cet article](./How-To/set-up-qnamaker-service-azure.md) pour apprendre à le faire.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Comment partager une base de connaissances avec d’autres utilisateurs ?

Le partage fonctionne au niveau d’un service QnA Maker, autrement dit, toutes les bases de connaissances dans le service seront partagées. Lisez [ici](./How-To/collaborate-knowledge-base.md) comment collaborer sur une base de connaissances.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Est-il possible de partager une base de connaissances avec un contributeur qui ne se trouve pas dans le même locataire AAD, dans le but de la modifier ? 

Le partage est basé sur le contrôle d’accès en fonction du rôle Azure (RBAC). Si vous pouvez partager _n’importe quelle_ ressource dans Azure avec un autre utilisateur, vous pouvez également partager QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Si vous avez un plan App Service avec cinq bases de connaissances QnAMaker. Est-il possible d’attribuer des droits de lecture/écriture à 5 utilisateurs différents afin que chacun d’eux ne puisse accéder qu’à une seule base de connaissances QnAMaker ?

Vous pouvez partager l’ensemble d’un service QnAMaker, mais pas des bases de connaissances individuelles.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Comment puis-je modifier le message par défaut si aucune bonne correspondance n’est trouvée ?

Le message par défaut fait partie des paramètres dans votre App Service.
- Accédez à votre ressource App Service dans le portail Azure

![appservice qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Cliquez sur l’option **Paramètres**

![paramètres d’appservice qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modifiez la valeur du paramètre **DefaultAnswer**
- Redémarrer votre App service

![redémarrer appservice qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Pourquoi mon lien SharePoint n’est-il pas extrait ?

Pour plus d’informations, consultez la section [Emplacements des sources de données](./Concepts/data-sources-supported.md#data-source-locations).

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Les mises à jour apportées à ma base de connaissances ne sont pas reflétées lors de la publication. Pourquoi ?

Toute opération de modification, qu’elle soit effectuée dans une mise à jour de table, un test ou un paramètre, doit être enregistrée avant d’être publiée. Pensez à appuyer sur le bouton  **Save and train**  (Enregistrer et entraîner) après chaque opération de modification.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>La base de connaissances prend-elle en charge les données enrichies ou le contenu multimédia ?

La base de connaissances prend en charge Markdown. Cependant, les fonctionnalités de conversion HTML en Markdown sont limitées pour l’extraction automatique à partir d’une URL. Si vous voulez utiliser la fonctionnalité Markdown complète, vous pouvez modifier votre contenu directement dans la table ou charger une base de connaissances avec le contenu enrichi.

Le contenu multimédia, comme les images et les vidéos, n’est pas pris en charge pour l’instant.

### <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker prend-il en charge d’autres langues que l’anglais ?

Affichez plus d’informations sur les [langues prises en charge](./Overview/languages-supported.md).

Si vous avez du contenu dans plusieurs langues, veillez à créer un service distinct pour chaque langue.

## <a name="manage-service"></a>Gérer le service

### <a name="when-should-i-restart-my-app-service"></a>Quand dois-je redémarrer mon instance d’App Service ? 

Actualisez votre instance d’App Service lorsque l’icône d’avertissement apparaît en regard du numéro de version de la base de connaissances dans le tableau **Clés de point de terminaison** sur la page **Paramètres utilisateur**[page](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quand dois-je actualiser mes clés de point de terminaison ?

Actualisez vos clés de point de terminaison si vous pensez qu’elles ont été compromises.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Puis-je utiliser la même ressource Recherche Azure pour des bases de connaissances utilisant plusieurs langues ?

Pour utiliser plusieurs langues et plusieurs bases de connaissances, l’utilisateur doit créer une ressource QnA Maker pour chaque langue. Cela créera un service de recherche Azure distinct par langue. La combinaison de bases de connaissances en différentes langues dans un même service de recherche Azure entraîne une détérioration de la pertinence des résultats.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Comment changer le nom de la ressource Recherche Azure utilisée par QnA Maker ?

Le nom de la ressource Recherche Azure est le nom de ressource QnA Maker avec des lettres aléatoires ajoutées à la fin. De ce fait, il est difficile de faire la distinction entre plusieurs ressources de recherche pour QnA Maker. Créez un service Recherche Azure distinct (en le nommant comme vous le souhaitez), puis connectez-le à votre service QnA. La procédure est similaire à celle que vous devez effectuer pour [mettre à niveau une Recherche Azure](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service).

## <a name="integrate-with-other-services-including-bots"></a>Intégration aux autres services tels que les bots

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Dois-je utiliser Bot Framework pour pouvoir utiliser QnA Maker ?

Non, vous n’avez pas besoin d’utiliser Bot Framework avec QnA Maker. Toutefois, QnA Maker est proposé parmi plusieurs modèles dans Azure Bot Service. Bot Service permet le développement rapide de bot intelligent via Microsoft Bot Framework et s’exécute dans un environnement serverless.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Comment puis-je créer un bot avec QnA Maker ?

Suivez les instructions de [cette](./Tutorials/create-qna-bot.md) documentation pour créer votre bot avec Azure Bot Service.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Comment incorporer le service QnA Maker dans mon site web ?

Procédez comme suit pour incorporer le service QnA Maker en tant que contrôle de conversation web dans votre site web :

1. Créez votre bot de FAQ en suivant les instructions [ici](./Tutorials/create-qna-bot.md).
2. Activez la conversation web en suivant [ces](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) étapes

## <a name="data-storage"></a>Stockage des données

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Où se trouve l’emplacement de stockage et quelles données sont stockées ? 

Lorsque vous avez créé votre service QnA Maker, vous avez sélectionné une région Azure. Vos bases de connaissances et vos fichiers journaux sont stockés dans cette région. 
