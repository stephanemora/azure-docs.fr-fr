---
title: Guide pratique pour consigner les dialogues dans un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment consigner les dialogues dans un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9a819f94ae0cc90d86fdbdf4c9201f1d5a2df6ee
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213342"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Guide pratique pour consigner les dialogues dans un modèle Conversation Learner

Ce didacticiel montre comment des dialogues journalisés sont employés pour mieux former des modèles Conversation Learner à partir d’interactions enregistrées avec des utilisateurs réels.

## <a name="video"></a>Vidéo

[![Didacticiel sur les dialogues journalisés en préversion](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

et que le modèle de météo ait été créé dans le cadre des didacticiels précédents.

## <a name="details"></a>Détails
Les dialogues journalisés sont des journaux enregistrés des interactions de votre bot avec des utilisateurs finaux. Ces dialogues journalisés vous permettent de corriger des étiquettes d’entité et des sélections d’action afin d’améliorer les performances du modèle et les performances globales du système.

## <a name="steps"></a>Étapes

Dans l’interface utilisateur web, cliquez sur « Import Tutorials » (Importer des didacticiels), puis sélectionnez le modèle nommé « Tutorial-11-LogDialogs ».

Celui-ci contient une entité nommée « city » (ville), et des actions conçues pour répondre à des interrogations concernant la météo dans cette ville. Deux dialogues d’apprentissage ont été utilisés pour former le modèle. Par conséquent, les attentes en matière de performances sont assez faibles. Le modèle s’améliorerait avec un apprentissage supplémentaire et une exposition à des interactions avec des utilisateurs réels.

### <a name="create-a-new-conversation"></a>Créer une conversation

1. Dans le volet de gauche, cliquez sur Log Dialogs (dialogues journalisés), puis sur le bouton New Log Dialog (Nouveau dialogue journalisé).
2. Dans le panneau de conversation affichant « Type your message... » (Tapez votre message...), entrez « Prévisions météo Austin ».
3. Cliquez sur le bouton Done Testing (Test terminé).
4. Cliquez sur le dialogue journalisé « Prévisions météo Austin » dans la liste.
5. Cliquez sur l’énoncé « Prévisions météo Austin » dans le panneau de conversation.
6. Cliquez sur « Austin », puis sur « city » dans la liste d’entités.
7. Cliquez sur le bouton « Submit Changes » (Envoyer les modifications).
    - Ce changement de valeur d’entité entraîne des modifications en aval de la conversation dans la mesure où nous avons de nouvelles valeurs d’entité en mémoire. Des actions ultérieures sont probablement devenues non valides, en particulier celles impliquant l’entité « city ».
8. Cliquez sur « Quelle ville ? » dans le panneau de conversation.
9. Sélectionnez la réponse, « The weather in Austin is probably sunny » (la météo à Austin est probablement ensoleillée).
10. Cliquez sur le bouton « Save As Train Dialog » (Enregistrer en tant que dialogue d’apprentissage).
    - L’apprentissage démarre immédiatement

Une dernière remarque. Selon les besoins, vous pouvez désactiver la fonctionnalité de journalisation des conversations en accédant aux paramètres et en désactivant l’option « Log Conversations » (Journaliser les conversations).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rappel de la détection d’entités](./12-entity-detection-callback.md)
