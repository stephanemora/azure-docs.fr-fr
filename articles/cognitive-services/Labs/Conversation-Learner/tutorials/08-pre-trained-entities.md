---
title: Comment ajouter des entités préentraînées à un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment ajouter des entités préentraînées à un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704116"
---
# <a name="how-to-add-pre-trained-entities"></a>Comment ajouter des entités préentraînées
Ce didacticiel explique comment ajouter des entités préentraînées à votre modèle Conversation Learner.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel Entités préentraînées](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails

Les entités préentraînées reconnaissent les types classiques d’entités, tels que les nombres, les dates, les montants monétaires, etc.  Elles sont prêtes à l’emploi, ne nécessitent aucune formation et leur comportement ne peut pas être modifié, contrairement aux entités personnalisées.  Par défaut, les entités préentraînées contiennent plusieurs valeurs, car elles accumulent chaque instance identifiée de l’entité.

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle

1. Sélectionnez **New Model**.
2. Entrez **PretrainedEntities** comme **Name**.
3. Sélectionnez **Créer**.

### <a name="entity-creation"></a>Création d’entités

1. Sélectionnez **Entities** dans le volet gauche, puis **New Entity**.
2. Sélectionnez **Pre-Trained/datetimeV2** comme **Entity Type**.
3. Cochez **Multi-valued** pour permettre à l’entité d’accumuler une ou plusieurs valeurs. Notez que les entités préentraînées ne peuvent pas être niées.
4. Sélectionnez **Créer**.

![](../media/T08_entity_create.png)

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **La date est $builtin-datetimev2** comme **Bot’s Response...** .
3. Sélectionnez **Créer**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **Quelle est la date ?** comme **Bot’s Response...** . Les entités préentraînées ne peuvent pas être des **entités obligatoires**, puisqu’elles sont reconnues par défaut pour tous les énoncés.
3. Entrez **builtin-datetimev2** comme **Disqualifying Entities**.
4. Sélectionnez **Créer**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Sélectionnez **Train Dialogs** dans le volet gauche, puis **New Train Dialog**.
2. Entrez **Bonjour** comme énoncé de l’utilisateur dans le volet gauche de conversation.
3. Sélectionnez **Score Actions**.
4. Sélectionnez **Quelle est la date ?** dans la liste d’Actions
5. Entrez **aujourd’hui** comme énoncé de l’utilisateur dans le volet gauche de conversation.
    - L’énoncé **aujourd’hui** est automatiquement reconnu par les modèles préentraînés dans LUIS.
    - Survoler les valeurs des entités préentraînées permet d’afficher des données supplémentaires fournies par LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Résolveurs d’entité](./09-entity-resolvers.md)
