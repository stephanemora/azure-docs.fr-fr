---
title: 'Procédure : Ajouter une confirmation à une commande personnalisée'
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous apprendrez à implémenter des confirmations pour une commande dans Commandes personnalisées.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307179"
---
# <a name="add-interaction-rules"></a>Ajouter des règles d’interaction

Cet article vous fournit des informations sur les **règles d’interaction**. Il s’agit de règles supplémentaires pour gérer des situations plus spécifiques ou complexes. Même si vous êtes libre de créer vos propres règles d’interaction personnalisées, dans cet article, vous utilisez des règles d’interaction pour les scénarios ciblés suivants :

* Confirmation de commandes
* Ajout d’une correction en une étape à des commandes

Accédez à la section [Références](./custom-commands-references.md) pour en savoir plus sur les règles d’interaction.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :
> [!div class="checklist"]
> * [Guide pratique pour Créer une application avec des commandes simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Guide pratique pour Ajouter des paramètres aux commandes](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Ajouter des confirmations à une commande

Pour ajouter une confirmation, utilisez la commande **SetTemperature**. Pour obtenir une confirmation, vous créez des règles d’interaction en procédant comme suit.

1. Sélectionnez la commande **SetTemperature** dans le volet gauche.
2. Ajoutez des règles d’interaction en sélectionnant **Ajouter** dans le volet central, puis **Règles d’interaction** > **Confirmer la commande**.

    Cette étape ajoutera 3 règles d’interaction. Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme et attend une confirmation (yes/no) pour le tour suivant.

    1. Modifiez la règle d’interaction **Confirmer la commande** conformément à la configuration suivante
        1. Renommez **Nom** en **`Confirm Temperature`** .
        1. Ajoutez une nouvelle condition en sélectionnant **Paramètres obligatoires > Température**
        1. Ajoutez une nouvelle action en sélectionnant **Type > Envoyer une réponse vocale > `Are you sure you want to set the temperature as {Temperature} degrees?`**
        1. Conservez la valeur par défaut **Attente de confirmation de l'utilisateur** dans la section des attentes.
      
         > [!div class="mx-imgBorder"]
         > ![Créer une réponse pour un paramètre obligatoire](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifiez la règle d'interaction **Confirmation réussie** pour gérer une confirmation réussie (l’utilisateur a répondu yes).
      
          1. Remplacez **Nom** par **`Confirmation temperature succeeded`** .
          1. Conservez la condition existante **Confirmation réussie**.
          1. Ajoutez une nouvelle condition en sélectionnant **Type >  Paramètres obligatoires > Température**
          1. Conservez la valeur par défaut de **État après exécution** sur **Exécuter les règles d’exécution**.

    1. Modifiez la valeur **Confirmation refusée** (l’utilisateur a répondu yes) pour gérer les scénarios où une confirmation est refusée.

          1. Remplacez **Nom** par **`Confirmation temperature denied`** .
          1. Conservez la condition existante **Confirmation refusée**.
          1. Ajoutez une nouvelle condition en sélectionnant **Type >  Paramètres obligatoires > Température**
          1. Ajoutez une nouvelle action en sélectionnant **Type > Envoyer une réponse vocale > `No problem. What temperature then?`**
          1. Conservez la valeur par défaut de **État après exécution** sur **Attente d'entrée de l'utilisateur**.

> [!IMPORTANT]
> Dans cet article, vous avez utilisé la fonction de confirmation intégrée. Vous pouvez également obtenir le même résultat en ajoutant les règles d’interaction une par une, manuellement.
   

### <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez **Former**, attendez la fin de l'apprentissage, puis sélectionnez **Tester**.

- Entrée : régler la température sur 80 degrés
- Sortie : ok 80 ?
- Entrée : Non
- Sortie : aucun problème. Quelle température ?
- Entrée : 83 degrés
- Sortie : ok 83 ?
- Entrée : Oui
- Sortie : Régler la température sur 83 degrés


## <a name="implementing-corrections-in-a-command"></a>Implémentation de corrections dans une commande

Dans cette section, vous allez configurer la correction en une étape, effectuée une fois que l’action d’exécution a déjà été exécutée. Vous pouvez également afficher un exemple de la façon dont la correction est activée par défaut si la commande n’est pas encore terminée. Pour ajouter une correction lorsque la commande n’est pas terminée, ajoutez un nouveau paramètre **AlarmTone**.

Sélectionnez la commande **SetAlarm** dans le volet gauche, puis ajoutez un nouveau paramètre **AlarmTone**.
        
- **Nom** > `AlarmTone`
- **Type** > Chaîne
- **Valeur par défaut** > `Chimes`
- **Configuration** > Accepter les valeurs d’entrée prédéfinies du catalogue interne
- **Valeurs d’entrée prédéfinies** > `Chimes`, `Jingle` et `Echo`. Chacune en tant qu’entrées prédéfinies individuelles.


Ensuite, mettez à jour la réponse pour le paramètre DateTime avec la valeur `Ready to set alarm with tone as {AlarmTone}. For what time?`. Modifiez ensuite la règle d’exécution comme suit.

1. Sélectionnez la règle d’exécution **ConfirmationResponse** existante.
1. Dans le volet droit, pointez sur l’action existante, puis sélectionnez le bouton **Modifier**.
1. Mettez à jour la réponse vocale avec la valeur `Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez **Former**, attendez la fin de l'apprentissage, puis sélectionnez **Tester**.
Testez les énoncés suivants :

- Entrée : régler une alarme
- Sortie : Prêt à régler l’alarme avec une tonalité carillon. Pour quelle heure ?
- Entrée : régler une alarme avec une tonalité jingle pour 9 h demain matin
- Sortie : Ok, alarme réglée pour 30-05-2020 09:00:00. La tonalité de l’alarme est jingle.

> [!IMPORTANT]
> Notez que la tonalité d’alarme peut être modifiée sans configuration explicite dans une commande en cours, c’est-à-dire lorsque la commande n’est pas encore terminée. **La correction est activée par défaut pour tous les paramètres de la commande, quel que soit le nombre de retours si la commande n’est pas encore terminée.**

### <a name="correction-when-command-is-completed"></a>Correction lorsque la commande est terminée

La plateforme Commandes personnalisées permet également d’effectuer une correction en une étape même lorsque la commande est terminée. Toutefois, cette fonctionnalité n’est pas activée par défaut et doit être configurée de manière explicite. Procédez comme suit pour configurer la correction en une étape.

1. Dans la commande **SetAlarm**, ajoutez une règle d’interaction de type **Mettre à jour la commande précédente** pour mettre à jour l’alarme précédemment définie. Renommez la valeur **Nom** par défaut de la règle d’interaction en **Mettre à jour l’alarme précédente**.
1. Conservez la condition par défaut **La commande précédente doit être mise à jour**.
1.  Ajoutez une nouvelle condition en sélectionnant **Type > Paramètres obligatoires > DateHeure**.
1. Ajoutez une nouvelle action en sélectionnant **Type > Envoyer une réponse vocale > Éditeur simple > `Updating previous alarm time to {DateTime}.`**
1. Conservez la valeur par défaut **Commande terminée** de l’état après exécution.

### <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez **Former**, attendez la fin de l'apprentissage, puis sélectionnez **Tester**.

- Entrée : régler une alarme
- Sortie : Prêt à régler l’alarme avec une tonalité carillon. What time?
- Entrée : régler une alarme avec une tonalité jingle pour 9 h demain matin
- Sortie : Ok, alarme réglée pour 21-05-2020 09:00:00. La tonalité de l’alarme est jingle.
- Entrée : non, 8 heures
- Sortie : Mise à jour de l’alarme précédente à 29-05-2020 08:00.

> [!NOTE]
> Dans une application réelle, dans la section Actions de cette règle de correction, vous devrez également renvoyer une activité au client ou appeler un point de terminaison HTTP pour mettre à jour l’heure de l’alarme dans votre système. Cette action doit être seule responsable de la mise à jour de l’heure de l’alarme et non de tout autre attribut de la commande, une tonalité d’alarme dans ce cas.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Guide pratique pour Ajouter des modèles de génération de langage pour les réponses vocales](./how-to-custom-commands-add-language-generation-templates.md)