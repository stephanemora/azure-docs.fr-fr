---
title: Obtenir la réponse par défaut - QnA Maker
description: La réponse par défaut est retournée quand il n’existe aucune correspondance avec la question. Vous souhaiterez peut-être remplacer la réponse par défaut standard.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979966"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Modifier la réponse par défaut pour une ressource QnA Maker

La réponse par défaut d’une base de connaissances doit être retournée quand aucune réponse n’a été trouvée. Si vous utilisez une application cliente, telle que le [service Azure Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), celle-ci peut également avoir sa propre réponse par défaut, indiquant qu’aucune réponse n’a atteint le seuil de score.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Définir une réponse par défaut quand vous créez la base de connaissances

Lorsque vous créez une base de connaissances, le texte de la réponse par défaut correspond à un paramètre. Si vous choisissez de ne pas la définir pendant le processus de création, vous pourrez la modifier ultérieurement à l’aide de la procédure suivante.

## <a name="change-default-answer-in-qna-maker-portal"></a>Modifier la réponse par défaut dans le portail QnA Maker

La réponse par défaut de la base de connaissances est retournée quand aucune réponse n’est retournée par le service QnA Maker.

1. Connectez-vous au [portail QnA Maker](https://www.qnamaker.ai/), puis sélectionnez votre base de connaissances dans la liste.
1. Dans la barre de navigation, sélectionnez **Paramètres**.
1. Changez la valeur de **Texte de réponse par défaut** dans la section **Gérer une base de connaissances**.

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Capture d’écran du portail QnA Maker, page Paramètres, avec la zone de texte Réponse par défaut mise en évidence.":::

1. Sélectionnez **Enregistrer et entraîner** pour enregistrer la modification.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une base de connaissances](../How-to/manage-knowledge-bases.md)