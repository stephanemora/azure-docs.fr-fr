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
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858275"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Procédure : Ajouter une correction en une étape à une commande personnalisée (préversion)

Dans cet article, vous allez découvrir comment ajouter une confirmation en une étape à une commande.

Une correction en une étape est utilisée pour mettre à jour une commande qui vient d’être effectuée. Autrement dit, si vous venez de configurer une alarme, vous pouvez changer d'avis et mettre à jour l'heure de l'alarme. En voici un exemple :

- Entrée : Set alarm for tomorrow at noon
- Sortie : Ok, alarm set for 2020-05-02 12:00:00
- Entrée : Non, demain à 13h00
- Sortie : OK

Scénario réel, généralement accompagné de l'exécution d'une action par le client suite à l'utilisation d'une commande - cet article suppose que vous, en tant que développeur, disposez d'un mécanisme pour mettre à jour l'alarme dans votre application principale.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :
> [!div class="checklist"]

> * [Démarrage rapide : Créer une commande personnalisée (préversion)](./quickstart-custom-speech-commands-create-new.md)
> * [Démarrage rapide : Créer une commande personnalisée avec Paramètres (préversion)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Guide pratique pour Ajouter une confirmation à une commande personnalisée (préversion)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Ajouter des règles d'interaction pour la correction en une étape 

Pour illustrer la correction en une étape, nous allons étendre la commande **SetAlarm** créée dans [Guide pratique : Ajouter une confirmation à une commande personnalisée (préversion)](./how-to-custom-speech-commands-confirmations.md).
1. Ajoutez une règle d'interaction pour mettre à jour l'alarme précédemment définie. 

    Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme et attend une confirmation (yes/no) pour le tour suivant.

   | Paramètre               | Valeur suggérée                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nom de la règle             | Mettre à jour l’alarme précédente                                            | Nom décrivant l’objectif de la règle          |
   | Conditions            | La commande précédente doit être mise à jour + Paramètre obligatoire -> DateHeure                | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | Actions               | Envoyer une réponse vocale -> Éditeur simple -> Mise à jour de l'alarme précédente sur {DateHeure}      | Action à exécuter lorsque les conditions de la règle sont remplies |
   | État après exécution | Commande terminée        | État de l’utilisateur après le tour                   |

1. Déplacez la règle que vous venez de créer vers le haut des règles d'interaction (sélectionnez la règle dans le panneau et cliquez sur la flèche HAUT située sous l'icône `...`, en haut du volet central).
   > [!div class="mx-imgBorder"]
   > ![Ajouter une validation de plage](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > Dans une application réelle, dans la section Actions de cette règle, vous allez également renvoyer une activité au client ou appeler un point de terminaison HTTP pour mettre à jour l’alarme dans votre système.

## <a name="try-it-out"></a>Faites un essai

Sélectionnez `Train`, attendez la fin de l'apprentissage, puis sélectionnez `Test`.

- Entrée : Set alarm for tomorrow at noon
- Sortie : Are you sure you want to set an alarm for 2020-05-02 12:00:00
- Entrée : Oui
- Sortie : Ok, alarm set for 2020-05-02 12:00:00
- Entrée : No, tomorrow at 2pm
- Sortie : Updating previous alarm to 2020-05-02 14:00:00
