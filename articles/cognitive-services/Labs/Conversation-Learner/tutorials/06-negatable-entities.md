---
title: Comment utiliser des entités qui peuvent être niées avec un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des entités qui peuvent être niées avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 2c758d1dd5d4d1e7ab25faccd5280963211181d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388801"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Utiliser des entités qui peuvent être niées avec un modèle Conversation Learner

Ce didacticiel illustre la propriété « negatable » des entités.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel sur les entités Negatable](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails
La propriété « Negatable » d’une entité vous permet d’étiqueter les instances normales (positives) et négatives de l’entité, de lui enseigner des modèles positifs et négatifs et d’effacer la valeur d’une entité existante. Les entités ayant un jeu de propriétés « Negatable » sont appelées entités Negatable dans Conversation Leaner.

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle

1. Sélectionnez **New Model**.
2. Entrez **NegatableEntity** comme **Name**.
3. Sélectionnez **Créer**.

### <a name="entity-creation"></a>Création d’entités

1. Sélectionnez **Entities** dans le volet gauche, puis **New Entity**.
2. Sélectionnez **Custom Trained** comme **Entity Type**.
3. Entrez **nom** comme **Entity Name**.
4. Cochez **Negatable** pour permettre aux utilisateurs de fournir une valeur d’entité, ou de dire que quelque chose n’est *pas* une valeur d’entité, supprimant ainsi la valeur de l’entité correspondante.
5. Sélectionnez **Créer**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Créer la première action

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **Je ne connais pas votre nom.** comme **Bot’s Response...** .
3. Entrez **nom** comme **Disqualifying Entities**.
4. Sélectionnez **Créer**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **Je connais votre nom. Il s’agit de $nom.** comme **Bot’s Response...** .
3. Sélectionnez **Créer**.

> [!NOTE]
> L’entité **nom** a été ajoutée automatiquement aux **Required Entities** (Entités requises) par référence dans l’énoncé de réponse.

Vous avez maintenant deux actions.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Sélectionnez **Train Dialogs** dans le volet gauche, puis **New Train Dialog**.
2. Entrez **Bonjour** comme énoncé de l’utilisateur dans le volet gauche de conversation.
3. Sélectionnez **Score Actions**.
4. Sélectionnez **Je ne connais pas votre nom.** dans la liste d’Actions. Le centile est de 100 % (seule action valide basée sur les contraintes).
5. Tapez **Mon nom est Frank** comme énoncé de l’utilisateur dans le volet gauche de conversation.
6. Mettez en surbrillance **Frank**, puis sélectionnez **+nom**. Les entités qui peuvent être niées ont deux instances : (+) plus ajoute ou remplace la valeur ; (-) moins supprime la valeur.
7. Sélectionnez **Score Actions**. L’entité **nom** est désormais définie comme **Frank** dans la mémoire du modèle. Par conséquent, l’action **Je connais votre nom. Il s’agit de $nom** est disponible.
8. Sélectionnez **Je connais votre nom. Il s’agit de $nom.** dans la liste d’Actions.
9. Entrez **Mon nom n’est pas Frank.** comme énoncé de l’utilisateur dans le volet gauche de conversation.
10. Mettez en surbrillance **Frank**, puis sélectionnez **-nom** pour effacer la valeur de l’entité **nom**.
11. Sélectionnez **Score Actions**.
12. Sélectionnez **Je ne connais pas votre nom.** dans la liste d’Actions.
13. Entrez **Mon nom est Susan.** comme troisième énoncé de l’utilisateur dans le volet gauche de conversation.
14. Mettez en surbrillance **Susan** puis **+nom**. 

![](../media/T06_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités à valeurs multiples](./07-multi-value-entities.md)
