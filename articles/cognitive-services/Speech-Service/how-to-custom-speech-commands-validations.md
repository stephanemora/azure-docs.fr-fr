---
title: 'Procédure : Ajouter des validations aux paramètres des Commandes personnalisées'
titleSuffix: Azure Cognitive Services
description: Dans cet article, nous expliquons comment ajouter des validations à un paramètre dans les commandes personnalisées.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857185"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procédure : ajouter des validations aux paramètres de commande personnalisée (préversion)

Dans cet article, vous allez ajouter des validations aux paramètres et des invites de correction.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :

> [!div class="checklist"]
> * [Démarrage rapide : Créer une application Commandes personnalisées](./quickstart-custom-speech-commands-create-new.md)
> * [Démarrage rapide : Créer une Commande personnalisée avec des paramètres](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Créer une commande SetTemperature

Pour illustrer les validations, commençons par créer une commande permettant à aux utilisateurs de régler la température.

1. Ouvrir votre application de commandes personnalisées précédemment créée dans [Speech Studio](https://speech.microsoft.com/)
1. Créer une commande `SetTemperature`
1. Ajoutez un paramètre pour la température cible.

   | Configuration des paramètres           | Valeur suggérée    |Description                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Nom              | Température                       | Nom descriptif du paramètre                                |
   | Obligatoire          | cochée                           | Case à cocher indiquant si une valeur de ce paramètre est requise avant d'exécuter la commande |
   | Réponse pour le paramètre obligatoire     | Éditeur simple -> Quelle température voulez-vous ?  | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue |
   | Type              | Number                            | Type du paramètre, par exemple Nombre, Chaîne, Date/heure ou Zone géographique   |

1. Ajoutez une validation pour le paramètre de température.

    - Sur la page de configuration **Paramètres** du paramètre `Temperature`, sélectionnez `Add a validation` dans la section Validations.
    - Renseignez les valeurs comme suit dans la fenêtre contextuelle **Nouvelle validation**, puis sélectionnez **Créer**.

  
       | Configuration des paramètres         | Valeur suggérée                                          | Description                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Valeur minimale        | 60               | Pour les paramètres numériques, valeur minimale que ce paramètre peut accepter |
       | Valeur maximale        | 80               | Pour les paramètres numériques, valeur maximale que ce paramètre peut accepter |
       | Réponse en cas d'échec - Éditeur simple| Première variante - Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés      | Inviter à demander une nouvelle valeur en cas d'échec de la validation                                       |

       > [!div class="mx-imgBorder"]
       > ![Ajouter une validation de plage](media/custom-speech-commands/validations-add-temperature.png)

1. Ajouter quelques exemples de phrases

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Ajouter une règle de complétion pour confirmer le résultat

   | Paramètre    | Valeur suggérée                                           |Description                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nom       | Message de confirmation                                      |Nom décrivant l’objectif de la règle |
   | Conditions | Paramètres obligatoires - `Temperature`                       |Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions    | Envoyer une réponse vocale- `Ok, setting temperature to {Temperature} degrees` | Action à exécuter lorsque la condition de la règle est remplie |

> [!TIP]
> Cet exemple utilise une réponse vocale pour confirmer le résultat. Pour obtenir des exemples de complétion de la commande avec une action client, consultez : [Guide pratique pour Exécuter des commandes sur le client avec le SDK Speech](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Faites un essai
1. Sélectionnez l'icône `Train` en haut du volet droit.

1. Une fois l'apprentissage terminé, sélectionnez `Test` et essayez quelques interactions.

    - Entrée : Régler la température sur 72 degrés
    - Sortie : Régler la température sur 72 degrés
    - Entrée : Régler la température sur 45 degrés
    - Sortie : Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés
    - Entrée : Régler plutôt sur 72 degrés
    - Sortie : Régler la température sur 72 degrés

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter une confirmation à une commande personnalisée (préversion)](./how-to-custom-speech-commands-confirmations.md)
