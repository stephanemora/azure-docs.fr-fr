---
title: Comment utiliser des entités à valeurs multiples avec un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Apprenez à utiliser des entités à valeurs multiples avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228302"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Utiliser des entités à valeurs multiples avec un modèle d’Apprenant de conversation
Ce tutoriel présente la propriété « À valeur multiple » des entités.

## <a name="video"></a>Vidéo

[![Préversion du tutoriel Entités à valeurs multiples](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Les entités à valeurs multiples accumulent des valeurs dans une liste plutôt que de stocker une valeur unique.  Ces entités s'avèrent très utiles lorsque les utilisateurs ont la possibilité de spécifier plusieurs valeurs. Des garnitures sur une pizza par exemple.

Pour une entité marquée comme « à valeurs multiples », chaque instance reconnue de celle-ci est ajoutée à une liste dans la mémoire du bot. Toute reconnaissance ultérieure s'ajoutera à la valeur de l'entité au lieu de l'écraser.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l'interface utilisateur web, cliquez sur « Nouveau modèle ».
2. Dans le champ « Nom », entrez « Entités à valeurs multiples » et appuyez sur Entrée.
3. Cliquez sur le bouton « Créer ».

### <a name="entity-creation"></a>Création d’entités

1. Dans le panneau gauche, cliquez sur « Entités », puis sur le bouton « Nouvelle entité ».
2. Sous « Type d'entité », sélectionnez « Apprentissage personnalisé ».
3. Sous « Nom de l'entité », entrez « Garnitures ».
4. Cochez la case « À valeurs multiples ».
    - Les entités à valeurs multiples accumulent une ou plusieurs valeurs dans l'entité.
5. Cochez la case à cocher « Ne peut être nié ».
    - La propriété « Ne peut être nié » est présentée dans un autre tutoriel.
6. Cliquez sur le bouton « Créer ».

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Créer la première action

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle Action ».
2. Dans le champ « La réponse du bot... », entrez « Voici vos garnitures : $garnitures ».
    - Le symbole dollar indique une référence d'entité.
3. Cliquez sur le bouton « Créer ».

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle Action ».
2. Dans le champ « La réponse du bot... », entrez « Quelles garnitures vous feraient-elles plaisir ? ».
3. Dans le champ « Entités disqualifiantes », entrez « garnitures ».
4. Cliquez sur le bouton « Créer ».

Vous avez maintenant deux actions.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Bonjour ».
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse « Quelles garnitures vous feraient-elles plaisir ? ».
    - Le centile est de 100 % (seule action valide basée sur les contraintes).
5. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Fromage et champignons ».
6. Cliquez sur « Fromage », puis choisissez l'étiquette « + Garnitures ».
7. Cliquez sur « Champignons », puis choisissez l’étiquette « + Garnitures ».
8. Cliquez sur le bouton « Noter les actions ».
9. Sélectionnez la réponse, « Voici vos garnitures : $garnitures ».
10. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Ajouter du poivre ».
11. Cliquez sur « Poivre », puis choisissez l'étiquette « + Garnitures ».
12. Cliquez sur le bouton « Noter les actions ».
13. Sélectionnez la réponse, « Voici vos garnitures : $garnitures ».
14. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Supprimer le fromage ».
15. Cliquez sur « Fromage », puis choisissez l'étiquette « + Garnitures ».
16. Cliquez sur le bouton « Noter les actions ».
17. Sélectionnez la réponse, « Voici vos garnitures : $garnitures ».

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités préentraînées](./08-pre-trained-entities.md)
