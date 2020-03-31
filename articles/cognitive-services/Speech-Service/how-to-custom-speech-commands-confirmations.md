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
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75453364"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procédure : Ajouter une confirmation à une commande personnalisée (préversion)

Cet article montre comment ajouter une confirmation à une commande.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :

- [Démarrage rapide : Créer une commande personnalisée (préversion)](./quickstart-custom-speech-commands-create-new.md)
- [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Créer une commande SetAlarm

Pour illustrer les validations, commençons par créer une commande permettant à l’utilisateur de définir une alarme.

1. Ouvrir votre application de commandes personnalisées précédemment créée dans [Speech Studio](https://speech.microsoft.com/)
1. Créez une commande **SetAlarm**.
1. Ajoutez un paramètre nommé DateTime.

   | Paramètre           | Valeur suggérée                                          | Description                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nom              | DateTime                                                 | Nom descriptif de votre paramètre de commande                                                    |
   | Obligatoire          | true                                                     | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande |
   | Modèle de réponse | "- What time?"                                           | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue                              |
   | Type              | DateTime                                                 | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure                                      |
   | Date par défaut     | Si la date n’est pas indiquée, utiliser aujourd’hui                             |                                                                                                  |
   | Heure par défaut     | Si l’heure n’est pas indiquée, utiliser le début de la journée                      |                                                                                                  | 

1. Ajouter des exemples de phrases
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Ajouter une règle de complétion pour confirmer le résultat

   | Paramètre    | Valeur suggérée                                         | Description                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle  | Définir l’alarme                                               | Nom décrivant l’objectif de la règle          |
   | Actions    | SpeechResponse - "- Ok, alarm set for {DateTime}"       | Action à exécuter lorsque la condition de la règle est remplie |

## <a name="try-it-out"></a>Faites un essai

Sélectionnez le panneau de test pour essayer quelques interactions.

- Entrée : Set alarm for tomorrow at noon
- Sortie : "Ok, alarm set for 12/06/2019 12:00:00"

- Entrée : Set an alarm
- Sortie : "What time?"
- Entrée : 5pm
- Sortie : "Ok, alarm set for 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Ajouter les règles avancées pour la confirmation

1. Ajoutez une règle avancée pour la confirmation. 

    Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme et attend une confirmation (yes/no) pour le tour suivant.

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Confirmer la date et l’heure                                                                | Nom décrivant l’objectif de la règle          |
   | Conditions            | Required Parameter : DateTime                                                    | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions               | SpeechResponse - "- Are you sure you want to set an alarm for {DateTime}?"       | Action à exécuter lorsque la condition de la règle est remplie |
   | État après exécution | Attendre l’entrée                                                                   | État de l’utilisateur après le tour                  |
   | Attentes          | Confirmation                                                                     | Attente pour le tour suivant                      |

1. Ajoutez une règle avancée pour gérer une confirmation réussie (l’utilisateur a répondu yes).

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Confirmation d’acceptation                                                            | Nom décrivant l’objectif de la règle          |
   | Conditions            | SuccessfulConfirmation et Required Parameter : DateTime                           | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | État après exécution | Prêt pour l’achèvement                                                             | État de l’utilisateur après le tour                   |

1. Ajoutez une règle avancée pour gérer une confirmation réussie (l’utilisateur a répondu no).

   | Paramètre               | Valeur suggérée                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Confirmation de refus                                                                   | Nom décrivant l’objectif de la règle          |
   | Conditions            | DeniedConfirmation et Required Parameter : DateTime                               | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions               | ClearParameter : DateTime et SpeechResponse - "- No problem, what time then?"     | Action à exécuter lorsque la condition de la règle est remplie |
   | État après exécution | Attendre l’entrée                                                                   | État de l’utilisateur après le tour                   |
   | Attentes          | ElicitParameters : DateTime                                                      | Attente pour le tour suivant                      |

## <a name="try-it-out"></a>Faites un essai

Sélectionnez le panneau de test pour essayer quelques interactions.

- Entrée : Set alarm for tomorrow at noon
- Sortie : "Are you sure you want to set an alarm for 12/07/2019 12:00:00?"
- Entrée : Non
- Sortie : "No problem, what time then?"
- Entrée : 5pm
- Sortie : "Are you sure you want to set an alarm for 12/06/2019 17:00:00?"
- Entrée : Oui
- Sortie : "Ok, alarm set for 12/06/2019 17:00:00"

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter une correction en une étape à une commande personnalisée (préversion)](./how-to-custom-speech-commands-one-step-correction.md)