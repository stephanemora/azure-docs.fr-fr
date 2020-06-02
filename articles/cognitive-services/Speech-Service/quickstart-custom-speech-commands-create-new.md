---
title: 'Démarrage rapide : Créer une commande personnalisée (préversion) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Cet article explique comment créer et tester une application de commandes personnalisées hébergée.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872525"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Démarrage rapide : Créer une application Commandes personnalisées (préversion)

Ce guide de démarrage rapide explique comment créer et tester une application Commandes personnalisées.
L'application créée traite des énoncés tels que « Allume la télévision », et répond par un message simple de type « D'accord, j'allume la télévision ».

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Créer une ressource Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Pour le moment, le service Commandes personnalisées prend uniquement en charge les abonnements Speech dans les régions westus, westus2 et neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Accédez à Speech Studio pour les commandes personnalisées

1. Ouvrez votre navigateur web et accédez à [Speech Studio](https://speech.microsoft.com/)
1. Entrez vos informations d’identification pour vous connecter au portail.

   - L'affichage par défaut est votre liste d'abonnements Speech.
     > [!NOTE]
     > Si vous ne voyez pas la page Sélectionner un abonnement, vous pouvez y accéder en choisissant « Ressources Speech » dans le menu Paramètres sur la barre supérieure.

1. Sélectionnez votre abonnement Speech, puis **Accéder à Studio**.
1. Sélectionnez **Commandes personnalisées**.

     - L'affichage par défaut présente liste des applications Commandes personnalisées disponibles sous l'abonnement sélectionné.

## <a name="create-a-custom-commands-project"></a>Créer un projet Commandes personnalisées

1. Sélectionnez **Nouveau projet** pour créer un projet.

   > [!div class="mx-imgBorder"]
   > ![Créer un projet](media/custom-speech-commands/create-new-project.png)

1. Entrez le nom du projet.
1. Sélectionnez une langue dans la liste déroulante.
1. Sélectionnez une ressource de création dans la liste déroulante. En l'absence de ressources de création valides, créez-en une en cliquant sur **Créer une ressource de création LUIS**.

   > [!div class="mx-imgBorder"]
   > ![Créer un groupe de ressources](media/custom-speech-commands/create-new-resource.png)

   - Entrez le nom de la ressource et le groupe de ressources.
   - Choisissez la valeur correspondant à l'emplacement et au niveau tarifaire dans la liste déroulante.

      > [!NOTE]
      > Vous pouvez créer des groupes de ressources en entrant le nom du groupe de ressources souhaité dans le champ « Groupe de ressources ». Sélectionnez **Créer** pour créer le groupe de ressources.

1. Puis sélectionnez **Créer** pour créer le projet.
1. Une fois votre projet créé, sélectionnez-le.

    - Votre affichage doit à présent présenter un aperçu de l'application Commandes personnalisées que vous venez de créer.

## <a name="update-luis-resources-optional"></a>Mettre à jour les ressources LUIS (facultatif)

Vous pouvez mettre à jour la ressource de création définie dans la fenêtre du nouveau projet, puis définir une ressource de prédiction. La ressource de prédiction est utilisée pour la reconnaissance une fois votre application Commandes personnalisées publiée. Aucune ressource de prédiction n'est nécessaire pour les phases de développement et de test.

1. Sélectionnez **Paramètres** dans le volet gauche, puis accédez à la section **Ressources LUIS** dans le volet central.
1. Sélectionnez une ressource de prédiction, ou créez-en une en sélectionnant **Créer une ressource**.
1. Sélectionnez **Enregistrer**.
    
    > [!div class="mx-imgBorder"]
    > ![Définir les ressources LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Comme la ressource de création ne prend en charge que 1 000 requêtes de point de terminaison de prédiction par mois, vous devez obligatoirement définir une ressource de prédiction LUIS avant de publier votre application Commandes personnalisées.


## <a name="create-a-new-command"></a>Créer une commande

Nous allons créer une commande simple qui ne traitera qu'un seul énoncé, `turn on the tv`, puis répondra avec le message `Ok, turning on the tv`.

1. Créez une nouvelle commande en sélectionnant l'icône `+ New command` disponible en haut du volet le plus à gauche. Une nouvelle fenêtre contextuelle intitulée **Nouvelle commande** apparaît.
1. Indiquez la valeur suivante pour le champ **Nom** : `TurnOn`.
1. Sélectionnez **Create** (Créer).

Le volet central inscrit les différentes propriétés d'une commande :


| Configuration            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exemples de phrases | Exemples d’énoncés que l’utilisateur peut prononcer pour déclencher la commande                                                                 |
| Paramètres       | Informations requises pour exécuter la commande                                                                                |
| Règles d'exécution | Actions à effectuer pour exécuter la commande. Par exemple, répondre à l'utilisateur ou communiquer avec un autre service web. |
| Règles d'interaction   | Règles supplémentaires pour gérer des situations plus spécifiques ou complexes                                                              |


> [!div class="mx-imgBorder"]
> ![Créer une commande](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Ajouter des exemples de phrases

Nous allons commencer par la section Exemples de phrases, puis nous fournirons un exemple de ce que l'utilisateur peut dire.

1. Sélectionnez la section **Exemples de phrases** dans le volet central et, dans le volet le plus à droite, ajoutez des exemples :

    ```
    turn on the tv
    ```

1. Sélectionnez l'icône `Save` présente en haut de ce volet.

Pour le moment, comme nous n'avons aucun paramètre. Nous pouvons donc passer à la section **Règles d'exécution**.

### <a name="add-a-completion-rule"></a>Ajouter une règle d'exécution

Ajoutez maintenant une règle d'exécution dotée de la configuration suivante. Cette règle indique à l'utilisateur qu'une action d'exécution est en cours.


| Paramètre    | Valeur suggérée                          | Description                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nom de la règle  | ConfirmationResponse                  | Nom décrivant l’objectif de la règle          |
| Conditions | None                                     | Conditions déterminant le moment où la règle peut s’exécuter    |
| Actions    | SpeechResponse : « D'accord, j'allume la télévision » | Action à exécuter lorsque la condition de la règle est remplie |

1. Pour créer une nouvelle règle d'exécution, sélectionnez l'icône `+Add` en haut du volet central.
1. Indiquez la valeur dans la section **Nom**.
1. Ajouter une action
   1. Créez une nouvelle action en sélectionnant **+ Ajouter une action** dans la section **Actions**.
   1. Dans la fenêtre contextuelle **Nouvelle action**, sélectionnez `Send speech response` dans les options de la liste déroulante **Type**.
   1. Choisissez `Simple editor` pour le champ **Réponse**.
       - Dans le champ **Première variante**, entrez la valeur suivante pour la réponse : `Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Créer une ressource Speech](media/custom-speech-commands/create-speech-response-action.png)

1. Cliquez sur **Enregistrer** pour enregistrer la règle.
1. Revenez à la section **Règles d'exécution** et sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées. 

> [!div class="mx-imgBorder"]
> ![Créer une règle d’exécution](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Faites un essai

Testez le comportement à l'aide du volet de conversation Tester.
1. Sélectionnez l'icône `Train` en haut du volet droit.
1. Une fois l'apprentissage terminé, sélectionnez `Test`. Une nouvelle fenêtre **Tester votre application** apparaît.
    - Entrez : Allume la télévision
    - La réponse attendue est : D'accord, j'allume la télévision


> [!div class="mx-imgBorder"]
> ![Tester avec Web Chat](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)
