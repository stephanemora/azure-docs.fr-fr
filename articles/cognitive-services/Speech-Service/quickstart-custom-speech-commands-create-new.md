---
title: 'Démarrage rapide : Créer une application Commandes personnalisées (préversion) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Cet article explique comment créer et tester une application Commandes personnalisées (préversion) hébergée. Cette application traite des énoncés.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 5b54923bb667053ffc886b335ab9d1b0cf4cb754
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509353"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Démarrage rapide : Créer une application Commandes personnalisées (préversion)

Ce guide de démarrage rapide explique comment créer et tester une application Commandes personnalisées.
L’application traite des énoncés tels que « Allume la télévision », et répond par un message simple tel que « D’accord, j’allume la télévision ».

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Créer une ressource Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Pour le moment, le service Commandes personnalisées prend uniquement en charge les abonnements Speech dans les régions westus, westus2 et neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Accédez à Speech Studio pour les commandes personnalisées

1. Dans un navigateur web, accédez à [Speech Studio](https://speech.microsoft.com/).
1. Entrez vos informations d’identification pour vous connecter au portail.

   L'affichage par défaut est votre liste d'abonnements Speech.
    > [!NOTE]
    > Si vous ne voyez pas la page Sélectionner un abonnement, vous pouvez y accéder en sélectionnant **Ressources Speech** dans le menu Paramètres en haut de l’écran.

1. Sélectionnez votre abonnement Speech, puis **Accéder à Studio**.
1. Sélectionnez **Commandes personnalisées**.

     L’affichage par défaut présente la liste des applications Commandes personnalisées disponibles sous l’abonnement sélectionné.

## <a name="create-a-custom-commands-project"></a>Créer un projet Commandes personnalisées

1. Sélectionnez **Nouveau projet** pour créer un projet.

   > [!div class="mx-imgBorder"]
   > ![Créer un projet](media/custom-speech-commands/create-new-project.png)

1. Dans la zone **Nom**, entrez un nom de projet.
1. Dans la liste **Langue**, sélectionnez une langue.
1. Dans la liste **Ressource de création LUIS**, sélectionnez une ressource de création. En l’absence de ressources de création valides, créez-en une en sélectionnant **Créer une ressource de création LUIS**.

   > [!div class="mx-imgBorder"]
   > ![Créer un groupe de ressources](media/custom-speech-commands/create-new-resource.png)

   1. Dans la zone **Nom de la ressource**, entrez le nom de la ressource.
   1. Dans la liste **Groupe de ressources**, sélectionnez un groupe de ressources.
   1. Dans la liste **Localisation**, sélectionnez une localisation.
   1. Dans la liste **Niveau tarifaire**, sélectionnez un niveau.

      > [!NOTE]
      > Vous pouvez créer un groupe de ressources en entrant le nom du groupe de ressources dans la zone **Groupe de ressources**. Sélectionnez **Créer** pour créer le groupe de ressources.

1. Sélectionnez **Create** (Créer).
1. Une fois que le projet est créé, sélectionnez-le.

    Vous devez à présent voir une vue d’ensemble de votre nouvelle application Commandes personnalisées.

## <a name="update-luis-resources-optional"></a>Mettre à jour les ressources LUIS (facultatif)

Vous pouvez mettre à jour la ressource de création que vous avez sélectionnée dans la fenêtre **Nouveau projet**, puis définir une ressource de prédiction. La ressource de prédiction est utilisée pour la reconnaissance quand votre application Commandes personnalisées est publiée. Aucune ressource de prédiction n’est nécessaire pendant les phases de développement et de test.

1. Sélectionnez **Paramètres** dans le volet gauche, puis sélectionnez **Ressources LUIS** dans le volet central.
1. Sélectionnez une ressource de prédiction, ou créez-en une en sélectionnant **Créer une ressource**.
1. Sélectionnez **Enregistrer**.
    
    > [!div class="mx-imgBorder"]
    > ![Définir les ressources LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Comme la ressource de création ne prend en charge que 1 000 requêtes de point de terminaison de prédiction par mois, vous devez définir une ressource de prédiction LUIS avant de publier votre application Commandes personnalisées.


## <a name="create-a-command"></a>Créer une commande

Nous allons créer une commande simple qui ne traitera qu'un seul énoncé, `turn on the tv`, puis répondra avec le message `Ok, turning on the tv`.

1. Créez une commande en sélectionnant **Nouvelle commande** en haut du volet gauche. La fenêtre **Nouvelle commande** s’ouvre.
1. Dans la zone **Nom**, entrez **TurnOn**.
1. Sélectionnez **Create** (Créer).

Le volet central liste les propriétés de la commande :


| Configuration            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Exemples de phrases** | Exemples d’énoncés que l’utilisateur peut prononcer pour déclencher la commande.                                                                 |
| **Paramètres**       | Informations requises pour exécuter la commande.                                                                                |
| **Règles d’exécution** | Actions effectuées pour exécuter la commande. Par exemple, répondre à l’utilisateur ou communiquer avec un autre service web. |
| **Règles d’interaction**   | Règles supplémentaires pour gérer des situations plus spécifiques ou complexes.                                                              |


> [!div class="mx-imgBorder"]
> ![Créer une commande](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Ajouter des exemples de phrases

Commençons par la section **Exemple de phrases**. Nous fournirons un exemple de ce que l’utilisateur peut prononcer.

1. Sélectionnez **Exemples de phrases** dans le volet central. 
1. Dans le volet droit, ajoutez des exemples :

    ```
    turn on the tv
    ```

1. En haut du volet, sélectionnez **Enregistrer**.

Pour le moment, nous n’avons pas de paramètres ; nous pouvons donc passer à la section **Règles d’exécution**.

### <a name="add-a-completion-rule"></a>Ajouter une règle d’exécution

Ajoutez à présent une règle d’exécution dotée de la configuration suivante. Cette règle indique à l’utilisateur qu’une action d’exécution est en cours.


| Paramètre    | Valeur suggérée                          | Description                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Nom**  | **RéponseConfirmation**                  | Nom décrivant l’objectif de la règle.          |
| **Conditions** | None                                     | Conditions d’exécution de la règle.    |
| **Actions**    | **Envoyer une réponse vocale-> D’accord, j’allume la télévision** | Action à exécuter quand la condition de la règle est remplie. |

1. Pour créer une règle d’exécution, sélectionnez **Ajouter** en haut du volet central.
1. Dans la zone **Nom** , entrez un nom.
1. Ajoutez une action.
   1. Créez une action en sélectionnant **Ajouter une action** dans la section **Actions**.
   1. Dans la fenêtre **Modifier une action**, dans la liste **Type**, sélectionnez **Envoyer une réponse vocale**.
   1. Sous **Réponse**, sélectionnez **Éditeur simple**. Dans la zone **Première variante**, entrez **D’accord, j’allume la télévision**.

   > [!div class="mx-imgBorder"]
   > ![Créer une réponse](media/custom-speech-commands/create-speech-response-action.png)

1. Sélectionnez **Enregistrer** pour enregistrer la règle.
1. Revenez à la section **Règles d'exécution** et sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées. 

> [!div class="mx-imgBorder"]
> ![Créer une règle d’exécution](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Faites un essai

Testez le comportement à l’aide du volet de conversation de test.
1. Sélectionnez **Entraîner** en haut du volet droit.
1. Une fois l’entraînement terminé, sélectionnez **Tester**. Une nouvelle fenêtre **Tester votre application** apparaît.
    - Entrez **Allume la télévision**.
    - La réponse attendue est : **D’accord, j’allume la télévision**


> [!div class="mx-imgBorder"]
> ![Tester le comportement](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une application Commandes personnalisées (préversion) avec des paramètres](./quickstart-custom-speech-commands-create-parameters.md)
