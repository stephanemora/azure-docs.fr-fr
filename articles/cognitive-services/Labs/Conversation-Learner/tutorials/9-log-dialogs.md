---
title: Guide pratique pour consigner les dialogues dans une application Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment consigner les dialogues dans une application Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369664"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>Guide pratique pour consigner les dialogues dans une application Conversation Learner

Ce tutoriel montre comment effectuer des tests de type utilisateur final dans l’interface Conversation Learner, comment consigner les dialogues et comment apporter des corrections aux dialogues consignés afin d’améliorer le modèle.

## <a name="requirements"></a>Configuration requise
Pour les besoins de ce tutoriel, le bot général des tutoriels doit être en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails
Les dialogues consignés peuvent servir à vérifier et à apporter des corrections aux dialogues menés avec les utilisateurs finaux.  Plus précisément, vous pouvez corriger les étiquettes des entités et les sélections d’actions pour améliorer les performances du modèle entraîné et du système global. 

## <a name="steps"></a>Étapes

### <a name="create-the-application"></a>Création de l'application

1. Dans l’interface utilisateur web, cliquez sur Nouvelle application.
2. Dans Nom, entrez LogDialogs. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Dans Nom de l’entité, entrez « city ».
3. Cliquez sur Créer.

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle action.
2. Dans Réponse, tapez « Quelle ville ? ».
3. Dans Entités disqualifiantes, entrez $city.
3. Click Create

Ensuite, créez la deuxième action :

1. Cliquez sur Actions, puis sur Nouvelle action.
3. Dans Réponse, tapez « Il fait probablement beau à $city ».
4. Dans Entités requises, entrez $city.
4. Cliquez sur Créer.

Vous avez maintenant deux actions.

### <a name="train-the-bot"></a>Entraîner le bot

1. Cliquez sur Dialogues d’apprentissage, puis sur Nouveau dialogue d’apprentissage.
2. Tapez « Quel temps fait-il ? ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle ville ? ».
2. Entrez « Seattle ».
3. Double-cliquez sur « Seattle » et sélectionnez « ville ».
    - Seattle est marqué comme entité de type « ville ».
5. Cliquez sur Attribuer un score aux actions.
6. Sélectionnez « Il fait probablement beau à $city ».
7. Cliquez sur Apprentissage terminé.

Ajoutez un autre exemple de dialogue :

1. Cliquez sur Nouvelle Action, puis sur Nouveau dialogue d’apprentissage.
2. Tapez « Quel temps fait-il à Seattle ? ». Remarquez au passage que Seattle est marqué comme entité.
5. Cliquez sur Attribuer un score aux actions. 
6. Sélectionnez « Il fait probablement beau à $city ».
7. Cliquez sur Apprentissage terminé.

### <a name="try-the-bot-as-the-user"></a>Essayer le bot en tant qu’utilisateur
Imaginons que nous avons déployé ce bot auprès des utilisateurs.

1. Cliquez sur Consigner les dialogues.
2. Cliquez sur Nouvelle session de conversation.
    - Cette action présente le bot comme le verrait l’utilisateur dans le contrôle de la discussion web sur la gauche de l’interface utilisateur. Vous pouvez ignorer la zone avec les espaces blancs sur la droite.
3. Tapez « Bonjour ».
4. Réponse du bot : « Quelle ville ? »
4. Tapez « Boston ».
5. Réponse du bot : « Quelle ville ? »
    - Cela ne va pas. Nous allons donc enregistrer ce dialogue.
2. Cliquez sur Tests terminés.

Lançons une nouvelle session :

2. Cliquez sur Nouvelle session de conversation.
3. Tapez « météo de Boston ».
4. Réponse du bot : « Quelle ville ? »
2. Cliquez sur Apprentissage terminé.

Nous allons maintenant apporter des corrections au deuxième dialogue :

1. Cliquez sur « météo de Boston » sous Consigner les dialogues.
    - La conversation s’ouvre.
    - Si vous cliquez du côté de l’utilisateur dans la conversation (ici, sur « météo de Boston »), vous pouvez modifier les étiquettes des entités.
    - Si vous cliquez du côté du système (ici, sur « Quelle ville ? »), vous pouvez sélectionner une autre action.
5. Cliquez sur « météo de Boston ». 
    - La cause racine est que Boston n’était pas marqué comme entité. Il faut y remédier.
    - Double-cliquez sur « Boston », puis sélectionnez « ville ».
    - Cliquez sur Envoyer les modifications, puis sur Enregistrer. Cette action crée un dialogue d’apprentissage à partir des modifications que vous avez apportées et l’ouvre à l’endroit où a été effectué le changement.
6. Sélectionnez « Il fait probablement beau à $city ».
7. Cliquez sur Apprentissage terminé. Si vous ouvrez maintenant Entraîner les dialogues, vous verrez que la nouvelle action a été ajoutée.

![](../media/tutorial9_logdiag1.PNG)

Nous allons maintenant apporter des corrections à l’autre dialogue :

1. Cliquez sur « Bonjour » sous Consigner les dialogues.
    - La conversation s’ouvre.
3. Notez que la réponse à « Bonjour » est « Quelle ville ? ». Nous voulons la remplacer par une réponse plus pertinente, comme « Bonjour, je suis le bot météo ». Mais il n’existe aucune action de ce type ; il nous faut donc en créer une.
4. Cliquez sur Action.
    - Dans Réponse, tapez « Je suis le bot météo. Je peux vous donner les prévisions. »
6. Décochez la case Attendre la réponse pour que ce ne soit pas une action d’attente.
7. Cliquez sur Créer.
8. Puis cliquez sur Sélectionner cette nouvelle action. Cliquez ensuite sur Enregistrer.
    - Cela vous ramène à cet endroit dans la session d’apprentissage.
6. Cliquez pour sélectionner « Quelle ville ? ».
7. Tapez « Boston ». Double-cliquez pour marquer Boston comme entité si ce n’est pas déjà fait.
8. Cliquez sur Attribuer un score aux actions.
9. Cliquez pour sélectionner « Il fait probablement beau à $city ».
10. Cliquez sur Apprentissage terminé.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rappel de la détection d’entités](./10-entity-detection-callback.md)
