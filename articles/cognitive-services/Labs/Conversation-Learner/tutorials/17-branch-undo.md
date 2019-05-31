---
title: Comment utiliser les opérations de création et de suppression de branche avec un modèle Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les opérations de création et de suppression de branche avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 6ffa0881df07e453c8beb175b8580deebbfc1ec9
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389895"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Comment utiliser les opérations de création et de suppression de branche
Dans ce didacticiel, nous allons aborder les opérations de création et de suppression de branche.


## <a name="details"></a>Détails
### <a name="undo"></a>Annuler
Permet au développeur d’« annuler » le dernier choix d’entrée ou d’action utilisateur. En arrière-plan, « annuler » crée en fait un nouveau dialogue et le rejoue jusqu’à l’étape précédente.  Cela signifie que le rappel de détection d’entité et les appels d’API dans la boîte de dialogue seront rappelés.

### <a name="branch"></a>Branche
Crée un nouveau dialogue d’apprentissage qui commence de la même façon qu’un dialogue d’apprentissage existant. Cela permet de supprimer les efforts de nouvelle saisie manuelle. En arrière-plan, « branche » crée un nouveau dialogue et répète le dialogue d’apprentissage existant jusqu’à l’étape sélectionnée.  Cela signifie que le rappel de détection d’entité et les appels d’API dans la boîte de dialogue seront rappelés.


## <a name="requirements"></a>Configuration requise
Ce didacticiel requiert que le bot qui prend les commandes de pizza soit en cours d’exécution :

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Ouvrir ou importer la démonstration

Si vous avez déjà travaillé dans le didacticiel de commande de pizza, ouvrez simplement ce modèle dans la liste présentée par l’interface utilisateur web. Sinon, vous devrez cliquer sur « Import Tutorials » (Importer des didacticiels) et sélectionnez le modèle nommé « Demo-PizzaOrder ».

## <a name="undo"></a>Annuler

Voici un exemple de la façon de voir la fonctionnalité `Undo` en action :

### <a name="training-dialogs"></a>Dialogues d’apprentissage
1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis cliquez sur le bouton `New Train Dialog`.
2. Tapez « Commander une pizza ».
3. Cliquez sur le bouton `Score Actions`.
4. Cliquez pour sélectionner « What would you like on your pizza » (que voulez-vous sur votre pizza ?)
5. Tapez « peu importe ».
6. Cliquez sur le bouton `Undo`.
    - La dernière entrée est supprimée, cédant la place à la dernière réponse du bot à la question « Que voulez-vous sur votre pizza ? »

## <a name="branch"></a>Branche

Pour cette démonstration, nous allons ouvrir un dialogue d’apprentissage existant et créer un nouveau dialogue d’apprentissage à partir du premier en créant une branche.

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage).
2. Observez la grille. Vous ne devriez voir qu’une seule formation commençant par « new order » (nouvelle commande).
3. Dans la grille, cliquez sur « new order » pour ouvrir le dialogue d’apprentissage existant.
4. Cliquez sur le dernier « no » dans la boîte de dialogue.
5. Cliquez sur l’icône « Branche », entourée en rouge dans cette image :
    - ![](../media/tutorial15_branch.PNG)
    - Le dialogue de formation entier avant le « non » est copié dans un nouveau dialogue de formation.
    - Cela vous évite de devoir entrer à nouveau les informations précédentes pour explorer une nouvelle « branche » de conversation à ce stade.
6. Tapez « yes », puis appuyez sur Entrée.
7. Cliquez sur le bouton `Score Actions`.
    - À ce stade le bot choisit automatiquement une réponse. Mais comme nous n’aimons pas la réponse, nous allons la modifier.
8. Cliquez sur la dernière réponse du bot.
    - Cela nous permet de sélectionner un autre réponse.
9. Sélectionnez « UseLastToppings ».
10. Cliquez sur le bouton `Score Actions`.
    - Le bot choisit de nouveau automatiquement une réponse. Il devrait dire, « Vous avez saucisson, du fromage et des champignons sur votre pizza. ». 
    - Cette fois, comme nous aimons la réponse, nous allons la conserver.
11. Cliquez sur le bouton `Score Actions`.
    - À nouveau, le bot choisit automatiquement une réponse. Il devrait dire : « Voulez-vous autre chose ? ».
12. Tapez « non ».
13. Cliquez sur le bouton `Save Branch`.
14. Notez que la grille comporte maintenant deux formations dont l’intitulé commence par « new order » (nouvelle commande).
    - L’un d’eux est celui que vous avez utilisé pour créer la branche.
    - Et l’autre est la version de branche que vous venez d’enregistrer.
    - Cliquez sur chacun d’eux pour vérifier si c’est correct.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Contrôle de version et marquage](./18-version-tag.md)
