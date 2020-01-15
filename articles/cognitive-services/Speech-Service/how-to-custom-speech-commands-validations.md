---
title: 'Procédure : ajouter des validations aux paramètres de commande personnalisée (préversion)'
titleSuffix: Azure Cognitive Services
description: Dans cet article, nous expliquons comment ajouter des validations à un paramètre dans les commandes personnalisées.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: c89c388f919ca95a331d1d406f5b1776c127ebad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446916"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procédure : ajouter des validations aux paramètres de commande personnalisée (préversion)

Dans cet article, vous allez apprendre à ajouter des validations aux paramètres et à demander une correction.

## <a name="prerequisites"></a>Conditions préalables requises

Vous devez avoir effectué les étapes décrites dans les articles suivants :

- [Démarrage rapide : Créer une commande personnalisée (préversion)](./quickstart-custom-speech-commands-create-new.md)
- [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Créer une commande SetTemperature

Pour illustrer les validations, commençons par créer une commande permettant à l’utilisateur de régler la température.

1. Ouvrir votre application de commandes personnalisées précédemment créée dans [Speech Studio](https://speech.microsoft.com/)
1. Recréer une commande **SetTemperature**
1. Ajouter un paramètre pour la température cible
1. Ajouter une validation pour le paramètre de température
   > [!div class="mx-imgBorder"]
   > ![Ajouter une validation de plage](media/custom-speech-commands/validations-add-temperature.png)

   | Paramètre           | Valeur suggérée                                          | Description                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name              | Température                                              | Nom descriptif de votre paramètre de commande                                                    |
   | Obligatoire          | true                                                     | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande |
   | Modèle de réponse | « - Quelle température aimeriez-vous ? »                     | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue                              |
   | Type              | Number                                                   | Type du paramètre, par exemple Nombre, Chaîne ou Date/heure                                      |
   | Validation        | Valeur minimale : 60, Valeur maximale : 80                             | Pour les paramètres de type Nombre, plage de valeurs autorisée pour le paramètre                             |
   | Modèle de réponse | « - Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés. »      | Demander d’indiquer une valeur mise à jour en cas d’échec de la validation                                       |

1. Ajouter des exemples de phrases

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Ajouter une règle de complétion pour confirmer le résultat

   | Paramètre    | Valeur suggérée                                           | Description                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle  | Message de confirmation                                      | Nom décrivant l’objectif de la règle          |
   | Conditions | Paramètre obligatoire - Température                          | Conditions déterminant le moment où la règle peut s’exécuter    |
   | Actions    | SpeechResponse - « - OK, réglage sur {temperature} degrés » | Action à exécuter lorsque la condition de la règle est remplie |

> [!TIP]
> Cet exemple utilise une réponse vocale pour confirmer le résultat. Pour obtenir des exemples de complétion de la commande avec une action client, consultez : [Guide pratique pour effectuer des commandes sur le client avec le SDK Speech (préversion)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Faites un essai

Sélectionnez le panneau de test pour essayer quelques interactions.

- Entrée : Régler la température sur 72 degrés
- Sortie : « OK, réglage sur 72 degrés »

- Entrée : Régler la température sur 45 degrés
- Sortie : « Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés. »
- Entrée : Régler plutôt sur 72 degrés
- Sortie : « OK, réglage sur 72 degrés »

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter une confirmation à une commande personnalisée (préversion)](./how-to-custom-speech-commands-confirmations.md)