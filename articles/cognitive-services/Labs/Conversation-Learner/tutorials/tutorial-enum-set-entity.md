---
title: Quand utiliser des entités ENUM et des actions SET_ENTITY avec un modèle Conversation Learner ? - Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez quand il convient d’utiliser des entités ENUM et des actions SET_ENTITY avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476493"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quand utiliser des entités ENUM et des actions SET_ENTITY ?

Ce tutoriel explique quand utiliser des entités ENUM (énumération) et des actions SET_ENTITY.

## <a name="video"></a>Vidéo

[![Tutoriel sur SET_ENTITY (préversion)](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Éléments

Ce tutoriel présente deux nouvelles fonctionnalités. Un nouveau type d’entité appelée ENUM (abréviation d’énumération) et un nouveau type d’action appelée SET_ENTITY, qui peut faire référence à l’une de ces valeurs d’énumération et comme son nom l’indique, affecte cette valeur à l’entité. Comme vous allez le voir ci-dessous, ces nouvelles fonctionnalités sont utilisées ensemble. Nous allons expliquer ce qu’elles sont et comment les utiliser. Avant d’entrer dans les détails, il est important de comprendre quel problème ces fonctionnalités aident à résoudre.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problème

Il existe des cas où la signification des mots varie en fonction du contexte de la conversation.  Normalement, les mots clés étiquetés sont appris et extraits à l’aide d’un service Language Understanding, mais dans ces cas-là ces systèmes risquent de ne pas pouvoir apprendre à l’aide d’exemples étiquetés.

Imaginez que vous entendez une partie d’une conversation entre plusieurs personnes, et que vous entendez uniquement le mot « Oui ». Vous ne saurez pas ce que le mot « Oui » confirme ou accepte, puisque vous n’avez pas entendu la question posée. Cette question posée juste avant est le contexte, qui donne une signification à la réponse. De plus, le mot « Oui » étant une réponse tellement courante à de nombreuses questions, il ne peut pas être appris en fournissant des exemples comme vous le feriez avec des entités [entraînées personnalisées](04-introduction-to-entities.md), car alors il apprendrait à étiqueter chaque « Oui » en tant que cette entité.

### <a name="example"></a>Exemples

Nous allons clarifier tout ceci avec l’exemple suivant :

Bot : Aimez-vous Azure Cognitive Services ?
Utilisateur : Oui Bot : Aimez-vous la crème glacée ?
Utilisateur : Oui

Dans les tutoriels précédents, nous avons examiné les entités [entraînées personnalisées](04-introduction-to-entities.md), et votre idée initiale pourrait consister à créer une entité nommée « likesCogServices » et à étiqueter le premier « Oui » en tant que cette entité.  Toutefois, le système étiquetterait également le second « Oui ». Quand nous tenterions de corriger l’étiquette du deuxième « Oui » sur « likesIceCream », nous créerions alors un conflit de deux mêmes entrées « Oui » signifiant différentes choses et nous serions bloqués.

C’est dans ce cas-là que vous devez utiliser des entités ENUM et des actions SET_ENTITY.

## <a name="when-to-use-enums-or-set_entity-actions"></a>Quand utiliser des ENUM ou des actions SET_ENTITY ?

Utilisez les règles ci-dessous pour savoir quand utiliser des entités ENUM et des actions SET_ENTITY :

- La détection ou définition d’entité dépend du contexte
- Le nombre de valeurs possibles est fixe (Oui et Non constitueraient deux valeurs)

En d’autres termes, utilisez-les pour toutes les invites « fermées », telles que les questions demandant confirmation et entraînant toujours la réponse Oui ou Non.

> [!NOTE]
> Nous avons actuellement une limitation de cinq valeurs par entité ENUM. Chaque valeur utilise l’un des emplacements dans la limite actuelle de 64. Voir [cl-values-and-boundaries](../cl-values-and-boundaries.md).

Exemple : Bot : Votre commande est-elle correcte ?
Utilisateur : Oui

Quand les valeurs possibles de l’entité sont ouvertes et non fixes, vous devez utiliser une autre caractéristique telle que l’[entité attendue](05-expected-entity.md).

Exemple : Bot : Comment vous appelez-vous ?
Utilisateur : Matt Bot : Quelle est votre couleur préférée ?
Utilisateur : Argent

Ces invites sont considérées comme ouvertes, car vous pouvez y répondre par des valeurs arbitraires.

## <a name="what"></a>Quoi

### <a name="enum-entities"></a>Entités ENUM

La procédure de création des entités ENUM est la même que pour les autres entités. Comme pour les entités de « programmation », vous ne pouvez pas étiqueter des mots en tant que ces entités. Au lieu de cela, vous devez les définir par le biais de code ou d’actions SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Actions de définition d’entité

Comme mentionné plus haut, les actions SET_ENTITY affectent simplement une valeur d’énumération connue à une entité. Vous pouvez obtenir les mêmes résultats en créant une action de rappel d’API et en utilisant le gestionnaire de mémoire pour affecter une valeur à l’entité. Par exemple, `memory.Set(entityName, entityValue)`. Devoir écrire ce code et créer ces actions deviendrait rapidement fastidieux et difficile à gérer. Conversation Learner propose des actions spéciales pour faciliter ce travail et générer automatiquement ces actions quand elles sont utilisées. Le fait de disposer de ces actions en tant qu’actions indépendantes vous permet de les composer sans qu’elles soient couplées à d’autres actions ou à du code dans votre bot.

- Les actions de définition d’entité peuvent uniquement être créées quand elles font référence à une valeur d’une entité ENUM ; vous devez donc d’abord créer une entité ENUM.
- Les actions de définition d’entité sont également « non-await », dans la mesure où elles n’ont aucune sortie visible et doivent être suivies d’une action « wait » visible par l’utilisateur.
- Les actions de définition d’entité sont immuables, ce qui signifie que vous ne pouvez pas les modifier après leur création.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Génération automatique d’action

Si une entité ENUM existe dans votre modèle, Conversation Learner crée des espaces réservés d’actions pour chacune des valeurs possibles et les rend sélectionnables pendant l’entraînement. Lors de la sélection, l’action est créée automatiquement pour vous.

Par exemple, si je crée une entité ENUM avec les valeurs « Yes » et « No » :

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Même sans créer de manière explicite des actions pour cette nouvelle entité ENUM, vous verriez deux nouvelles actions disponibles au cours de l’entraînement :

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Créer un bot à l’aide de ces nouvelles fonctionnalités

### <a name="requirements"></a>Configuration requise

Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

Nous allons créer un bot pour simuler les commandes dans un établissement de restauration rapide. Il aura des valeurs discrètes pour les tailles des boissons et des frites (SMALL/MEDIUM/LARGE), et des questions de confirmation avec des réponses YES / NO. Ces deux entités satisfont aux deux règles mentionnées plus haut ; il s’agit de réponses dépendant du contexte et elles ont des valeurs fixes.

### <a name="create-the-model"></a>Créer le modèle

1. Dans l’interface utilisateur web, cliquez sur « Importer ».
2. Sélectionnez le tutoriel nommé « Tutorial-Enum-Set-Entity ».

Vous accéderez à la page de gestion de modèle.
Notez que le modèle contient déjà quelques entités ENUM et des actions de définition d’entité.

### <a name="create-the-first-dialog"></a>Créer le premier dialogue

1. Dans le volet de navigation gauche, cliquez sur « Train Dialogs » (« Boîtes de dialogue d’entraînement »), puis sur le bouton « New Train Dialog » (« Nouvelle boîte de dialogue d’entraînement »).
2. Dans le rôle de l’utilisateur, tapez « Hi, I would like to order a coke and fries please. »
3. Cliquez sur le bouton « Noter les actions ».

   > L’utilisateur n’a pas encore spécifié de taille pour la boisson ou les frites. Nous devons les lui demander.

4. Sélectionnez l’action avec réponse : « What size drink would you like ? »
5. Dans le rôle de l’utilisateur, tapez « large ».
6. Cliquez sur le bouton « Noter les actions ».
7. Sélectionnez l’action SET_ENTITY - "drinkSize: LARGE"
8. Sélectionnez l’action avec réponse : « What size fries would you like ? »
9. Dans le rôle de l’utilisateur, tapez « Um, make those a medium ».
10. Cliquez sur le bouton « Noter les actions ».
11. Sélectionnez l’action SET_ENTITY - "friesSize: MEDIUM"
12. Sélectionnez l’action avec réponse : « Would you like any condiments? »
13. Dans le rôle de l’utilisateur, tapez « Yes ».
14. Cliquez sur le bouton « Noter les actions ».
15. Sélectionnez l’action SET_ENTITY - "condimentsConfirmation: YES"
16. Sélectionnez l’action avec réponse : « Ok, I have an order for a LARGE drink and MEDIUM fries. Is that correct? »
17. Dans le rôle de l’utilisateur, tapez « Yes ».
18. Cliquez sur le bouton « Noter les actions ».
19. Sélectionnez l’action SET_ENTITY - "orderConfirmation: YES"
20. Sélectionnez l’action avec réponse : « Ok, your order number is 58. Please wait over there. »
21. Cliquez sur « Enregistrer » pour fermer la boîte de dialogue.

Vous venez de créer votre première boîte de dialogue à l’aide d’entités ENUM et d’actions SET_ENTITY. Vous pouvez créer beaucoup plus de combinaisons avec l’utilisateur spécifiant des informations partielles, ou expérimenter avec d’autres types de questions fermées.

> [!NOTE]
> Au cours de l’entraînement, vous verrez des espaces réservés pour les actions SET_ENTITY, tels que
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> mais lors de la création des boîtes de dialogue ou de l’utilisation de bots déployés, les utilisateurs ne les verront pas.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entrées alternatives](./10-alternative-inputs.md)
