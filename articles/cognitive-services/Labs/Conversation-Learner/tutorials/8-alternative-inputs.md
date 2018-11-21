---
title: Guide pratique pour utiliser les entrées de remplacement avec Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les entrées de remplacement avec Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f344424c372f65cd8bfe5779cc65f0e866e58a11
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633969"
---
# <a name="how-to-use-alternative-inputs"></a>Guide pratique pour utiliser les entrées de remplacement

Ce tutoriel montre comment utiliser le champ « Entrées de remplacement » de l’entrée de l’utilisateur dans l’interface d’apprentissage.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 8](https://aka.ms/cl-tutorial-08-preview)](https://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Les « entrées de remplacement » sont des énoncés que l’utilisateur aurait pu dire à un moment donné au cours d’un dialogue d’apprentissage. Elles permettent de spécifier une manière plus compacte les variantes des propos de l’utilisateur, sans avoir à lister chacune d’entre elles dans un dialogue d’apprentissage distinct.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Nom, entrez AlternativeInputs. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans la partie correspondant au nom de l’entité, entrez la ville.
3. Cliquez sur Créer.

### <a name="create-three-actions"></a>Créer trois actions

1. Cliquez sur Actions, puis sur Nouvelle action.
2. Dans Réponse, tapez « Quelle ville souhaitez-vous ? ».
3. Dans Entités disqualifiantes, entrez $city.
3. Cliquez sur Create.

Ensuite, créez la deuxième action :

1. Cliquez sur Actions, puis sur Nouvelle action.
3. Dans Réponse, tapez « Il fait probablement beau à $city ».
4. Dans Entités requises, entrez $city.
4. Cliquez sur Créer.

Créez la troisième action :

1. Cliquez sur Actions, puis sur Nouvelle action.
3. Dans Réponse, tapez « Essayez de demander la météo ».
    - Ce serait la réponse à une question de l’utilisateur du type « Que peut faire le système ? ».
4. Dans Entités disqualifiantes, entrez $city.
4. Cliquez sur Create.

Vous avez maintenant trois actions.

### <a name="train-the-bot"></a>Entraîner le bot

1. Cliquez sur Dialogues d’apprentissage, puis sur Nouveau dialogue d’apprentissage.
2. Tapez « Quel temps fait-il ? ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville souhaitez-vous ? ».
2. Entrez « Denver ».
3. Double-cliquez sur « Denver » et sélectionnez « ville ».
    - Seattle est marqué comme entité de type « ville ».
5. Cliquez sur Attribuer un score aux actions
    - denver est maintenant présente dans l’entité ville. 
6. Sélectionnez « Il fait probablement beau à $city ».
7. Cliquez sur Apprentissage terminé.

Ajoutez un autre exemple de dialogue :

1. Cliquez sur Nouvelle Action, puis sur Nouveau dialogue d’apprentissage.
2. Tapez « Que pouvez-vous faire ? ».
3. Cliquez sur Attribuer un score aux actions, puis sélectionnez « Essayez de demander la météo ».
2. Tapez « Quel temps fait-il à Seattle ? ».
3. Double-cliquez sur « Seattle » et sélectionnez « ville ».
    - Seattle est marqué comme entité de type « ville ».
5. Cliquez sur Attribuer un score aux actions
    - seattle est maintenant présente dans l’entité ville. 
6. Sélectionnez « Il fait probablement beau à $city ».
7. Cliquez sur Apprentissage terminé.

Voyons ce qui se passe si le propos de l’utilisateur est similaire d’un point de vue sémantique à l’énoncé ci-dessus :

1. Cliquez sur Nouvelle Action, puis sur Nouveau dialogue d’apprentissage.
2. Tapez « Aide ».
3. Cliquez sur Score Actions (Actions de score).
    - Les scores des deux réponses potentielles sont très proches. ce qui indique que le modèle ne connaît pas la limite entre les deux actions.
6. Cliquez sur Abandonner l’apprentissage et Confirmer.

![](../media/tutorial8_closescores.png)

Dans ce cas, il serait utile d’ajouter des entrées de remplacement aux dialogues. Vous pouvez les ajouter au cours de l’apprentissage. Vous pouvez également revenir les insérer ultérieurement.

2. Cliquez sur « Que pouvez-vous faire ? » dans Entraîner les dialogues.
2. Dans le dialogue, cliquez sur « Que pouvez-vous faire ? » pour le sélectionner.
    1. Dans le volet droit, entrez deux autres phrases dans Ajouter une entrée de remplacement sous Détection des entités :
    1. Entrez « Quels sont les choix possibles ? ».
    2. Entrez « Dites-moi quels sont les choix possibles ».
    3. Entrez « Aide ».
    1. Cliquez sur Envoyer les modifications.


![](../media/tutorial8_helpalternates.png)

2. Cliquez maintenant sur « Quel temps fait-il à Seattle ? ».
    1. Dans Ajouter une entrée de remplacement, entrez « météo de Seattle ».
    2. Double-cliquez sur « Seattle » et sélectionnez « ville ». Les entités des entrées de remplacement doivent avoir le même jeu d’entités et que celles-ci doivent être présentes. Si le contenu des entités est différent, cela ne pose pas de problème.
    3. Dans Ajouter une entrée de remplacement, entrez « Va-t-il pleuvoir aujourd'hui à Denver ? ».
    4. Cliquez sur « Denver » et sélectionnez « ville ».
    5. Cliquez sur Envoyer les modifications et Terminé.


Ajoutons des entrées de remplacement au premier dialogue :

1. Cliquez sur Entraîner les dialogues.
2. Cliquez sur le dialogue qui commence par « Quel temps fait-il ».
2. Cliquez pour sélectionner « Quel temps fait-il » dans le volet gauche :
    1. Dans Ajouter une entrée de remplacement, entrez « prévisions météo ».
    2. Entrez « Va-t-il pleuvoir ? ».
    3. Cliquez sur Envoyer les modifications.
4. Cliquez pour sélectionner « Denver » dans le volet gauche :
    1. Dans Ajouter une entrée de remplacement, entrez « de Seattle ».
    1. Entrez « météo d’Austin ».
        - Un message d’erreur s’affiche : « Les variations de texte doivent contenir les mêmes entités détectées que le texte d’entrée d’origine. »
        - Cliquez sur le mot « austin ».
        - Une fenêtre contextuelle s’affiche pour vous permettre de sélectionner l’entité pour « austin ». Cliquez sur **Ville**.
        - Cliquez sur Envoyer les modifications.
    1. Cliquez sur Terminé pour entraîner à nouveau le modèle.

![](../media/tutorial8_altcities.png)

Essayons les variantes :

1. Cliquez sur Nouveau dialogue d’apprentissage.
2. Tapez « Quelles sont vos fonctionnalités ? ».
3. Cliquez sur Score Actions (Actions de score).
    - Les scores sont maintenant plus déterminants pour l’action suivante, qui indique le degré de certitude du modèle.
2. Sélectionnez « Essayez de demander la météo ».
6. Cliquez sur Apprentissage terminé.

Vous avez maintenant vu comment utiliser les entrées de remplacement pour indiquer d’autres choses que l’utilisateur aurait pu dire. Elles dispensent de créer de nombreux dialogues identiques à différents égards, en les réduisant à un seul dialogue et en énumérant les énoncés possibles de l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Consigner les dialogues](./9-log-dialogs.md)
