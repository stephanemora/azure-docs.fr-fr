---
title: 'Procédure : Ajouter une confirmation à une commande personnalisée (préversion)'
titleSuffix: Azure Cognitive Services
description: Cet article montre comment implémenter des confirmations pour une commande dans des commandes personnalisées.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858241"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procédure : Ajouter une confirmation à une commande personnalisée (préversion)

Cet article montre comment ajouter une confirmation à une commande.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :
> [!div class="checklist"]
> *  [Démarrage rapide : Créer une commande personnalisée (préversion)](./quickstart-custom-speech-commands-create-new.md)
> * [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Créer une commande SetAlarm

Pour illustrer les confirmations, commençons par créer une commande permettant à l’utilisateur de définir une alarme.

1. Ouvrez votre application de commandes personnalisées précédemment créée dans [Speech Studio](https://speech.microsoft.com/).
1. Créez une commande `SetAlarm`.
1. Ajoutez un paramètre appelé `DateTime` avec la configuration suivante.

   | Paramètre                           | Valeur suggérée                     |  Description                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Nom                              | DateTime                                | Nom descriptif du paramètre                                |
   | Obligatoire                          | cochée                                 | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande |
   | Réponse pour le paramètre obligatoire   | Éditeur simple -> What time?                              | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue |
   | Type                              | DateTime                                | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure ou Zone géographique   |
   | Date par défaut                     | Si la date n’est pas indiquée, utiliser aujourd’hui            | Valeur par défaut de la variable à utiliser si elle n'est pas fournie par l’utilisateur.  |  
   | Heure par défaut                     | Si l’heure n’est pas indiquée, utiliser le début de la journée     |  Valeur par défaut de la variable à utiliser si elle n'est pas fournie par l’utilisateur.|

1. Ajoutez quelques exemples de phrases.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Ajoutez une règle de complétion pour confirmer le résultat.

   | Paramètre    | Valeur suggérée                               |Description                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Nom de la règle  | Définir l’alarme                                               |    Nom décrivant l’objectif de la règle |
   | Actions    | Envoyer une réponse vocale - Ok, alarm set for {DateTime}"    |Action à exécuter lorsque la condition de la règle est remplie

## <a name="try-it-out"></a>Faites un essai

1. Sélectionnez l'icône `Train` en haut du volet droit.

1. Une fois l'apprentissage terminé, sélectionnez `Test`.
    - Entrée : Set alarm for tomorrow at noon
    - Sortie : Ok, alarm set for 2020-05-02 12:00:00
    - Entrée : Set an alarm
    - Sortie : What time?
    - Entrée : 5pm
    - Sortie : Ok, alarm set for 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Ajouter les règles avancées pour la confirmation

Les confirmations sont effectuées moyennant l'ajout de règles d’interaction.

1. Dans la commande `SetAlarm` existante, ajoutez une **règle d’interaction** en sélectionnant l'icône `+Add` dans le volet central, puis **Règles d’interaction** -> **Confirmer la commande**.

    Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme et attend une confirmation (yes/no) pour le tour suivant.

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Confirmer la date et l’heure                                                                | Nom décrivant l’objectif de la règle          |
   | Conditions            | Paramètre obligatoire -> DateTime                                                    | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions               | Envoyer une réponse vocale -> Are you sure you want to set an alarm for {DateTime}?     | Action à exécuter lorsque la condition de la règle est remplie |
   | Attentes          | Attente de confirmation de l'utilisateur                                                 | Attente pour le tour suivant                      |
   | État après exécution  | Attente d'entrée de l'utilisateur                                                            | État de l’utilisateur après le tour                  |
  
      > [!div class="mx-imgBorder"]
      > ![Créer une réponse pour un paramètre obligatoire](media/custom-speech-commands/add-validation-set-temperature.png)

1. Ajoutez une autre règle d'interaction pour gérer une confirmation réussie (l’utilisateur a répondu yes)

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Confirmation d’acceptation                                                            | Nom décrivant l’objectif de la règle          |
   | Conditions            | Confirmation réussie et paramètre obligatoire -> DateTime                      | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | État après exécution | Exécuter les règles d’exécution                                                          | État de l’utilisateur après le tour                   |

1. Ajoutez une règle avancée pour gérer une confirmation réussie (l’utilisateur a répondu no).

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Confirmation refusée                                                                   | Nom décrivant l’objectif de la règle          |
   | Conditions            | Confirmation refusée et paramètre obligatoire -> DateTime                               | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions               | Effacer la valeur du paramètre-> DateTime et envoyer une réponse vocale-> No problem, what time then?  | Action à exécuter lorsque la condition de la règle est remplie |
   | État après exécution | Attendre l’entrée                                                                   | État de l’utilisateur après le tour                   |
   | Attentes          | Attente d'entrée de paramètre(s) de l'utilisateur -> DateTime                           | Attente pour le tour suivant                      |

## <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez `Train`, attendez la fin de l’apprentissage, puis sélectionnez `Test`.

- Entrée : Set alarm for tomorrow at noon
- Sortie : Are you sure you want to set an alarm for 2020-05-02 12:00:00?
- Entrée : Non
- Sortie : No problem, what time then?
- Entrée : 5pm
- Sortie : Are you sure you want to set an alarm for 2020-05-01 17:00:00?
- Entrée : Oui
- Sortie : Ok, alarm set for 2020-05-01 17:00:00

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter une correction en une étape à une commande personnalisée (préversion)](./how-to-custom-speech-commands-one-step-correction.md)