---
title: Quand utiliser des entités d’ENUM et actions d’entité défini avec un modèle d’apprenant de Conversation - Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez quand il convient d’utiliser des entités d’ENUM et actions d’entité défini avec un modèle d’apprenant de Conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476493"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Quand utiliser les entités ENUM et actions d’entité défini

Ce didacticiel explique quand utiliser les entités ENUM (énumération) et les actions de SET_ENTITY.

## <a name="video"></a>Vidéo

[![Aperçu ensemble du didacticiel de l’entité](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Éléments

Ce didacticiel vous présente deux nouvelles fonctionnalités. Un nouveau type d’entité appelée ENUM (abréviation d’énumération) et un nouveau type d’action appelé SET_ENTITY, qui peut faire référence à une de ces valeurs enum, comme son nom l’indique, définit l’entité à cette valeur. Comme vous allez le voir ci-dessous, ces nouvelles fonctionnalités sont utilisées ensemble, et nous expliquerons ce qu’ils sont et comment les utiliser ci-dessous. Avant d’entrer dans les détails, il est important de comprendre quel problème ces fonctionnalités aident à résoudre.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problème

Il existe des cas où la signification des mots varie selon le contexte des conversations.  Normalement, les mots clés étiquetées sont apprises et extraites à l’aide d’un service de compréhension du langage, mais dans ce cas ces systèmes peuvent ne pas pouvoir pour en savoir plus à l’aide d’exemples étiquetés.

Imaginez que vous overhear partie d’une conversation entre personnes proches et que vous entendez uniquement le mot « Oui ». Vous sauriez pas ce que le « Yes » est accepté d’ou confirmé dans la mesure où vous n’avez pas entendu la question posée avant elle. La question posée avant est le contexte, qui donne une signification à la réponse. De même depuis « Oui » est telle une réponse courante aux nombreuses questions qu’il ne peut pas être appris en fournissant des exemples que vous le feriez avec [personnalisé formé](04-introduction-to-entities.md) entités car puis il serait Apprenez à étiqueter chaque « Oui » en tant que cette entité.

### <a name="example"></a>Exemples

Nous allons clarifier avec l’exemple suivant :

Bot : Aimez-vous Azure Cognitive Services ?
Utilisateur : Oui, Bot : Aimez-vous la crème glacée ?
Utilisateur : Oui

Dans les didacticiels précédents, nous avons vu [personnalisé formé](04-introduction-to-entities.md) votre pensée initiale et les entités peuvent consister à créer une entité nommée « likesCogServices » et d’étiqueter le premier « Oui » en tant que cette entité.  Toutefois, le système aurait également étiqueter le second « Oui ». Lorsque nous avons essayé de corriger l’étiquette de la deuxième « Oui » pour « likesIceCream », nous créerions ensuite un conflit de deux entrées mêmes « Oui » des choses différentes signification et allaient être bloqués.

Il est dans ce cas vous devez utiliser des entités d’ENUM et les actions de SET_ENTITY.

## <a name="when-to-use-enums-or-setentity-actions"></a>Quand utiliser des énumérations ou des actions de SET_ENTITY

Utilisez ces règles ci-dessous pour savoir quand utiliser des entités d’ENUM et les actions de SET_ENTITY :

- Paramètre d’entité ou de la détection est dépendant du contexte
- Nombre de valeurs possibles est fixe (Oui et ne serait deux valeurs)

En d’autres termes, utilisez-les pour toutes les invites s’est terminée de fermeture tels que les questions de confirmation toujours pour résultat Oui ou non.

> [!NOTE]
> Nous disposons actuellement de limitation des valeurs jusqu'à 5 par entité de l’enum. Chaque valeur utilise l’un des emplacements dans la limite de 64 actuelle. Consultez [cl valeurs et limites](../cl-values-and-boundaries.md)

Exemple : Bot : Votre commande est correct ?
Utilisateur : Oui

Lorsque les valeurs possibles de l’entité sont pas fixes et de durée indéterminée, vous devez utiliser une fonctionnalité alternative comme [attendu entité](05-expected-entity.md).

Exemple : Bot : Comment vous appelez-vous ?
Utilisateur : Matt Bot : Quel est votre couleur préférée ?
Utilisateur : Silver

Ces invites sont considérés comme très flexible, car ils ont pu répondu avec des valeurs arbitraires.

## <a name="what"></a>Quoi

### <a name="enum-entities"></a>Entités d’ENUM

Entités de l’ENUM sont créées comme les autres entités. Comme pour les entités de « programmation », vous ne pouvez pas l’étiquette mots ces entités. Au lieu de cela, ils doivent être définies via le code ou les actions SET_ENTITY.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Définir les actions d’entité

Comme mentionné ci-dessus, les actions « Entité définie » définissez simplement une entité sur une valeur enum connus. Vous pouvez obtenir les mêmes résultats en créant une action de rappel d’API et en utilisant le Gestionnaire de mémoire pour l’entité la valeur est une valeur. Par exemple, `memory.Set(entityName, entityValue)`. Avoir à écrire ce code et créer ces actions devient fastidieux et difficile à gérer - apprenant de Conversation a des actions spéciales afin de faciliter ce travail et de générer automatiquement ces actions lorsqu’ils sont utilisés. Lorsque ces actions indépendantes préserve la capacité à composer ces sans être couplé avec d’autres actions ou le code dans votre robot.

- Définir les actions d’entité peuvent uniquement être créées lorsque faisant référence à une valeur d’une entité de l’enum, donc vous devez d’abord créer une entité de l’enum.
- Définir les actions d’entité sont également « non-await » dans la mesure où ils aucune sortie visible et ont besoin d’être suivi par une action « wait » l’utilisateur peut voir.
- Définir les actions d’entité sont immuables, ce qui signifie que vous ne pouvez pas les modifier après sa création.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Génération d’une action automatique

Si une entité enum existe dans votre modèle, apprenant de Conversation crée espace réservé actions pour chacune des valeurs possibles et les rendre accessibles à sélectionner pendant la formation. Dès que vous sélectionnez, l’action est automatiquement créée pour vous.

Par exemple, si je crée une entité enum avec les valeurs « Yes » et « Non » :

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Même sans création explicite d’actions pour ce nouvel enum, vous verriez deux nouvelles actions disponibles au cours de formation :

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Créer un robot à l’aide de ces nouvelles fonctionnalités

### <a name="requirements"></a>Configuration requise

Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

Nous allons créer un robot pour simuler le classement de la restauration rapide. Elle aura des valeurs discrètes pour les tailles des boissons et hamburgers (petite/moyenne/grande) et les questions auxquelles la confirmation avec Oui / aucune des réponses. Ces deux entités satisfont aux deux règles ci-dessus d’être des réponses dépendant du contexte et des valeurs fixes.

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur « Importer »
2. Sélectionnez le didacticiel nommé « Didacticiel-Enum-Set-Entity »

Ceci vous permet d’accéder à la page de gestion de modèle.
Notez que le modèle contient déjà quelques entités enum et définir des actions de l’entité.

### <a name="create-the-first-dialog"></a>Créer le premier dialogue

1. Dans le volet de navigation de gauche, cliquez sur « Boîtes de dialogue Train », puis cliquez sur le bouton « Nouvelle boîte de dialogue Train ».
2. En tant que type d’utilisateur dans, « Bonjour, je souhaiterais commander un coke et hamburgers. ».
3. Cliquez sur le bouton « Score Actions »

   > L’utilisateur n’a pas encore spécifié les tailles pour les boissons ou hamburgers, nous devons lui demander.

4. Sélectionnez l’action de réponse : « Les boissons taille souhaitez-vous ? »
5. Comme type d’utilisateur dans, « large »
6. Cliquez sur le bouton « Score Actions »
7. Sélectionnez l’action SET_ENTITY - « drinkSize : LARGE »
8. Sélectionnez l’action de réponse : « Quelle taille hamburgers voulez-vous ? »
9. En tant que le type d’utilisation dans « messagerie unifiée, rendre ces un support.
10. Cliquez sur le bouton « Score Actions »
11. Sélectionnez l’action SET_ENTITY - « friesSize : MOYENNE »
12. Sélectionnez l’action de réponse : « Voulez-vous que les condiments ? »
13. Comme le type d’utilisation dans « Oui »
14. Cliquez sur le bouton « Score Actions »
15. Sélectionnez l’action SET_ENTITY - « condimentsConfirmation : OUI »
16. Sélectionnez l’action de réponse : « Ok, j’ai une commande pour une boisson grandes et moyennes hamburgers. Est-ce correct ? »
17. Comme le type d’utilisation dans « Oui »
18. Cliquez sur le bouton « Score Actions »
19. Sélectionnez l’action SET_ENTITY - « orderConfirmation : OUI »
20. Sélectionnez l’action de réponse : « Ok, votre numéro de commande est 58. Veuillez patienter là-bas. »
21. Cliquez sur « Enregistrer » pour fermer la boîte de dialogue

Vous venez de créer votre première boîte de dialogue à l’aide d’entités de l’ENUM et les actions de SET_ENTITY. Vous pouvez faire beaucoup plus de combinaisons de l’utilisateur des informations partielles ou de l’expérimentation avec d’autres types de questions s’est terminée de fermeture.

> [!NOTE]
> Au cours de formation vous verrez des espaces réservés pour les actions SET_ENTITY tel que
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> mais lorsque création de boîtes de dialogue ou à l’aide de déployé robots utilisateurs ne verront ces.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entrées alternatives](./10-alternative-inputs.md)
