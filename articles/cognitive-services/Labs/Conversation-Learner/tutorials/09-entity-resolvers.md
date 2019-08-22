---
title: Résolveurs d’entité dans un modèle Conversation Learner – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les résolveurs d’entité avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704040"
---
# <a name="entity-resolvers"></a>Résolveurs d’entité

Ce tutoriel montre comment utiliser les résolveurs d’entité avec un modèle Conversation Learner

## <a name="video"></a>Vidéo

[![Tutoriel sur les résolveurs d’entité en préversion](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution

    npm run tutorial-general

## <a name="details"></a>Détails

- Le type de résolveur est une propriété facultative d’Entités personnalisées.
- Les résolveurs d’entités utilisent la puissance des identificateurs d’entités préformés dans LUIS pour fournir plus de détails et de clarté à votre entité personnalisée.

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle

1. Sélectionnez **New Model**.
2. Entrez **Résolveurs d’entité** comme **Name**.
3. Sélectionnez **Créer**.

### <a name="create-a-pair-of-entities"></a>Créer une paire d’entités

1. Sélectionnez **Entities** dans le volet gauche, puis **New Entity**.
2. Entrez **departure** comme **Entity Name**.
3. Sélectionnez **datetimeV2** comme **Resolver Type**.
4. Sélectionnez **Créer**. Faîtes disparaître la fenêtre contextuelle d’information en sélectionnant **OK**.
5. Répétez les étapes 1 à 4 pour créer une deuxième entité nommée **retour** avec le type de résolveur **datetimeV2**.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Créer une paire d’Actions

1. Sélectionnez **Actions** dans le volet gauche, puis **New Action**.
2. Entrez **You are leaving on $departure and returning on $return** (Vous partez le $departure et revenez le $return) comme **Bot’s Response...** .
    - IMPORTANT : Quand vous tapez $[entityName], vous devez appuyer sur Entrée ou cliquer sur l’entité dans le menu déroulant, sinon Conversation Learner considère qu’il s’agit de texte plutôt que d’une entité.
    - Notez que le champ **Required Entities** recevra également ces Entités, qui ne peuvent pas être supprimées. Ainsi, cette action n’est disponible qu’en présence de ces deux entités requises.
3. Sélectionnez **Créer**.
4. Sélectionnez **New Action** pour créer une deuxième Action.
5. Entrez **When are you planning to travel?** (Quand comptez-vous voyager ?) comme  **Bot’s Response...** (Réponse du bot).
6. Entrez **departure** et **return** comme **Disqualifying Entities**. Cela ordonne à notre bot de NE PAS effectuer cette action si l’une de ces entités contient une valeur.
7. Sélectionnez **Créer**.

![](../media/T09_actions.png)

### <a name="training"></a>Formation

1. Attendez que **Training: [Status]** dans le coin supérieur gauche indique **Completed**.
    - Vous pouvez cliquer sur le lien « Refresh » (« Actualiser ») si cela est trop long.
    - L’état « Completed » (« Terminé ») est nécessaire pour la formation afin que les résolveurs d’entité fonctionnent lorsque nous formons le modèle.

2. Dans le volet gauche, cliquez sur « Train Dialogs » (« Boîtes de dialogue de formation «), puis sur le bouton « New Train Dialog » (« Nouvelle boîte de dialogue de formation »).
3. Tapez le premier énoncé utilisateur : « book me a flight » (« me réserver un billet d’avion »). 
4. Cliquez sur le bouton « Score Actions ».
5. Sélectionnez la réponse « When are you planning to travel? » (« Quand comptez-vous voyager ? »).
6. En tant qu’utilisateur, répondez par « leaving tomorrow and returning Sunday next week » (« pars demain et reviens dimanche la semaine prochaine »).
    - Remarquez la façon dont Conversation Learner a détecté deux Entités « Pre-Trained date » (« Date de pré-formation ») dans ce tour de rôle.
7. Dans le panneau « Entity Detection » (« Détection d’entité »), sélectionnez le texte « tomorrow » (« demain ») et identifiez-le comme « departure » (« départ »)
8. Indiquez également « Sunday next week » (« Dimanche prochain ») pour « return » (« retour »)
9. Cliquez sur le bouton « Score Actions ».
    - Notez que le volet « Memory » (« Mémoire ») contient vos dates de départ et de retour.
    - Placez le curseur sur chacun d’eux et observez comment les Entités sont des objets date qui capturent clairement la date calendaire réelle et non pas « Sunday » (« Dimanche ») ou « tomorrow » (« demain »).
10. Sélectionnez « You are leaving on... » (« Vous partez le... ») Réponse du bot.
11. Cliquez sur le bouton « Save » (« Enregistrer »).

![](../media/T09_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités d’énumération](./tutorial-enum-set-entity.md)
