---
title: Modèle Conversation Learner de démonstration, réinitialisation de mot de passe – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Conversation Learner de démonstration.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6436193dadb5933074fdce861a31672a0f4849de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227333"
---
# <a name="demo-password-reset"></a>Démo : Réinitialisation de mot de passe
Ce didacticiel présente un simple bot de support technique qui peut aider à réinitialiser des mot de passe, optimisé par Conversation Learner. Le modèle du bot peut apprendre des flux de dialogue non triviaux et des séquences à plusieurs tours incluant des classes hors domaine. La tâche peut être accomplie sans code ou entité.

## <a name="video"></a>Vidéo

[![Aperçu de démonstration Mot de passe](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Configuration requise
Ce didacticiel requiert l’exécution du robot de réinitialisation de mot de passe.

    npm run demo-password

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur Tutorial Demo Password Reset (Tutoriel de démonstration de réinitialisation de mot de passe). 

### <a name="actions"></a>Actions

Le modèle contient un ensemble d’actions visant à aider les utilisateurs à résoudre des problèmes courants liés aux mots de passe.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogues d’apprentissage

Le modèle contient également plusieurs dialogues d’apprentissage, dont certains illustrent une formation de classe de domaine. Par exemple, des utilisateurs qui pourraient demander des itinéraire de voiture. L’exemple de bot a été formé à des fins de démonstration, et répond simplement en déclarant qu’il ne peut pas aider. La liste des dialogues d’apprentissage existants figure sous « Train Dialogs » (Dialogues d’apprentissage) dans le volet gauche.

![](../media/tutorial_pw_reset_entities.PNG)

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. Dans le panneau de conversation, sous « Type your message... » (Entrez votre message...), tapez « J’ai perdu mon mot de passe ».
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
4. Sélectionnez la réponse « Is that for your local account or Microsoft account » (Est-ce que cela concerne votre compte local ou votre compte Microsoft ?).
5. Dans le panneau de conversation, sous « Type your message... » (Entrez votre message...), tapez « un compte local ».
6. Cliquez sur le bouton « Sore actions » (Noter les actions).
7. Sélectionnez la réponse « Which version of Windows do you have? » (Quelle version de Windows avez-vous ?).
8. Dans le panneau de conversation, sous « Type your message... » (Tapez votre message...), entrez « windows xp ».
9. Cliquez sur le bouton « Sore actions » (Noter les actions).
10. Cliquez sur le bouton « + Action ».
11. Dans le champ « Bot’s response... » (Réponse du bot...), entrez « SOLUTION : Comment réinitialiser un mot de passe sous Windows XP... ».
12. Cliquez sur le bouton « Create » (Créer).

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Dialogues d’apprentissage pour les scénarios hors du domaine

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis sur le dialogue d’apprentissage existant « toy stores » (magasins de jouets).
2. Dans le volet de la conversation, cliquez sur l’énoncé « toy stores » (magasins de jouets).
3. Dans le champ « Add alternative input... » (Ajoutez une autre entrée...), tapez « recherche web », puis appuyez sur Entrée.
4. Dans le champ « Add alternative input... » (Ajoutez une autre entrée...), tapez « réservation de vol », puis appuyez sur Entrée.
5. Cliquez sur le bouton « Save changes » (Enregistrer les modifications).
6. Cliquez sur le bouton « Enregistrer la modification ».
7. Dans le volet gauche, cliquez sur « Log Dialogs » (Dialogues journalisés), puis sur le bouton « New Log Dialog » (Nouveau dialogue journalisé).
8. Dans le panneau de conversation, sous « Type your message... » (Entrez votre message...), tapez « J’ai perdu mon mot de passe ».
9. Dans le panneau de conversation, sous « Type your message... » (Entrez votre message...), tapez « Compte Microsoft ».
10. Dans le panneau de conversation, sous « Type your message... » (Tapez votre message...), entrez « Merci ».
11. Cliquez sur le bouton « Done Testing » (Test terminé).
12. Cliquez sur le dialogue journalisé « i can’t find my password » (j’ai perdu mon mot de passe) de l’affichage de grille.
13. Dans le panneau de conversation, cliquez sur la réponse incorrectement rendue « Solution : comment réinitialiser un mot de passe de compte Microsoft ».
14. Cliquez sur le bouton « + Action ».
15. Dans le champ « Bot’s response... » (Réponse du bot...), entrez « Je vous en prie ».
16. Cliquez sur le bouton « Create » (Créer).
17. Cliquez sur le bouton « Save As Train Dialog » (Enregistrer en tant que dialogue d’apprentissage).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démonstration : commande de pizza](./demo-pizza-order.md)
