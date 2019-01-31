---
title: Comment ajouter des entités préentraînées à un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment ajouter des entités préentraînées à un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: afa927009e684fa7f8c6217c91dcb589b331b5f5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224171"
---
# <a name="how-to-add-pre-trained-entities"></a>Comment ajouter des entités préentraînées
Ce didacticiel explique comment ajouter des entités préentraînées à votre modèle Conversation Learner.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel Entités préentraînées](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

Les entités préentraînées reconnaissent les types classiques d’entités, tels que les nombres, les dates, les montants monétaires, etc.  Elles sont prêtes à l’emploi, ne nécessitent aucune formation et leur comportement ne peut pas être modifié, contrairement aux entités personnalisées.  Par défaut, les entités préentraînées contiennent plusieurs valeurs, car elles accumulent chaque instance identifiée de l’entité.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l'interface utilisateur web, cliquez sur « Nouveau modèle ».
2. Dans le champ « Nom », tapez « PretrainedEntities », puis appuyez sur Entrée.
3. Cliquez sur le bouton « Créer ».

### <a name="entity-creation"></a>Création d’entités

1. Dans le panneau gauche, cliquez sur « Entités », puis sur le bouton « Nouvelle entité ».
2. Sélectionnez le « Type d’entité » « Pré-Trained/datetimeV2 ».
3. Cochez la case « À valeurs multiples ».
    - Les entités à valeurs multiples accumulent une ou plusieurs valeurs dans l'entité.
    - Les propriétés pouvant être niées sont désactivées pour les entités préentraînées.
4. Cliquez sur le bouton « Créer ».

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Créer la première action

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle action ».
2. Dans le champ « Réponse du bot... », entrez « La date est $luis-datetimev2 ».
3. Cliquez sur le bouton « Créer ».

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>Créer la deuxième action

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « Nouvelle action ».
2. Dans le champ « Réponse du bot... », entrez « Quelle est la date ? ».
    - Les entités préentraînées ne peuvent pas être des entités obligatoires, puisqu’elles sont reconnues par défaut pour tous les énoncés de l’utilisateur.
3. Dans le champ « Entités disqualifiantes », entrez « builtin-datetimev2 ».
4. Cliquez sur le bouton « Créer ».

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>Former le modèle

1. Dans le volet gauche, cliquez sur « Dialogues de formation », puis sur le bouton « Nouveau dialogue de formation ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Bonjour ».
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse « Quelle est la date ? ».
5. Dans le panneau de conversation, sous « Entrez votre message... », tapez « aujourd’hui ».
    - L’énoncé aujourd'hui est automatiquement reconnu par les modèles préentraînés dans LUIS.
    - Survoler les valeurs des entités préentraînées permet d’afficher des données supplémentaires fournies par LUIS.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Résolveurs d’entité](./09-entity-resolvers.md)
