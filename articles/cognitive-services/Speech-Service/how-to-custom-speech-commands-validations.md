---
title: Ajouter des validations dans Commandes personnalisées (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment ajouter des validations à un paramètre de commande dans une application Commandes personnalisées (préversion).
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: nitinme
ms.openlocfilehash: eb011510a9f636aea9910a4be445cd094acf0c21
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509574"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Ajouter des validations à un paramètre de commande dans une application Commandes personnalisées (préversion)

Dans cet article, vous allez apprendre à ajouter des validations aux paramètres et des invites de correction.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes décrites dans les articles suivants :

> [!div class="checklist"]
 
> * [Démarrage rapide : Créer une application Commandes personnalisées en préversion](./quickstart-custom-speech-commands-create-new.md)
> * [Démarrage rapide : Créer une application Commandes personnalisées (préversion) avec des paramètres](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Créer une commande SetTemperature

Pour illustrer les validations, créez une commande permettant aux utilisateurs de régler la température.

1. Dans [Speech Studio](https://speech.microsoft.com/), ouvrez l’application Commandes personnalisées (préversion) que vous avez créée.
1. Créez une commande **SetTemperature**.
1. Ajoutez un paramètre de température qui a la configuration suivante :

   | Configuration des paramètres           | Valeur suggérée    |Description                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Nom**              | **Température**                       | Nom descriptif du paramètre                                |
   | **Obligatoire**          | Activé                           | Case à cocher indiquant si une valeur de ce paramètre est requise avant d’exécuter la commande |
   | **Réponse pour le paramètre obligatoire**     | **Éditeur simple -> Quelle température voulez-vous ?**  | Invite permettant de demander la valeur de ce paramètre lorsqu'elle n'est pas connue |
   | **Type**              | **Nombre**                            | Type du paramètre, par exemple Nombre, Chaîne, Date/heure ou Zone géographique   |

1. Ajoutez une validation pour le paramètre de température.

    1. Dans la page de configuration **Paramètres** du paramètre de température, sélectionnez **Ajouter une validation** dans la section **Validations**.

    1. Dans la fenêtre contextuelle **Nouvelle validation**, configurez la validation comme suit :
  
       | Configuration des paramètres         | Valeur suggérée                                          | Description                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Valeur minimale**        | **60**               | Pour les paramètres numériques, valeur minimale que ce paramètre peut accepter |
       | **Valeur maximale**        | **80**               | Pour les paramètres numériques, valeur maximale que ce paramètre peut accepter |
       | **Réponse en cas d’échec - Éditeur simple**| **Première variante - Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés**      | Inviter à demander une nouvelle valeur en cas d'échec de la validation                                       |

       > [!div class="mx-imgBorder"]
       > ![Ajouter une validation de plage](media/custom-speech-commands/validations-add-temperature.png)

1. Sélectionnez **Create** (Créer).

1. Ajoutez quelques exemples de phrases.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Ajoutez une règle d’exécution dotée de la configuration suivante. Cette règle confirme le résultat.

   | Paramètre    | Valeur suggérée                                           |Description                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nom       | Message de confirmation                                      |Nom décrivant l’objectif de la règle |
   | **Conditions** | **Paramètres obligatoires - Température**                       |Conditions déterminant le moment où la règle peut s’exécuter    |   
   | **Actions**    | **Envoyer une réponse vocale - OK, réglage de la température sur {temperature} degrés** | Action à exécuter lorsque la condition de la règle est remplie |

> [!TIP]
> Cet exemple utilise une réponse vocale pour confirmer le résultat. Pour obtenir des exemples de complétion de la commande avec une action cliente, consultez [Guide pratique : Exécuter des commandes sur le client avec le SDK Speech](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Faites un essai

1. Sélectionnez **Train** (Entraîner).

1. Une fois l’entraînement terminé, sélectionnez **Tester**, puis essayez les interactions suivantes :

    - Entrée : Régler la température sur 72 degrés
    - Sortie : Régler la température sur 72 degrés
    - Entrée : Régler la température sur 45 degrés
    - Sortie : Désolé, je peux uniquement régler une température comprise entre 60 et 80 degrés
    - Entrée : Régler plutôt sur 72 degrés
    - Sortie : Régler la température sur 72 degrés

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des confirmations à une commande dans une application Commandes personnalisées (préversion)](./how-to-custom-speech-commands-confirmations.md)
