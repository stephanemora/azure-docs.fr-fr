---
title: Comment utiliser des entités qui peuvent être niées avec un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des entités qui peuvent être niées avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168185"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Utiliser des entités qui peuvent être niées avec un modèle Conversation Learner

Ce didacticiel illustre la propriété « negatable » des entités.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel sur les entités Negatable](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails
La propriété « Negatable » d’une entité vous permet d’étiqueter les deux normal (positif) et négatifs des instances de l’entité, enseigner basés sur des modèles positifs et négatifs et effacer la valeur d’une entité existante. Les entités ayant un jeu de propriétés « Negatable » sont appelées entités Negatable dans Conversation Leaner.

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle

1. Sélectionnez **nouveau modèle**.
2. Entrez **NegatableEntity** pour **nom**.
3. Sélectionnez **Créer**.

### <a name="entity-creation"></a>Création d’entités

1. Sélectionnez **entités** dans le volet gauche, puis **nouvelle entité**.
2. Sélectionnez **personnalisé formé** pour **Type d’entité**.
3. Entrez **nom** pour **nom de l’entité**.
4. Vérifiez **Negatable** pour permettre aux utilisateurs de fournir une valeur de l’entité, disons que quelque chose est *pas* une entité valeur supprimant ainsi la valeur de l’entité correspondante.
5. Sélectionnez **Créer**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Créer la première action

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **je ne sais pas votre nom.** pour **réponse de robot...** .
3. Entrez **nom** pour **donne le droit de cette manière**.
4. Sélectionnez **Créer**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **savoir votre nom. Il est $name.** pour **réponse de robot...** .
3. Sélectionnez **Créer**.

> [!NOTE]
> Le **nom** entité a été ajoutée automatiquement comme un **entités requises** par référence dans l’énoncé de réponse.

Vous avez maintenant deux actions.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Sélectionnez **boîtes de dialogue Train** dans le volet gauche, puis **nouvelle boîte de dialogue de Train**.
2. Entrez **hello** pour énoncé de l’utilisateur dans le volet gauche de conversation.
3. Sélectionnez **Score Actions**.
4. Sélectionnez **je ne sais pas votre nom.** dans la liste d’Actions. Le centile est 100 % en tant que la seule Action valide selon les contraintes.
5. Entrez **Mon nom est Frank** pour énoncé de l’utilisateur dans le volet gauche de conversation.
6. Mettez en surbrillance **Frank** puis sélectionnez **+ nom**. Entités negatable apparaissent deux instances : (+) et ajoute ou remplace la valeur ; (-) moins supprime la valeur.
7. Sélectionnez **Score Actions**. Le **nom** entité est maintenant définie en tant que **Frank** en mémoire du modèle, donc la **savoir votre nom. Il s’agit $name** Action est disponible.
8. Sélectionnez **savoir votre nom. Il est $name.** dans la liste d’Actions.
9. Entrez **Mon nom est ne pas Frank.** pour un énoncé de l’utilisateur dans le volet gauche de conversation.
10. Mettez en surbrillance **Frank** puis sélectionnez **-nom** pour effacer la valeur à partir de la **nom** entité.
11. Sélectionnez **Score Actions**.
12. Sélectionnez **je ne sais pas votre nom.** dans la liste d’Actions.
13. Entrez **Mon nom est Susan.** pour énoncé tiers de l’utilisateur dans le volet gauche de conversation.
14. Mettez en surbrillance **Susan** puis **+ nom** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités à valeurs multiples](./07-multi-value-entities.md)
