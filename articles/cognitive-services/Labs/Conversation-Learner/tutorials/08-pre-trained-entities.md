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
ms.openlocfilehash: fb70983c2f9fd20368bb8c6803c9568b27141af7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389271"
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

1. Sélectionnez **nouveau modèle**.
2. Entrez **PretrainedEntities** pour **nom**.
3. Sélectionnez **Créer**.

### <a name="entity-creation"></a>Création d’entités

1. Sélectionnez **entités** dans le volet gauche, puis **nouvelle entité**.
2. Sélectionnez **Trained pré/datetimeV2** pour **Type d’entité**.
3. Vérifiez **multiples** pour activer l’entité s’accumulent une ou plusieurs valeurs. Notez que les entités Pre-Trained ne peut pas être negatable.
4. Sélectionnez **Créer**.

![](../media/T08_entity_create.png)

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **la date est $builtin-datetimev2** pour **réponse de robot...** .
3. Sélectionnez **Créer**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **quelle est la date ?** pour **réponse de robot...** . Entités préformées ne peut pas être **entités requises** qu’elles sont reconnues par défaut pour tous les énoncés.
3. Entrez **builtin-datetimev2** pour **entités de cette manière**.
4. Sélectionnez **Créer**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Effectuer la formation du modèle

1. Sélectionnez **boîtes de dialogue Train** dans le volet gauche, puis **nouvelle boîte de dialogue de Train**.
2. Entrez **hello** pour énoncé de l’utilisateur dans le volet gauche de conversation.
3. Sélectionnez **Score Actions**.
4. Sélectionnez **quelle est la date ?** à partir de la liste d’Actions
5. Entrez **aujourd'hui** pour énoncé de l’utilisateur dans le volet gauche de conversation.
    - Le **aujourd'hui** énoncé est automatiquement reconnue par les modèles préentraînés dans LUIS.
    - Survoler les valeurs des entités préentraînées permet d’afficher des données supplémentaires fournies par LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Résolveurs d’entité](./09-entity-resolvers.md)
