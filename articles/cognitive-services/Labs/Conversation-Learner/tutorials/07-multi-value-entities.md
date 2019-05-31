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
ms.openlocfilehash: 1f62def5e498f3f744beaed0cda207e1a75bfdf2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387962"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Utiliser des entités à valeurs multiples avec un modèle d’Apprenant de conversation
Ce tutoriel présente la propriété « À valeur multiple » des entités.

## <a name="video"></a>Vidéo

[![Aperçu de didacticiel les entités à valeurs multiples](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails
Les entités à valeurs multiples accumulent des valeurs dans une liste plutôt que de stocker une valeur unique.  Ces entités s'avèrent très utiles lorsque les utilisateurs ont la possibilité de spécifier plusieurs valeurs. Des garnitures sur une pizza par exemple.

Pour une entité marquée comme « à valeurs multiples », chaque instance reconnue de celle-ci est ajoutée à une liste dans la mémoire du bot. Toute reconnaissance ultérieure s'ajoutera à la valeur de l'entité au lieu de l'écraser.

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle

1. Sélectionnez **nouveau modèle**.
2. Entrez **MultiValueEntities** pour **nom**.
3. Sélectionnez **Créer**.

### <a name="entity-creation"></a>Création d’entités

1. Sélectionnez **entités** dans le volet gauche, puis **nouvelle entité**.
2. Sélectionnez **personnalisé formé** pour **Type d’entité**.
3. Entrez **GARNITURE** pour **nom de l’entité**.
4. Vérifiez **multiples** pour activer l’entité s’accumulent une ou plusieurs valeurs.
5. Vérifiez **Negatable**.
6. Sélectionnez **Créer**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Créer la première action

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **Voici votre GARNITURE : $toppings** pour **réponse de robot...** . Le symbole dollar début indique une référence d’entité.
3. Sélectionnez **Créer**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **les ingrédients vous aimeriez ?** pour **réponse de robot...** .
3. Entrez **GARNITURE** pour **donne le droit de cette manière**.
4. Sélectionnez **Créer**.

Vous avez maintenant deux actions.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Sélectionnez **boîtes de dialogue Train** dans le volet gauche, puis **nouvelle boîte de dialogue de Train**.
2. Entrez **Bonjour** pour énoncé de l’utilisateur dans le volet gauche de conversation.
3. Sélectionnez **Score Actions**.
4. Sélectionnez **les ingrédients vous aimeriez ?** à partir de la liste d’Actions. Le centile est 100 % en tant que la seule Action valide selon les contraintes.
5. Entrez **fromage et champignons** pour énoncé de l’utilisateur dans le volet gauche de conversation.
6. Mettez en surbrillance **fromage** puis sélectionnez **+ GARNITURE**.
7. Mettez en surbrillance **champignons** puis sélectionnez **+ GARNITURE**.
8. Sélectionnez **Score Actions**.
9. Sélectionnez **Voici votre GARNITURE : $toppings** à partir de la liste d’Actions.
10. Entrez **ajouter poivre** pour l’énoncé suivant de l’utilisateur dans le volet gauche de conversation.
11. Mettez en surbrillance **poivre** puis sélectionnez **+ GARNITURE**.
12. Sélectionnez **Score Actions**.
13. Sélectionnez **Voici votre GARNITURE : $toppings** à partir de la liste d’Actions.
14. Entrez **supprimer fromage** pour énoncé de tiers de l’utilisateur dans le volet gauche de conversation.
15. Mettez en surbrillance **fromage** puis sélectionnez **-GARNITURE**.
16. Sélectionnez **Score Actions**.
17. Sélectionnez **Voici votre GARNITURE : $toppings** à partir de la liste d’Actions.

![](../media/T07_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités préentraînées](./08-pre-trained-entities.md)
