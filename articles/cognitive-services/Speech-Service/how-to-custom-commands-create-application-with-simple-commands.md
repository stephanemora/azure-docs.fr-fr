---
title: 'Procédure : Créer une application avec des commandes simples - service Speech'
titleSuffix: Azure Cognitive Services
description: Cet article décrit comment créer et tester une application Commandes personnalisées hébergée en utilisant des commandes simples.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d166257dd28773d89a4f1fd56de3cb1a22242523
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284164"
---
# <a name="create-application-with-simple-commands"></a>Créer une application avec des commandes simples

Dans cet article, vous apprendrez comment :
 - Créer une application vide
 - Mettre à jour des ressources LUIS
 - Ajouter des commandes de base à votre application Commandes personnalisées

## <a name="create-empty-application"></a>Créer une application vide
Créez une application Commandes personnalisées vide. Pour plus d’informations, reportez-vous au [démarrage rapide](quickstart-custom-commands-application.md). Cette fois, au lieu d’importer un projet, vous créez un projet vide.

1. Dans la zone **Nom**, entrez le nom de projet `Smart-Room-Lite` (ou le nom de votre choix).
1. Dans la liste **Langue**, sélectionnez **Français - France**.
1. Sélectionnez ou créez une ressource LUIS de votre choix.

   > [!div class="mx-imgBorder"]
   > ![Créer un projet](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Mettre à jour les ressources LUIS (facultatif)

Vous pouvez mettre à jour la ressource de création que vous avez sélectionnée dans la fenêtre **Nouveau projet**, puis définir une ressource de prédiction. La ressource de prédiction est utilisée pour la reconnaissance quand votre application Commandes personnalisées est publiée. Aucune ressource de prédiction n’est nécessaire pendant les phases de développement et de test.

## <a name="add-turnon-command"></a>Ajouter une commande TurnOn

Dans l’application Commandes personnalisées **Smart-Room-Lite** que vous venez de créer, ajoutez une commande simple qui traite un énoncé, `turn on the tv`, et répond avec le message `Ok, turning the tv on`.

1. Créez une commande en sélectionnant **Nouvelle commande** en haut du volet gauche. La fenêtre **Nouvelle commande** s’ouvre.
1. Pour le champ **Nom**, indiquez la valeur **TurnOn**.
1. Sélectionnez **Create** (Créer).

Le volet central répertorie les différentes propriétés de la commande. Vous configurez les propriétés suivantes de la commande. Pour plus d’explications sur toutes les propriétés de configuration d’une commande, accédez aux [références](./custom-commands-references.md).

| Configuration            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Exemples de phrases** | Exemples d’énoncés que l’utilisateur peut prononcer pour déclencher la commande                                                                 |
| **Paramètres**       | Informations requises pour exécuter la commande                                                                                |
| **Règles d’exécution** | Actions à effectuer pour exécuter la commande. Par exemple, répondre à l'utilisateur ou communiquer avec un autre service web. |
| **Règles d’interaction**   | Règles supplémentaires pour gérer des situations plus spécifiques ou complexes                                                              |


> [!div class="mx-imgBorder"]
> ![Créer une commande](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Ajouter des exemples de phrases

Nous allons commencer par la section **Exemples de phrases**, puis nous fournirons un exemple de ce que l'utilisateur peut dire.

1. Sélectionnez la section **Exemples de phrases** dans le volet central.
1. Dans le volet tout à droite, ajoutez des exemples :

    ```
    turn on the tv
    ```

1.  En haut du volet, sélectionnez **Enregistrer**.

Pour le moment, nous n’avons pas de paramètres ; nous pouvons donc passer à la section **Règles d’exécution**.

### <a name="add-a-completion-rule"></a>Ajouter une règle d’exécution

La commande doit maintenant avoir une règle d’exécution. Cette règle indique à l’utilisateur qu’une action d’exécution est en cours. Pour en savoir plus sur les règles et les règles d’exécution, consultez les [références](./custom-commands-references.md).

1. Sélectionnez la règle d’exécution par défaut **Done** (terminé) et modifiez-la comme suit :

    
    | Paramètre    | Valeur suggérée                          | Description                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nom**       | ConfirmationResponse                  | Nom décrivant l’objectif de la règle          |
    | **Conditions** | None                                     | Conditions déterminant le moment où la règle peut s’exécuter    |
    | **Actions**    | Envoyer une réponse vocale > Éditeur simple > Première variation > `Ok, turning the tv on` | Action à exécuter lorsque la condition de la règle est remplie |
    



   > [!div class="mx-imgBorder"]
   > ![Créer une ressource Speech](media/custom-commands/create-speech-response-action.png)

1. Sélectionnez **Enregistrer** pour enregistrer l’action.
1. Revenez à la section **Règles d'exécution** et sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées. 


    > [!NOTE]
    > Il n’est pas nécessaire d’utiliser la règle de saisie semi-automatique par défaut fournie avec la commande. Si nécessaire, vous pouvez supprimer la règle de saisie semi-automatique par défaut et ajouter votre propre règle.

### <a name="try-it-out"></a>Faites un essai

Testez le comportement à l'aide du volet de conversation Tester.
1. Sélectionnez l'icône **Former** en haut du volet droit.
1. Une fois l'apprentissage terminé, sélectionnez **Tester**. Testez l’énoncé suivant en mode vocal/texte :
    - Entrée : allume la télévision
    - Sortie : D’accord, j’allume la télévision


> [!div class="mx-imgBorder"]
> ![Tester avec Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Dans le panneau de test, vous pouvez sélectionner **Turn details** (Activer les détails) pour obtenir plus d’informations sur la façon dont cette entrée vocale/texte a été traitée.  

## <a name="add-settemperature-command"></a>Ajouter une commande SetTemperature

Ajoutez maintenant une nouvelle commande **SetTemperature** qui ne traitera qu’un seul énoncé, `set the temperature to 40 degrees`, puis répondra avec le message `Ok, setting temperature to 40 degrees`.

Suivez les étapes décrites dans la commande **TurnOn** pour créer une commande à l’aide de l’exemple de phrase « **règle la température sur 40 degrés** ».

Modifiez ensuite les règles d’exécution **Done** (terminé) existantes comme suit :

| Paramètre    | Valeur suggérée                          |
| ---------- | ---------------------------------------- |
| Nom  | ConfirmationResponse                  |
| Conditions | None                                     |
| Actions    | Envoyer une réponse vocale > Éditeur simple > Première variation > `Ok, setting temperature to 40 degrees` |

Sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées à la commande.

## <a name="add-setalarm-command"></a>Ajouter une commande SetAlarm
Créez une commande **SetAlarm** à l’aide de l’exemple de phrase « **règle une alarme pour 9 h demain matin** ». Modifiez ensuite les règles d’exécution **Done** (terminé) existantes comme suit :

| Paramètre    | Valeur suggérée                          |
| ---------- | ---------------------------------------- |
| Nom de la règle  | ConfirmationResponse                  |
| Conditions | None                                     |
| Actions    | Envoyer une réponse vocale > Éditeur simple > Première variation > `Ok, setting an alarm for 9 am tomorrow` |

Sélectionnez **Enregistrer** pour enregistrer toutes les modifications apportées à la commande.

## <a name="try-it-out"></a>Faites un essai

Testez le comportement à l'aide du volet de conversation Tester.
1. Sélectionnez **Train** (Entraîner). Une fois la formation terminée, sélectionnez **Tester** et essayez :
    - Vous tapez : règle la température sur 40 degrés
    - La réponse attendue est : Régler la température sur 40 degrés
    - Entrez : Allume la télévision
    - La réponse attendue est : D’accord, j’allume la télévision
    - Vous tapez : régler une alarme pour 9 h demain matin
    - La réponse attendue est : OK, régler une alarme pour 9 h demain matin

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter des paramètres aux commandes](./how-to-custom-commands-add-parameters-to-commands.md)
