---
title: 'Procédure : Ajouter une correction en une étape à une commande personnalisée (préversion) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans cet article, nous expliquons comment implémenter des corrections en une étape pour une commande dans des commandes personnalisées.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453348"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procédure : Ajouter une correction en une étape à une commande personnalisée (préversion)

Dans cet article, vous allez découvrir comment ajouter une confirmation en une étape à une commande.

Une correction en une étape est utilisée pour mettre à jour une commande qui vient d’être effectuée.

Par exemple, si vous venez de configurer une alarme, vous pouvez changer d’avis et mettre à jour l’heure de l’alarme.

- Entrée : Définir une alarme pour demain à midi
- Sortie : « OK, alarme définie pour le 06/12/2019 à 12:00:00 »
- Entrée : Non, demain à 13h00
- Sortie : « OK »

N’oubliez pas que cela implique que le développeur dispose d’un mécanisme permettant de mettre à jour l’alarme dans l’application principale.

## <a name="prerequisites"></a>Conditions préalables requises

Vous devez avoir effectué les étapes décrites dans les articles suivants :

- [Démarrage rapide : Créer une commande personnalisée (préversion)](./quickstart-custom-speech-commands-create-new.md)
- [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)
- [Guide pratique pour Ajouter une confirmation à une commande personnalisée (préversion)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Ajouter les règles avancées pour une correction en une étape 

Pour illustrer la correction en une étape, nous allons étendre la commande **SetAlarm** créée dans le [Guide pratique : Confirmations](./how-to-custom-speech-commands-confirmations.md).
 
1. Ajoutez une règle avancée pour mettre à jour l’alarme précédente. 

    Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme, et attend une confirmation (oui/non) pour le changement suivant.

   | Paramètre               | Valeur suggérée                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Mettre à jour l’alarme précédente                                            | Nom décrivant l’objectif de la règle          |
   | Conditions            | UpdateLastCommand et paramètre obligatoire - DateTime                | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions               | SpeechResponse - « - Mise à jour de l’alarme précédente en {DateTime} »       | Action à exécuter lorsque la condition de la règle est remplie |
   | État après exécution | Commande terminée                                                 | État de l’utilisateur après le changement                   |

1. Déplacez la règle que vous venez de créer en haut des règles avancées (faites défiler jusqu’à la règle dans le volet et cliquez sur la flèche haut).
   > [!div class="mx-imgBorder"]
   > ![Ajouter une validation de plage](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> Dans une application réelle, dans la section Actions de cette règle, vous allez également renvoyer une activité au client ou appeler un point de terminaison HTTP pour mettre à jour l’alarme dans votre système.

## <a name="try-it-out"></a>Faites un essai

Sélectionnez le panneau de test pour essayer quelques interactions.

- Entrée : Définir une alarme pour demain à midi
- Sortie : « Voulez-vous vraiment définir une alarme pour le 07/12/2019 12:00:00 ? »
- Entrée : Oui
- Sortie : « OK, alarme définie pour le 07/12/2019 à 12:00:00 »
- Entrée : Non, demain à 13h00
- Sortie : « Mise à jour de l’alarme précédente sur le 07/12/2019 à 13:00:00 »
