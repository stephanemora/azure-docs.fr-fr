---
title: Comment utiliser des entités qui peuvent être niées avec un modèle d’Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser des entités qui peuvent être niées avec un modèle d’Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: df6499410da3ae67715ade29dbc3cc4146fc2265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231989"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Utiliser des entités qui peuvent être niées avec un modèle d’Apprenant de conversation

Ce didacticiel illustre la propriété « negatable » des entités.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 5](https://aka.ms/cl-tutorial-05-preview)](https://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Marquez une action comme « pouvant être niée » si l’utilisateur peut « effacer » une valeur d’entité, comme dans « Non, je ne souhaite pas $entity » ou « non, pas $entity ». Par exemple, « non, je ne pas partir de Boston ».

Concrètement, si la propriété « negatable » d’une entité est définie :

- Lors de l’étiquetage de mentions d’entité, ceci vous permet d’étiqueter les instances normales (positives) d’une entité et les instances « négatives » de l’entité
- LUIS apprend deux modèles d’entité : un pour les instances positives et un autre pour les instances négatives
- L’effet d’une instance négative d’une entité consiste à effacer cette valeur de la variable d’entité (si elle existe)

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Nom, entrez NegatableEntity. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans Nom de l’entité, entrez le nom.
3. Vérifiez ce qui peut être nié.
    - Cette propriété indique que l’utilisateur pourra fournir une valeur pour l’entité, ou que quelque chose n’est *pas* la valeur de l’entité. Dans le dernier cas, cela entraîne la suppression d’une valeur correspondante de l’entité.
3. Cliquez sur Créer.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle action
2. Dans Réponse, tapez « Je ne connais pas votre nom ».
3. Dans Disqualifying Entities (Entités disqualifiantes), entrez un nom.
3. Cliquez sur Create.

Ensuite, créez la deuxième action.

1. Cliquez sur Actions, puis sur Nouvelle Action pour en créer une deuxième.
3. Dans Réponse, tapez « Je connais votre nom. Il s’agit de $name ».
4. Cliquez sur Create.

Vous avez maintenant deux actions.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Score Actions (Attribuer un score aux actions), puis sélectionnez « Je ne connais pas votre nom »
    - Le score est de 100 % car c’est la seule action valide.
2. Entrez « mon nom est david »
3. Sélectionnez « david » et choisissez l’étiquette « + nom »
    - Il existe deux instances de « nom » : « + nom » et « -nom ».  (+) Signe plus ajoute ou remplace la valeur. (-) Signe moins supprime la valeur.
5. Cliquez sur Attribuer un score aux actions
    - La valeur du nom figure maintenant dans la mémoire du bot.
    - « Je connais votre nom. « $name » et c’est la seule réponse possible. 
6. Sélectionnez « Je connais votre nom. Il s’agit de $name ».

Essayons d’effacer l’entité pouvant être niée :

7. Entrez « mon nom n’est pas david ».
    - Notez que « non » est sélectionné comme nom sur la base du modèle précédent. Cette étiquette est incorrecte.
2. Cliquez sur « non », puis sur le x rouge. 
3. Cliquez sur « david ».
    - Il s’agit désormais d’une entité négative indiquant qu’il ne s’agit pas de la valeur de l’entité du nom.
2. Sélectionnez «- nom ».
3. Cliquez sur Attribuer un score aux actions.
    - Notez que la valeur a été effacée de la mémoire.
2. Sélectionnez « Je ne connais pas votre nom », qui est la seule action.

Nous montrons ensuite comment une nouvelle valeur pour le nom peut être entrée.

3. Entrez « john » comme nom. Sélectionnez ensuite « john », puis cliquez sur le nom.
4. Cliquez sur Attribuer un score aux actions.
5. Sélectionnez « Je connais votre nom. Il s’agit de $name ».

Essayez maintenant de remplacer le nom entré.

6. Entrez « mon nom est susan ».
7. Sélectionnez « Je connais votre nom. Il s’agit de $name ».
7. Cliquez sur Attribuer un score aux actions.
8. Notez que **susan** a remplacé **john** dans les valeurs d’entité.
9. Entrez « mon nom n’est pas susan ».
    - Notez que le système a étiqueté cela comme une instance négative.
2. Cliquez sur Attribuer un score aux actions.
3. Sélectionnez « Je ne connais pas votre nom », qui est la seule action.
7. Cliquez sur Done Teaching (Apprentissage terminé).

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entités à valeurs multiples](./6-multi-value-entities.md)
