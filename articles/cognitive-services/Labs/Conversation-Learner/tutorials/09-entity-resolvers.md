---
title: Résolveurs d’entité dans un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les résolveurs d’entité avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cc1f9ea18d425679a4db9c7b91a1c0a3c4451d4b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796533"
---
# <a name="entity-resolvers"></a>Résolveurs d’entité

Ce tutoriel montre comment utiliser les résolveurs d’entité avec un modèle Conversation Learner

## <a name="video"></a>Vidéo

[![Tutoriel sur les résolveurs d’entité en préversion](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

- Le type de résolveur est une propriété facultative d’Entités personnalisées.
- Les résolveurs d’entités utilisent la puissance des identificateurs d’entités préformés dans LUIS pour fournir plus de détails et de clarté à votre entité personnalisée.

## <a name="steps"></a>Étapes

### <a name="create-a-new-model"></a>Créer un modèle

1. Dans l’interface utilisateur web, cliquez sur « New Model » (« Nouveau modèle »).
2. Dans le champ « Name » (Nom), tapez « Entity Resolvers » (Résolveurs d’entité), puis appuyez sur Entrée ou cliquez sur le bouton « Create » (Créer).

### <a name="create-a-pair-of-entities"></a>Créer une paire d’entités

1. Dans le volet gauche, cliquez sur « Entities » (« Entités »), puis sur le bouton « New Entity » (« Nouvelle entité »).
2. Dans le champ « Entity Name » (« Nom d’entité »), tapez « departure » (« départ »).
3. Dans la liste déroulante « Resolver Type » («Type de résolveur »), sélectionnez « datetimeV2 ».
4. Cliquez sur le bouton « Create » (« Créer »).
5. Après avoir lu la fenêtre contextuelle d’informations, cliquez sur le bouton « OK ».
6. En procédant de la même façon, créez une autre Entité nommée « return » (« retour ») qui possède également un résolveur de type « datetimeV2 ».

### <a name="create-a-pair-of-actions"></a>Créer une paire d’Actions

1. Dans le volet gauche, cliquez sur « Actions », puis cliquez sur le bouton « Nouvelle Action ».
2. Dans le champ « Bot’s response » (« Réponse du bot »), tapez « You are leaving on $departure and returning on $return » (« Vous partez le $departure et revenez le $return »).
    - IMPORTANT - Lorsque vous tapez « $[entityName] », vous devez appuyer sur Entrée ou cliquer sur l’entité dans le menu déroulant, sinon Conversation Learner considère qu’il s’agit de texte au lieu d’une entité.
    - Notez que le champ « Required Entities » (« Entités requises ») recevra également ces Entités, qui ne peuvent pas être supprimées. Ainsi, cette action n’est disponible qu’en présence de ces deux entités requises.
3. Cliquez sur le bouton « Create » (« Créer »).
4. Cliquez sur le bouton « New action » (« Nouvelle action ») une nouvelle fois pour créer une deuxième action.
5. Dans le champ « Bot’s response » (« Réponse du bot »), tapez « When are you planning to travel? » (« Quand comptez-vous voyager ? »).
6. Dans le champ « Disqualifying Entities » (Entités disqualifiantes), tapez « departure » (« départ ») et « return » (« retour »).
    - Cela ordonne à notre bot de NE PAS effectuer cette action si l’une de ces entités contient une valeur.
7. Cliquez sur le bouton « Create » (« Créer »).


### <a name="training"></a>Formation

1. Suivez « Training: [Status] » dans la partie supérieure gauche de la page jusqu’à ce qu’elle soit à l’état « Completed ».
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
    - Placez le curseur sur chacun d’eux et observez comment les Entités sont des objets date qui capturent clairement la date calendaire réelle et non pas « Sunday » (« Dimanche ») ou « tomorrow » (« demain »).
10. Sélectionnez « You are leaving on... » (« Vous partez le... ») Réponse du bot.
11. Cliquez sur le bouton « Save » (« Enregistrer »).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entrées alternatives](./10-alternative-inputs.md)
