---
title: Résolveurs d’entité dans un modèle d’apprenant de Conversation - Microsoft Cognitive Services | Microsoft Docs
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
ms.openlocfilehash: 7b5f5645a8b5d1d993f87d18503fe270f42c5de5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390023"
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

1. Sélectionnez **nouveau modèle**.
2. Entrez **résolveurs d’entité** pour **nom**.
3. Sélectionnez **Créer**.

### <a name="create-a-pair-of-entities"></a>Créer une paire d’entités

1. Sélectionnez **entités** dans le volet gauche, puis **nouvelle entité**.
2. Entrez **départ** pour **nom de l’entité**.
3. Sélectionnez **datetimeV2** pour **Type de programme de résolution**.
4. Sélectionnez **Créer**. Faire disparaître la fenêtre contextuelle d’information en sélectionnant **OK**.
5. Répétez les étapes 1 à 4 pour créer une deuxième entité nommée **retourner** avec **datetimeV2** type de programme de résolution.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Créer une paire d’Actions

1. Sélectionnez **Actions** dans le volet gauche, puis **nouvelle Action**.
2. Entrez **vous sont en laissant sur $departure et en retournant sur $return** pour **réponse de robot...** .
    - IMPORTANT - lors de la saisie dans $[entityName], vous devez effectuer un positionnement Entrez ou cliquez sur l’entité dans la liste déroulante sinon apprenant de Conversation est considérez ce texte au lieu d’une entité.
    - Notez que le **entités requises** champ recevront également ces entités et ils ne peuvent pas être supprimés. Ainsi, cette action n’est disponible qu’en présence de ces deux entités requises.
3. Sélectionnez **Créer**.
4. Sélectionnez **nouvelle Action** pour créer une deuxième Action.
5. Entrez **lorsque vous envisagez de voyage ?** pour **réponse de robot...** .
6. Entrez **départ** et **retourner** pour **entités de cette manière**. Cela ordonne à notre bot de NE PAS effectuer cette action si l’une de ces entités contient une valeur.
7. Sélectionnez **Créer**.

![](../media/T09_actions.png)

### <a name="training"></a>Formation

1. Regardez la **formation : [Status]** dans le coin supérieur gauche pour **terminé**.
    - Vous pouvez cliquer sur le lien « Refresh » (« Actualiser ») si cela est trop long.
    - L’état « Completed » (« Terminé ») est nécessaire pour la formation afin que les résolveurs d’entité fonctionnent lorsque nous formons le modèle.

2. Dans le volet gauche, cliquez sur « Train Dialogs » (« Boîtes de dialogue de formation «), puis sur le bouton « New Train Dialog » (« Nouvelle boîte de dialogue de formation »).
3. Tapez le premier énoncé utilisateur : « book me a flight » (« me réserver un billet d’avion »). 
4. Cliquez sur le bouton « Score Actions ».
5. Sélectionnez la réponse « When are you planning to travel? » (« Quand comptez-vous voyager ? »).
6. En tant qu’utilisateur, répondez par « leaving tomorrow and returning Sunday next week » (« pars demain et reviens dimanche la semaine prochaine »).
    - Remarquez la façon dont Conversation Learner a détecté deux Entités « Pre-Trained date » (« Date de pré-formation ») dans ce tour de rôle.
7. Dans le panneau « Entity Detection » (« Détection d’entité », sélectionnez le texte « tomorrow » (« demain ») et identifiez-le comme « departure » (« départ »)
8. Indiquez également « Sunday next week » (« Dimanche prochain ») pour « return » (« retour »)
9. Cliquez sur le bouton « Score Actions ».
    - Notez que le volet « Memory » (« Mémoire ») contient vos dates de départ et de retour.
    - Placez le curseur sur chacun d’eux et observez comment les entités sont des objets de date qui capturent clairement la date de calendrier actuelle par opposition à « Dimanche » ou « tomorrow ».
10. Sélectionnez « You are leaving on... » (« Vous partez le... ») Réponse du bot.
11. Cliquez sur le bouton « Save » (« Enregistrer »).

![](../media/T09_training.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entrées alternatives](./10-alternative-inputs.md)
