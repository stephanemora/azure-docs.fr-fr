---
title: Didacticiel de création d’une application LUIS pour extraire des données - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment créer une application LUIS simple utilisant une entité simple et des intentions pour extraire les données issues de l’apprentissage automatique.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265358"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Didacticiel : Créer une application utilisant une entité simple
Dans ce didacticiel, vous allez créer une application qui montre comment extraire les données issues de l’apprentissage automatique à partir d’un énoncé utilisant l’entité **Simple**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendre les entités simples 
> * Créer une application LUIS pour le domaine de la communication avec l’intention SendMessage
> * Ajouter l’intention _None_ ainsi que des exemples d’énoncés
> * Ajouter une entité simple pour extraire le contenu d’un message à partir d’un énoncé
> * Effectuer l’apprentissage de l’application et la publier
> * Interroger un point de terminaison de l’application pour voir la réponse JSON de LUIS

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="purpose-of-the-app"></a>Objet de l’application
Cette application montre comment extraire des données d’un énoncé. Par exemple, examinez l’énoncé suivant provenant d’un chatbot :

```JSON
Send a message telling them to stop
```

L’intention est d’envoyer un message. Les données importantes de l’énoncé sont le message proprement dit, `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Objet de l’entité simple
L’objet de l’entité simple est d’apprendre à LUIS ce qu’est un message et où il se trouve dans un énoncé. La partie de l’énoncé correspondant au message peut changer d’un énoncé à l’autre en fonction du choix des mots et de la longueur de l’énoncé. LUIS a besoin d’exemples de messages dans tous les énoncés de toutes les intentions.  

Pour cette application simple, le message se trouve à la fin de l’énoncé. 

## <a name="create-a-new-app"></a>Créer une application
1. Connectez-vous au site web [LUIS][LUIS]. Veillez à vous connecter à la région où vous avez besoin de points de terminaison LUIS publiés.

2. Sur le site web [LUIS][LUIS], sélectionnez **Créer une application**.  

    ![Liste des applications LUIS](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. Dans la boîte de dialogue contextuelle, entrez le nom `MyCommunicator`. 

    ![Liste des applications LUIS](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. À l’issue de ce processus, l’application affiche la page **Intents** (Intentions) avec l’intention **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Capture d’écran de la page Intents (Intentions) de LUIS avec l’intention None")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Créer une intention

1. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention). 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Capture d’écran de LUIS avec le bouton Create new intent (Créer une intention) mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Entrez le nom de la nouvelle intention `SendMessage`. Cette intention doit être sélectionnée à chaque fois qu’un utilisateur souhaite envoyer un message.

    En créant une intention, vous créez la catégorie principale d’informations que vous souhaitez identifier. Attribuer un nom à la catégorie permet à toute autre application utilisant les résultats de la requête LUIS d’utiliser ce nom de catégorie pour trouver une réponse appropriée ou agir de façon adéquate. LUIS ne répond pas à ces questions, et identifie uniquement le type d’informations demandé dans un langage naturel. 

    ![Entrer le nom d’intention SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Ajoutez sept énoncés à l’intention `SendMessage` qu’un utilisateur est susceptible de demander selon vous :

    | Exemples d’énoncés|
    |--|
    |Répondre : « j’ai reçu votre message, vous aurez ma réponse demain »|
    |Envoyer le message : « quand seras-tu à la maison ? »|
    |Texte : « je suis occupé »|
    |Leur dire que ce doit être terminé aujourd’hui|
    |Indiquer par messagerie instantanée que je conduis et que je répondrai plus tard|
    |Composer un message pour David indiquant : « c’était quand ? »|
    |Dire bonjour à Greg|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Capture d’écran de LUIS affichant les énoncés entrés")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Ajouter des énoncés à l’intention None

Pour le moment, l’application LUIS ne dispose d’aucun énoncé pour l’intention **None**. Comme elle a besoin d’énoncés auxquels vous ne souhaitez pas que l’application réponde, elle doit disposer d’énoncés dans l’intention **None**. Renseignez cette zone. 
    
1. Dans le panneau gauche, sélectionnez **Intents** (Intentions). 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Capture d’écran de LUIS avec le bouton Intents (Intentions) mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Sélectionnez l’intention **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Capture d’écran de sélection de l’intention None")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Ajoutez trois énoncés que votre utilisateur est susceptible d’entrer, mais qui ne sont pas pertinents pour votre application. Énoncés appropriés pour l’intention **None** :

    | Exemples d’énoncés|
    |--|
    |Annuler|
    |Au revoir|
    |Que se passe-t-il ?|
    
    Dans votre application d’appel de LUIS, comme un chatbot, si LUIS retourne l’intention **None** pour un énoncé, votre robot peut demander si l’utilisateur souhaite mettre fin à la conversation. Le robot peut également donner d’autres directions pour poursuivre la conversation si l’utilisateur ne souhaite pas y mettre un terme. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Capture d’écran de LUIS affichant les énoncés correspondant à l’intention None")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Créer une entité simple pour extraire un message 
1. Dans le menu gauche, sélectionnez **Intents** (Intentions).

    ![Sélectionner le lien Intents (Intentions)](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. Dans la liste des intentions, sélectionnez `SendMessage`.

    ![Sélectionner l’intention SendMessage](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. Dans l’énoncé `Reply with I got your message, I will have the answer tomorrow`, sélectionnez le premier mot du corps du message, `I`, ainsi le dernier mot du corps du message, `tomorrow`. Tous ces mots sont sélectionnés pour le message, et un menu déroulant s’affiche avec une zone de texte située dans la partie supérieure de l’écran.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Capture d’écran de la sélection de mots dans un énoncé à utiliser comme message")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Dans la zone de texte, entrez le nom de l’entité `Message`.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Capture d’écran de la saisie d’un nom d’entité dans la zone")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. Dans le menu déroulant, sélectionnez **Créer une entité**. L’objet de l’entité est d’extraire le texte qui constitue le corps du message. Dans cette application LUIS, le message est situé à la fin de l’énoncé, mais ce dernier peut être de n’importe quelle longueur, tout comme le message. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Capture d’écran de création d’une entité à partir d’un énoncé")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. Dans la fenêtre contextuelle, le type d’entité par défaut est **Simple**, et le nom de l’entité est `Message`. Conservez ces paramètres et sélectionnez **Terminé**.

    ![Vérifier le type d’entité](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Maintenant que l’entité est créée et qu’un énoncé est étiqueté, étiquetez le reste des énoncés pourvus de cette entité. Sélectionnez un énoncé, puis les premier et dernier mots d’un message. Dans le menu déroulant, sélectionnez l’entité `Message`. Le message est maintenant étiqueté dans l’entité. Continuez à étiqueter toutes les expressions de message dans les énoncés restants.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Capture d’écran de l’ensemble des énoncés de message étiquetés")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    La vue par défaut des énoncés est **Entities view** (Vue des entités). Sélectionnez le contrôle **Entities view** (Vue des entités) au-dessus des énoncés. La **Tokens view** (Vue des jetons) affiche le texte de l’énoncé. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Capture d’écran des énoncés figurant dans la Tokens view (Vue des jetons)")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>Effectuer l’apprentissage de l’application LUIS
LUIS ne connaît pas les modifications apportées aux intentions et aux entités (modèle) tant que son apprentissage n’a pas été effectué. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Effectuer l’apprentissage**.

    ![Sélectionner le bouton Effectuer l’apprentissage](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. L’apprentissage est terminé lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

    ![Notification de réussite de l’apprentissage](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publier l’application pour obtenir l’URL de point de terminaison
Pour obtenir une prédiction LUIS dans un chatbot ou une autre application, vous devez publier l’application. 

1. En haut à droite du site web LUIS, sélectionnez le bouton **Publier**. 

2. Sélectionnez l’emplacement Production et le bouton **Publier**.

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Capture d’écran de la page Publier avec le bouton Publier vers l’emplacement Production mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. La publication est terminée lorsque la barre d’état verte s’affiche en haut du site web, confirmant ainsi sa réussite.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Interroger le point de terminaison avec un autre énoncé
Dans la page **Publier**, sélectionnez le lien **Point de terminaison** en bas de la page. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Capture d’écran de la page Publier avec le point de terminaison mis en surbrillance")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Cette action ouvre une autre fenêtre de navigateur avec l’URL de point de terminaison affichée dans la barre d’adresses. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `text I'm driving and will be 30 minutes late to the meeting`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. Comme cet énoncé est différent des énoncés étiquetés, c’est un bon test qui doit retourner les énoncés `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Quel est l’accomplissement de cette application LUIS ?
Cette application, comptant seulement deux intentions et une entité, a identifié une intention de type requête en langage naturel et retourné les données de message. 

Le résultat JSON identifie l’intention `SendMessage` à notation supérieure dont le score est égal à 0,987501. Tous les scores sont compris entre 1 et 0, le meilleur score étant proche de 1. Le score de l’intention `None` est égal à 0,111048922, beaucoup plus proche de zéro. 

Les données de message ont un type `Message`, ainsi qu’une valeur, `i ' m driving and will be 30 minutes late to the meeting`. 

Votre chatbot possède maintenant suffisamment d’informations pour déterminer l’action principale, `SendMessage`, et un paramètre de cette action, le texte du message. 

## <a name="where-is-this-luis-data-used"></a>Où ces données LUIS sont-elles utilisées ? 
LUIS en a fini avec cette demande. L’application d’appel, par exemple un chatbot, peut prendre le résultat topScoringIntent et les données de l’entité pour envoyer le message via une API tierce. S’il existe d’autres options de programmation pour le robot ou l’application d’appel, LUIS n’effectue pas ce travail. LUIS détermine uniquement l’intention de l’utilisateur. 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour ce faire, sélectionnez le menu représentant trois points (...) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrir comment ajouter une entité hiérarchique](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
