---
title: Forum Aux Questions (FAQ) - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: FAQ
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369641"
---
# <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Pourquoi mon URL/mes fichiers n’extraient-ils pas les paires de question-réponse ?

Il est possible que QnA Maker ne puisse pas extraire automatiquement du contenu question-réponse (QnA) à partir des URL de FAQ valides. Dans ce cas, vous pouvez coller le contenu QnA dans un fichier .txt et voir si l’outil peut l’ingérer. Vous pouvez également ajouter manuellement du contenu à votre base de connaissances.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Quelle taille maximale peut avoir une base de connaissances ?

La taille de la base de connaissances dépend de la référence SKU de Recherche Azure que vous choisissez lors de la création du service QnA Maker. Lisez plus d’informations [ici](./Tutorials/choosing-capacity-qnamaker-deployment.md).

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Pourquoi rien ne s’affiche dans la liste déroulante lorsque j’essaie de créer une nouvelle base de connaissances ?

Vous n’avez encore jamais créé de services QnA Maker dans Azure. Lisez [ici](./How-To/set-up-qnamaker-service-azure.md) comment procéder.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Comment partager une base de connaissances avec d’autres utilisateurs ?

Le partage fonctionne au niveau d’un service QnA Maker, autrement dit, toutes les bases de connaissances dans les services seront partagées. Lisez [ici](./How-To/collaborate-knowledge-base.md) comment collaborer sur une base de connaissances.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Comment puis-je modifier le message par défaut si aucune bonne correspondance n’est trouvée ?

Le message par défaut fait partie des paramètres dans votre App Service.
- Accédez à votre ressource App Service dans le portail Azure

![appservice qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Cliquez sur l’option **Paramètres**

![paramètres d’appservice qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modifiez la valeur du paramètre **DefaultAnswer**
- Redémarrer votre App service

![redémarrer appservice qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Pourquoi mon lien SharePoint n’est-il pas extrait ?

L’outil analyse uniquement les URL publiques et ne prend pas en charge les sources de données authentifiées pour l’instant. Vous pouvez aussi télécharger le fichier et utiliser l’option de chargement de fichier pour extraire les questions et réponses.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Les mises à jour apportées à ma base de connaissances ne sont pas reflétées lors de la publication. Pourquoi ?

Toute opération de modification, qu’elle soit effectuée dans la table, le test ou les paramètres, doit être enregistrée avant d’être publiée. Pensez à appuyer sur le bouton Enregistrer et former après chaque opération de modification.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Quand dois-je actualiser mes clés de point de terminaison ?

Vous devez actualiser vos clés de point de terminaison si vous pensez qu’elles ont été compromises.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>La base de connaissances prend-elle en charge les données enrichies ou le contenu multimédia ?

La base de connaissances prend en charge Markdown. Cependant, les fonctionnalités de conversion HTML en Markdown sont limitées pour l’extraction automatique à partir d’une URL. Si vous voulez utiliser la fonctionnalité Markdown complète, vous pouvez modifier votre contenu directement dans la table ou charger une base de connaissances avec le contenu enrichi.

Le contenu multimédia, comme les images et les vidéos, n’est pas pris en charge pour l’instant.

## <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker prend-il en charge d’autres langues que l’anglais ?

Affichez plus d’informations sur les [langues prises en charge](./Overview/languages-supported.md).

Si vous avez du contenu dans plusieurs langues, veillez à créer un service distinct pour chaque langue.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Dois-je utiliser Bot Framework pour pouvoir utiliser QnA Maker ?

Non, vous n’avez pas besoin d’utiliser Bot Framework avec QnA Maker. Toutefois, QnA Maker est proposé parmi plusieurs modèles dans Azure Bot Service. Bot Service permet un développement de bot intelligent rapide via Microsoft Bot Framework, et il s’exécute dans un environnement serverless.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Comment puis-je créer un bot avec QnA Maker ?

Suivez les instructions de [cette](./Tutorials/create-qna-bot.md) documentation pour créer votre bot avec Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Comment incorporer le service QnA Maker dans mon site web ?

Procédez comme suit pour incorporer le service QnA Maker en tant que contrôle de conversation web dans votre site web :

1. Créez votre bot de FAQ en suivant les instructions [ici](./Tutorials/create-qna-bot.md).
2. Activez la conversation web en suivant [ces](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat) étapes


