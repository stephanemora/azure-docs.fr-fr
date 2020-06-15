---
title: Ajouter une correction en une étape dans Commandes personnalisées (préversion) - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment ajouter une correction en une étape pour une commande dans une application Commandes personnalisées (préversion).
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310425"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Ajouter une correction en une étape à une commande personnalisée dans une application Commandes personnalisées (préversion)

Dans cet article, vous allez découvrir comment ajouter une confirmation en une étape à une commande dans une application Commandes personnalisées (préversion).

Une correction en une étape est utilisée pour mettre à jour une commande qui vient d’être effectuée. Quand vous ajoutez une correction en une étape à une commande d’alarme, vous pouvez changer d’avis et mettre à jour l’heure de l’alarme. Par exemple :

- Entrée : Set alarm for tomorrow at noon
- Sortie : Ok, alarm set for 2020-05-02 12:00:00
- Entrée : Non, demain à 13h00
- Sortie : OK

> [!NOTE]
> Dans un scénario réel, le client exécute une action à la suite de l’exécution de la commande. Cet article suppose que vous disposez d’un mécanisme pour mettre à jour l’alarme dans votre application back-end.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes décrites dans les articles suivants :
> [!div class="checklist"]

> * [Démarrage rapide : Créer une application Commandes personnalisées (préversion)](./quickstart-custom-speech-commands-create-new.md)
> * [Démarrage rapide : Créer une application Commandes personnalisées (préversion) avec des paramètres](./quickstart-custom-speech-commands-create-parameters.md)
> * [Guide pratique pour ajouter des confirmations à une commande dans une application Commandes personnalisées (préversion)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Ajouter des règles d'interaction pour la correction en une étape

Pour illustrer la correction en une étape, étendons la commande **SetAlarm** que vous avez créée dans [Guide pratique : ajouter une confirmation à une commande dans Commandes personnalisées (préversion)](./how-to-custom-speech-commands-confirmations.md).

1. Ajoutez une règle d’interaction pour mettre à jour votre commande **SetAlarm**.

    Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme et attend une confirmation (yes/no) pour le tour suivant.

   | Paramètre               | Valeur suggérée                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Nom de la règle**             | **Mettre à jour l’alarme précédente**                                            | Nom décrivant l’objectif de la règle          |
   | **Conditions**            | **La commande précédente doit être mise à jour + Paramètre obligatoire -> DateHeure**                | Conditions déterminant le moment où la règle peut s’exécuter    |   
   | **Actions**               | **Envoyer une réponse vocale -> Éditeur simple -> Mise à jour de l’alarme précédente sur {DateHeure}**      | Action à exécuter lorsque les conditions de la règle sont remplies |
   | **État après exécution** | **Commande terminée**        | État de l’utilisateur après le tour                   |

1. Dans le volet, sélectionnez la règle d’interaction que vous venez de créer. Cliquez sur le bouton points de suspension ( **...** ) dans le coin supérieur gauche du volet. Utilisez ensuite la flèche **Monter** pour déplacer la règle vers le haut de la liste des **Règles d’interaction** .
   > [!div class="mx-imgBorder"]
   > ![Ajouter une validation de plage](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > Dans une application réelle, utilisez la section **Actions** pour renvoyer une activité au client ou appeler un point de terminaison HTTP afin de mettre à jour l’alarme dans votre système.

## <a name="try-it-out"></a>Faites un essai

1. Sélectionnez **Train** (Entraîner).

1. Une fois l’entraînement terminé, sélectionnez **Tester**, puis essayez les interactions suivantes :

   - Entrée : Set alarm for tomorrow at noon
   - Sortie : Are you sure you want to set an alarm for 2020-05-02 12:00:00
   - Entrée : Oui
   - Sortie : Ok, alarm set for 2020-05-02 12:00:00
   - Entrée : No, tomorrow at 2pm
   - Sortie : Updating previous alarm to 2020-05-02 14:00:00
