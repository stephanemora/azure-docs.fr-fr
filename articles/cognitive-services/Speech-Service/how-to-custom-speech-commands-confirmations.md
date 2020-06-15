---
title: Ajouter des confirmations dans une application Commandes personnalisées en préversion - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment ajouter des confirmations aux commandes dans une application Commandes personnalisées en préversion.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310466"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Ajouter des confirmations à une commande dans une application Commandes personnalisées en préversion

Dans cet article, vous apprenez à créer des confirmations pour des commandes dans une application Commandes personnalisées en préversion.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes décrites dans les articles suivants :
> [!div class="checklist"]
> * [Démarrage rapide : Créer une application Commandes personnalisées en préversion](./quickstart-custom-speech-commands-create-new.md)
> * [Démarrage rapide : Créer une application Commandes personnalisées en préversion avec des paramètres](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Créer une commande SetAlarm

Pour illustrer les confirmations, créez une commande qui définit une alarme.

1. Dans [Speech Studio](https://speech.microsoft.com/), ouvrez l’application Commandes personnalisées (préversion) que vous avez créée.
1. Créez une commande **SetAlarm**.
1. Ajoutez un paramètre **DateTime** qui a la configuration suivante :

   | Paramètre                           | Valeur suggérée                     |  Description                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Nom**                              | **DateTime**                                | Nom descriptif du paramètre                                |
   | **Obligatoire**                          | Activé                                 | Case à cocher indiquant si une valeur de ce paramètre est nécessaire pour exécuter la commande |
   | **Réponse pour un paramètre obligatoire**   | **Éditeur simple -> À quelle heure ?**                              | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue |
   | **Type**                              | **DateTime**                                | Type du paramètre, par exemple Nombre, Chaîne, DateHeure ou Zone géographique   |
   | **Date par défaut**                     | Si la date n’est pas indiquée, utiliser aujourd’hui            | Valeur par défaut de la variable à utiliser si elle n’est pas fournie par l’utilisateur  |  
   | **Heure par défaut**                     | Si l’heure n’est pas indiquée, utiliser le début de la journée     |  Valeur par défaut de la variable à utiliser si elle n’est pas fournie par l’utilisateur|

1. Ajoutez quelques exemples de phrases.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Ajoutez une règle de complétion pour confirmer le résultat. Utilisez la configuration suivante :

   | Paramètre    | Valeur suggérée                               |Description                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Nom de la règle**  | **Définir l’alarme**                                               |    Nom décrivant l’objectif de la règle |
   | **Actions**    | **Envoyer une réponse vocale -> Ok, alarme définie pour {DateTime}**    |Action à exécuter lorsque la condition de la règle est remplie

## <a name="try-it-out"></a>Faites un essai

1. Sélectionnez **Entraîner** en haut du volet droit.

1. Une fois l’entraînement terminé, sélectionnez **Tester**, puis essayez les interactions suivantes :
    - Entrée : Set alarm for tomorrow at noon
    - Sortie : Ok, alarme définie pour 2020-05-02 12:00:00
    - Entrée : Set an alarm
    - Sortie : What time?
    - Entrée : 5pm
    - Sortie : Ok, alarme définie pour 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Ajouter des règles d’interaction pour la confirmation

Créez des confirmations en ajoutant des règles d’interaction.

1. Dans votre commande **SetAlarm**, sélectionnez **Ajouter** dans le volet du milieu, puis sélectionnez **Règles d’interaction** > **Commande de confirmation**.

    Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme. Utilisez les paramètres suivants :

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nom de la règle**             | **Confirmer la date et l’heure**                                                                | Nom décrivant l’objectif de la règle          |
   | **Conditions**            | **Paramètre obligatoire -> DateTime**                                                    | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | **Actions**               | **Envoyer une réponse vocale -> Voulez-vous vraiment définir une alarme pour {DateTime} ?**     | Action à exécuter lorsque la condition de la règle est remplie |
   | **Attentes**          | **Attente de confirmation de l’utilisateur**                                                 | Attente pour le tour suivant                      |
   | **État après exécution**  | **Attente d’entrée de l’utilisateur**                                                            | État de l’utilisateur après le tour                  |
  
      > [!div class="mx-imgBorder"]
      > ![Créer une réponse pour un paramètre obligatoire](media/custom-speech-commands/add-validation-set-temperature.png)

1. Ajoutez une règle d’interaction pour une confirmation acceptée (l’utilisateur a dit « oui »). Utilisez la configuration suivante :

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nom de la règle**             | **Confirmation acceptée**                                                            | Nom décrivant l’objectif de la règle          |
   | **Conditions**            | **Confirmation réussie et paramètre obligatoire -> DateTime**                      | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | **État après exécution** | **Exécuter les règles de complétion**                                                          | État de l’utilisateur après le tour                   |

1. Ajoutez une règle d’interaction pour une confirmation refusée (l’utilisateur a dit « non »). Utilisez la configuration suivante :

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nom de la règle**             | **Confirmation refusée**                                                                   | Nom décrivant l’objectif de la règle          |
   | **Conditions**            | **Confirmation refusée et paramètre obligatoire -> DateTime**                               | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | **Actions**               | **Effacer la valeur du paramètre -> DateTime et envoyer une réponse vocale -> Aucun problème, à quelle heure ?**  | Action à exécuter lorsque la condition de la règle est remplie |
   | **État après exécution** | **Attendre l’entrée**                                                                   | État de l’utilisateur après le tour                   |
   | **Attentes**          | **Attente d’entrée de paramètres de l’utilisateur -> DateTime**                           | Attente pour le tour suivant                      |

## <a name="try-out-the-changes"></a>Essayer les modifications

1. Sélectionnez **Train** (Entraîner).

1. Une fois l’entraînement terminé, sélectionnez **Tester**, puis essayez ces interactions :

    - Entrée : Set alarm for tomorrow at noon
    - Sortie : Are you sure you want to set an alarm for 2020-05-02 12:00:00?
    - Entrée : Non
    - Sortie : No problem, what time then?
    - Entrée : 5pm
    - Sortie : Are you sure you want to set an alarm for 2020-05-01 17:00:00?
    - Entrée : Oui
    - Sortie : Ok, alarme définie pour 2020-05-01 17:00:00

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter une correction en une étape à une commande dans une application Commandes personnalisées en préversion](./how-to-custom-speech-commands-one-step-correction.md)
