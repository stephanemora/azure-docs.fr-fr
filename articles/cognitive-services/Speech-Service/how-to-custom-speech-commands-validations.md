---
title: Guide pratique pour ajouter des validations aux paramètres de commande personnalisée (préversion)
titleSuffix: Azure Cognitive Services
description: Dans cet article, ajoutez des validations aux paramètres de commande personnalisée.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 64e092405686caca7baeaf58f19d577a3f80e169
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506476"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Guide pratique pour ajouter des validations aux paramètres de commande personnalisée (préversion)

Dans cet article, vous allez apprendre à ajouter des validations aux paramètres et à demander une correction.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :

- [Démarrage rapide : Créer une commande personnalisée (préversion)](./quickstart-custom-speech-commands-create-new.md)
- [Démarrage rapide : Créer une commande personnalisée avec des paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)

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
   | Nom              | Température                                              | Nom descriptif de votre paramètre de commande                                                    |
   | Obligatoire          | true                                                     | Case à cocher indiquant si une valeur de ce paramètre est nécessaire avant de terminer la commande |
   | Modèle de réponse | « Quelle température aimeriez-vous ? »                       | Invite permettant de demander la valeur de ce paramètre quand il n’est pas connu                              |
   | Type              | Number                                                   | Type de paramètre, tel que Nombre, Chaîne ou Date Heure                                      |
   | Validation        | Valeur minimale : 60, Valeur maximale : 80                             | Pour les paramètres de type Nombre, plage de valeurs autorisée pour le paramètre                              |
   | Modèle de réponse | « Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés. »        | Demander d’indiquer une valeur mise à jour en cas d’échec de la validation                                       |

1. Ajouter des exemples de phrases

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Ajouter une règle de complétion pour confirmer le résultat

   | Paramètre    | Valeur suggérée                                         | Description                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle  | Message de confirmation                                    | Nom décrivant l’objectif de la règle          |
   | Conditions | Paramètre obligatoire - Température                        | Conditions déterminant le moment où la règle peut s’exécuter    |
   | Actions    | SpeechResponse - « OK, réglage sur {temperature} degrés » | Action à exécuter lorsque la condition de règle est remplie |

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
