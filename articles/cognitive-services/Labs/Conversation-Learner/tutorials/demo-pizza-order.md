---
title: Modèle Conversation Learner de démonstration, commande de pizza – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Conversation Learner de démonstration.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703424"
---
# <a name="demo-pizza-order"></a>Démonstration : commande de pizza
Cette démonstration illustre un bot de commande de pizza capable de prendre en charge une commande à la fois en :

- reconnaissant les ingrédients dans les énoncés des utilisateurs ;
- déterminant si les ingrédients sont en stock ou non, et en réagissant en conséquence ;
- mémorisant les commandes précédentes et en traitant plus rapidement toute nouvelle commande de pizza identique.

## <a name="video"></a>Vidéo

[![Aperçu de démonstration Pizza](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot de commande de pizza soit en cours d’exécution

    npm run demo-pizza

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles de l’interface utilisateur web, cliquez sur TutorialDemo Pizza Order. 

## <a name="entities"></a>Entités

Le modèle contient trois entités :

- « Garnitures » accumule les garnitures demandées par l'utilisateur, si elles sont disponibles.
- « EnRuptureDeStock » indique à l'utilisateur que la garniture sélectionnée est en rupture de stock.
- « DernièresGarnitures » contient l'historique des garnitures de la commande précédente de l'utilisateur.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Actions

Le modèle contient un ensemble d'actions : demander à l'utilisateur ce qu'il veut sur sa pizza, lui indiquer ce qu'il a ajouté jusqu'ici, etc.

Deux appels d'API sont également fournis :

- « FinaliserCommande » permet de passer la commande
- « UtiliserDernièresGarnitures » permet de traiter l'historique des garnitures

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogues d’apprentissage

Le modèle inclut plusieurs dialogues d'apprentissage.

![](../media/tutorial_pizza_dialogs.PNG)

Entamons l'apprentissage du modèle en créant un nouveau dialogue d'apprentissage.

1. Dans le volet gauche, cliquez sur « Dialogues d'apprentissage », puis sur le bouton « Nouveau dialogue d'apprentissage ».
2. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Commander une pizza au fromage ».
    - Le mot « fromage » a été extrait par l'extracteur d'entité.
3. Cliquez sur le bouton « Noter les actions ».
4. Sélectionnez la réponse « Vous avez du fromage sur votre pizza ».
5. Sélectionnez la réponse « Voulez-vous autre chose ? ».
6. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Ajouter des champignons et du poivre ».
7. Cliquez sur le bouton « Noter les actions ».
8. Sélectionnez la réponse « Vous avez du fromage, des champignons et du poivre sur votre pizza ».
9. Sélectionnez la réponse « Voulez-vous autre chose ? ».
10. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Supprimer le poivre et ajouter de la saucisse ».
11. Cliquez sur le bouton « Noter les actions ».
12. Sélectionnez la réponse « Vous avez du fromage, des champignons et de la saucisse sur votre pizza. »
13. Sélectionnez la réponse « Voulez-vous autre chose ? ».
14. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Ajouter de l'igname ».
15. Cliquez sur le bouton « Noter les actions ».
    - La valeur « igname » a été transférée vers l'entité « EnRuptureDeStock » par le code de rappel de détection d'entité car le texte ne correspond à aucun ingrédient pris en charge.
16. Sélectionnez la réponse « EnRuptureDeStock ».
17. Sélectionnez la réponse « Voulez-vous autre chose ? ».
18. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Non ».
    - Le « non » ne fait pas partie des types d'intention disponibles. Nous choisirons plutôt l'action appropriée en fonction du contexte.
19. Cliquez sur le bouton « Noter les actions ».
20. Sélectionnez la réponse « FinaliserCommande ».
    - La sélection de cette action a pour effet de sauvegarder les garnitures actuelles du client dans l'entité « DernièresGarnitures », et l'entité « Garnitures » est supprimée par le code de rappel « FinaliserCommande ».
21. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Nouvelle commande ».
22. Cliquez sur le bouton « Noter les actions ».
23. Sélectionnez la réponse « Voulez-vous du fromage, des champignons et de la saucisse ? ».
    - Cette action est désormais disponible car l'entité « DernièresGarnitures » est définie.
24. Dans le panneau de conversation, sous « Entrez votre message... », tapez « Oui ».
25. Cliquez sur le bouton « Noter les actions ».
26. Sélectionnez la réponse « UtiliserDernièresGarnitures ».
27. Sélectionnez la réponse « Vous avez du fromage, des champignons et de la saucisse sur votre pizza. »
28. Sélectionnez la réponse « Voulez-vous autre chose ? ».

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement d’un bot Conversation Learner](../deploy-to-bf.md)
