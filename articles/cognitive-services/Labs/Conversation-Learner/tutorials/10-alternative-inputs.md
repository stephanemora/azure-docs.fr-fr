---
title: Guide pratique pour utiliser les entrées de remplacement avec Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les entrées de remplacement avec Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 10335f9c74b9033b303c960a77af136cc80d75bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708204"
---
# <a name="how-to-use-alternative-inputs"></a>Guide pratique pour utiliser les entrées de remplacement

Ce didacticiel montre comment utiliser le champ Entrées alternatives pour des énoncés de l’utilisateur dans l’interface d’apprentissage.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel Entrées alternatives](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Les entrées alternatives sont des énoncés de remplacement sémantiquement équivalents que l’utilisateur peut avoir dits à un moment donné dans une boîte de dialogue d’apprentissage. Elles permettent de spécifier de manière plus compacte les variantes des énoncés, sans avoir à traiter chacune d’entre elles dans des boîtes de dialogue d’apprentissage distinctes.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l'interface utilisateur web, cliquez sur « Nouveau modèle ».
2. Dans le champ « Nom », tapez « AlternativeInputs » et appuyez sur Entrée.
3. Cliquez sur le bouton « Créer ».

### <a name="entity-creation"></a>Création d’entités

1. Dans le panneau gauche, cliquez sur « Entités », puis sur le bouton « Nouvelle entité ».
2. Sous « Type d'entité », sélectionnez « Apprentissage personnalisé ».
3. Sous « Nom de l’entité », entrez « Ville ».
4. Cliquez sur le bouton « Créer ».

![](../media/T10_actions.png)

Créons à présent trois actions.

### <a name="create-the-first-action"></a>Créer la première action

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle action ».
2. Dans le champ « Réponse du bot... », tapez « Quelle ville ? ».
3. Dans le champ « Entité attendue dans la réponse de l’utilisateur », tapez « Ville ».
4. Dans le champ « Entités disqualifiantes », tapez « ville ».
5. Cliquez sur le bouton « Créer ».

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle action ».
2. Dans le champ « Réponse du bot... », tapez « Il fait probablement beau à $city ».
3. Cliquez sur le bouton « Créer ».

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Créer la troisième action

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle action ».
2. Dans le champ « Réponse du bot... », tapez « Essayez de demander la météo ».
3. Dans le champ « Entités disqualifiantes », tapez « ville ».
4. Cliquez sur le bouton « Créer ».

![](../media/T10_action_create_3.png)

Vous avez maintenant trois actions.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Former le modèle

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue d'apprentissage », puis sur le bouton « Nouveau dialogue d'apprentissage ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Quel temps fait-il ? ».
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse, « Quelle ville ? ».
5. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Denver ».
6. Cliquez sur le bouton « Noter les actions ».
7. Sélectionnez la réponse, « La météo à Denver est probablement ensoleillée ».
8. Cliquez sur le bouton « Submit Changes » (Envoyer les modifications).

![](../media/T10_training_1.png)

Poursuivons la formation du modèle en créant une autre boîte de dialogue d’apprentissage.

### <a name="second-model-train-dialog"></a>Deuxième boîte de dialogue d’apprentissage du modèle

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue d'apprentissage », puis sur le bouton « Nouveau dialogue d'apprentissage ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Que pouvez-vous faire ? ».
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse, « Essayez de demander la météo ».
5. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Quel temps fait-il à Seattle ? ».
6. Cliquez sur « Seattle », puis sur « ville » dans la liste d’entités.
7. Cliquez sur le bouton « Noter les actions ».
8. Sélectionnez la réponse, « La météo à Seattle est probablement ensoleillée ».
9. Cliquez sur le bouton « Submit Changes » (Envoyer les modifications).

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Troisième boîte de dialogue d’apprentissage du modèle utilisant une entrée alternative

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue d'apprentissage », puis sur le bouton « Nouveau dialogue d'apprentissage ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Aide ».
3. Cliquez sur le bouton « Noter les actions ».
    - Le modèle n’est pas certain de la meilleure option, donc il choisira le centile le plus élevé par défaut.
4. Cliquez sur le bouton de « Abandonner l’apprentissage », puis sur le bouton « Confirmer ».

![](../media/T10_training_3.png)

Améliorons les paramètres du système en utilisant des entrées alternatives. Vous pouvez ajouter des entrées alternatives lors de l’apprentissage ou plus tard.

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue d’apprentissage », puis sélectionnez « Que pouvez-vous faire ? » dans la liste des boîtes de dialogue d’apprentissage.
1. Cliquez sur l’énoncé « Que pouvez-vous faire ? ». dans le panneau de conversation.
1. Dans le champ « Ajouter une entrée alternative... », tapez « aide », puis appuyez sur Entrée.
1. Cliquez sur le bouton « Enregistrer les modifications ».

![](../media/T10_training_4.png)

Ajoutons une autre entrée alternative pour gérer Houston.

1. Cliquez sur « Quel temps fait-il à Seattle ? ». dans le panneau de conversation.
1. Dans le champ « Ajouter une entrée alternative... », tapez « prévisions pour Houston », puis appuyez sur Entrée.
   - Le message d’erreur souligne que les entrées alternatives de faits doivent être sémantiquement équivalentes et contenir les mêmes entités que l’énoncé d’origine, pas seulement les mêmes valeurs d’entités. La présence des mêmes entités est obligatoire.
1. Cliquez sur « Houston » et sélectionnez « ville » dans la liste des entités.
1. Dans le champ « Ajouter une entrée alternative... », tapez « prévisions pour Seattle », puis appuyez sur Entrée.
1. Cliquez sur « Seattle » et sélectionnez « ville » dans la liste des entités.
1. Cliquez sur le bouton « Enregistrer les modifications ».
1. Cliquez sur le bouton « Enregistrer la modification ».

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Tester le modèle

1. Dans le panneau gauche, cliquez sur « Boîtes de dialogue du journal », puis sur le bouton « Nouvelle boîte de dialogue du journal ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « aidez-moi ».
3. Dans le panneau de conversation, sous « Entrez votre message... », tapez « prévisions pour Denver ».

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Consigner les dialogues](./11-log-dialogs.md)
