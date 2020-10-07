---
title: S’intégrer à d’autres applications - QnA Maker
description: QnA Maker s’intègre aux applications clientes telles que les chatbots, ainsi qu’à d’autres services de traitement en langage naturel tels que Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ff5e64dd31542b1e31df6d9ca709567b322995ce
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776525"
---
# <a name="design-knowledge-base-for-client-applications"></a>Concevoir une base de connaissances pour les applications clientes

QnA Maker s’intègre aux applications clientes telles que les chatbots, ainsi qu’à d’autres services de traitement en langage naturel tels que Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Intégration avec un client conversationnel

QnA Maker s’intègre aux applications clientes de conversation telles que [Microsoft Bot Framework](https://dev.botframework.com/). Le texte envoyé à QnA Maker n’a pas besoin d’être nettoyé ou transformé. QnA Maker accepte les langages naturels et retourne la meilleure réponse.

## <a name="create-a-bot-without-writing-any-code"></a>Créer un bot sans écrire de code

Après avoir publié votre base de connaissances, créez un bot à partir de la page **Publier**, en sélectionnant le bouton **Créer un bot**. Utilisez le [tutoriel de création de bot](../Quickstarts/create-publish-knowledge-base.md) pour découvrir ce qui se passe une fois ce bouton sélectionné.

## <a name="providing-multi-turn-conversations"></a>Conversations multitours

Un client bot fournit la meilleure réponse sélectionnée à partir de votre base de connaissances, et peut fournir des invites de suivi si la réponse fait partie d’une paire de questions-réponses multitour. Découvrez [comment](../how-to/multiturn-conversation.md) ajouter des jeux de questions et réponses de conversation multitour à votre base de connaissances.

## <a name="natural-language-processing"></a>Traitement en langage naturel

Bien que QnA Maker traite les questions qui utilisent un traitement en langage naturel, il peut également être utilisé dans le cadre d’un système plus grand qui répond aux questions de plusieurs bases de connaissances. Vous pouvez combiner QnA Maker avec un autre service cognitif, Language Understanding (LUIS), pour fournir un traitement en langage naturel avant d’accéder à une base de connaissances spécifique. Découvrez quand et comment utiliser conjointement [LUIS et QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [concepts](development-lifecycle-knowledge-base.md) du cycle de développement de QnA Maker.