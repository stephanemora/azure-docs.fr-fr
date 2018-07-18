---
title: Comment utiliser les opérations de création et de suppression de branche avec une application Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les opérations de création et de suppression de branche avec une application Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369609"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Comment utiliser les opérations de création et de suppression de branche
Dans ce didacticiel, nous allons aborder les opérations de création et de suppression de branche.

## <a name="details"></a>Détails
- Annuler : permet au développeur d’« annuler » un choix d’entrée ou d’action utilisateur. En arrière-plan, « annuler » crée en fait une nouvelle boîte de dialogue et la répète jusqu’à l’étape précédente.  Cela signifie que le rappel de détection d’entité et les appels d’API dans la boîte de dialogue seront rappelés.

- Créer une branche : crée une nouvelle boîte de dialogue d’apprentissage qui commence de la même façon qu’une boîte de dialogue d’apprentissage existante. Cela permet de supprimer les efforts de nouvelle saisie manuelle. En arrière-plan, « créer une branche » crée une nouvelle boîte de dialogue et répète la boîte de dialogue d’apprentissage existante jusqu’à l’étape sélectionnée.  Cela signifie que le rappel de détection d’entité et les appels d’API dans la boîte de dialogue seront rappelés.


## <a name="requirements"></a>Configuration requise
Ce didacticiel requiert l’exécution du robot de réinitialisation de mot de passe :

    npm run demo-pizza

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des applications de l’interface utilisateur web, cliquez sur TutorialDemo Pizza Order. 

Pour plus d’informations sur la démonstration de commande de pizza, consultez le didacticiel de commande de pizza.

## <a name="undo"></a>Annuler

Nous allons annuler une partie de la boîte de dialogue et la recréer à partir de cette étape.

### <a name="training-dialogs"></a>Dialogues d’apprentissage
Ouvrons une session d’apprentissage. 

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
1. Entrez « order a pizza » (commander une pizza).
2. Cliquez sur Action de score.
3. Cliquez pour sélectionner « what would you like on your pizza » (que voulez-vous sur votre pizza ?)
4. Entrez « champignons et fromage ».
5. Cliquez sur Attribuer un score aux actions.
3. Cliquez pour sélectionner « you have $Toppings on your pizza » (vous avez $Toppings sur votre pizza).
6. Sélectionnez « Would you like anything else? » (Voulez-vous autre chose ?)
7. Entrez « remove mushrooms and add peppers » (supprimer les champignons et ajouter des poivrons).
    - Sélectionnez «mushrooms » (champignons) et désélectionnez l’entité « Toppings » (Garnitures). Nous allons créer une action qui nous permettra d’annuler.
2. Cliquez sur Undo Step (Annuler une étape).
    - Notez que la dernière entrée est supprimée, et nous revenons à « Would you like anything else? » (Voulez-vous autre chose ?).  (capture d’écran ci-dessous)
2. Entrez « remove mushrooms and add peppers » (supprimer les champignons et ajouter des poivrons).
8. Cliquez pour sélectionner « you have $Toppings on your pizza » (vous avez $Toppings sur votre pizza)
    - Assurez-vous que les deux entités sont correctement sélectionnées.
2. Cliquez sur Action de score. Vous pouvez maintenant continuer avec la boîte de dialogue corrigée.
4. Cliquez sur Apprentissage terminé.

Vous savez maintenant comment utiliser l’annulation pour supprimer une entrée et une action utilisateur.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Branche

Par exemple, ouvrons une boîte de dialogue d’apprentissage existante et créons une autre boîte de dialogue d’apprentissage par création de branche.

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur « new order » (nouvelle commande) pour ouvrir la boîte de dialogue existant. 
2. Cliquez sur le dernier « no » (non) dans la boîte de dialogue (voir la capture d’écran ci-dessous).
3. Cliquez sur Branche.
    - Notez que « no » (non) est supprimé, et la boîte de dialogue entière à ce stade est copiée dans une nouvelle. 
    - Vous ne devez donc pas entrer à nouveau les informations précédentes pour observer une nouvelle « branche » à ce stade.
1. Entrez « yes » (oui).
2. Cliquez sur Action de score.
3. Sélectionnez « you have $Toppings on your pizza » (vous avez $Toppings sur votre pizza).
6. Sélectionnez « Would you like anything else? » (Voulez-vous autre chose ?)
7. Entrez « no » (non).
4. Cliquez sur Apprentissage terminé.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Contrôle de version et marquage](./16-versioning-and-tagging.md)
