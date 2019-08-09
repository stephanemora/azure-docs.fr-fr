---
title: Comment utiliser des entités à valeurs multiples avec un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Apprenez à utiliser des entités à valeurs multiples avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704089"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Utiliser des entités à valeurs multiples avec un modèle d’Apprenant de conversation
Ce tutoriel présente la propriété « À valeur multiple » des entités.

## <a name="video"></a>Vidéo

[![Préversion du tutoriel Entités à valeurs multiples](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails
Les entités à valeurs multiples accumulent des valeurs dans une liste plutôt que de stocker une valeur unique.  Ces entités s'avèrent très utiles lorsque les utilisateurs ont la possibilité de spécifier plusieurs valeurs. Des garnitures sur une pizza par exemple.

Pour une entité marquée comme « à valeurs multiples », chaque instance reconnue de celle-ci est ajoutée à une liste dans la mémoire du bot. Toute reconnaissance ultérieure s'ajoutera à la valeur de l'entité au lieu de l'écraser.

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle

1. Sélectionnez **New Model**.
2. Entrez **MultiValueEntities** comme **Name**.
3. Sélectionnez **Créer**.

### <a name="entity-creation"></a>Création d’entités

1. Sélectionnez **Entities** dans le volet gauche, puis **New Entity**.
2. Sélectionnez **Custom Trained** comme **Entity Type**.
3. Entrez **garniture** comme **Entity Name**.
4. Cochez **Multi-valued** pour permettre à l’entité d’accumuler une ou plusieurs valeurs.
5. Cochez **Negatable**.
6. Sélectionnez **Créer**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Créer la première action

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **Voici votre garniture : $garniture** comme **Bot’s Response...** . Le symbole dollar indique qu’il s’agit d’une référence d’entité.
3. Sélectionnez **Créer**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **Quelle garniture souhaitez-vous ?** comme **Bot’s Response...** .
3. Entrez **garniture** comme **Disqualifying Entities**.
4. Sélectionnez **Créer**.

Vous avez maintenant deux actions.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Sélectionnez **Train Dialogs** dans le volet gauche, puis **New Train Dialog**.
2. Entrez **Bonjour** comme énoncé de l’utilisateur dans le volet gauche de conversation.
3. Sélectionnez **Score Actions**.
4. Sélectionnez **Quelle garniture souhaitez-vous ?** dans la liste d’actions. Le centile est de 100 % (seule action valide basée sur les contraintes).
5. Entrez **fromage et champignons** comme énoncé de l’utilisateur dans le volet gauche de conversation.
6. Mettez en surbrillance **fromage**, puis sélectionnez **+garniture**.
7. Mettez en surbrillance **champignons**, puis sélectionnez **+garniture**.
8. Sélectionnez **Score Actions**.
9. Sélectionnez **Voici votre garniture : $garniture** dans la liste des actions.
10. Entrez **ajouter du poivre** comme énoncé suivant de l’utilisateur dans le volet gauche de conversation.
11. Mettez en surbrillance **poivre**, puis sélectionnez **+garniture**.
12. Sélectionnez **Score Actions**.
13. Sélectionnez **Voici votre garniture : $garniture** dans la liste des actions.
14. Entrez **supprimer fromage** comme troisième énoncé de l’utilisateur dans le volet gauche de conversation.
15. Mettez en surbrillance **fromage**, puis sélectionnez **-garniture**.
16. Sélectionnez **Score Actions**.
17. Sélectionnez **Voici votre garniture : $garniture** dans la liste des actions.

![](../media/T07_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités préentraînées](./08-pre-trained-entities.md)
