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
ms.openlocfilehash: f37109cc2677ad5ef18c5677bda9308a78cebccf
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851314"
---
# <a name="add-interaction-rules"></a>Ajouter des règles d’interaction

Cet article vous fournit des informations sur les *règles d’interaction*. Ces règles supplémentaires gèrent des situations plus spécifiques ou complexes. Vous pouvez créer vos propres règles d’interaction personnalisées, mais dans cet article, vous utilisez des règles d’interaction pour les scénarios ciblés suivants :

* Confirmation de commandes
* Ajout d’une correction en une étape à des commandes

Accédez à la section [Références](./custom-commands-references.md) pour en savoir plus sur les règles d’interaction.

## <a name="prerequisites"></a>Prérequis

Vous devez avoir effectué les étapes décrites dans les articles suivants :
> [!div class="checklist"]
> * [Créer une application avec des commandes simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Ajouter des paramètres aux commandes](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Ajouter des confirmations à une commande

Pour ajouter une confirmation, utilisez la commande **SetTemperature**. Pour obtenir une confirmation, vous créez des règles d’interaction en procédant comme suit.

1. Sélectionnez la commande **SetTemperature** dans le volet gauche.
1. Ajoutez des règles d’interaction en sélectionnant **Ajouter** dans le volet central. Sélectionnez ensuite **Règles d’interaction** > **Confirmer la commande**.

    Cette action ajoute trois règles d’interaction. Cette règle demande à l’utilisateur de confirmer la date et l’heure de l’alarme et attend une confirmation (yes/no) pour le tour suivant.

    1. Modifiez la règle d’interaction **Confirmer la commande** conformément à la configuration suivante :
        1. Renommez le **Nom** sur **Confirmer la température**.
        1. Ajoutez une nouvelle condition en sélectionnant **Paramètres obligatoires** > **Température**.
        1. Ajoutez une nouvelle action en sélectionnant **Type** > **Envoyer une réponse vocale** > **Voulez-vous vraiment définir la température sur {Temperature} degrés ?**
        1. Conservez la valeur par défaut **Attente de confirmation de l'utilisateur** dans la section **Attentes**.
      
         > [!div class="mx-imgBorder"]
         > ![Créer une réponse pour un paramètre obligatoire](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modifiez la règle d'interaction **Confirmation réussie** pour gérer une confirmation réussie (l’utilisateur a répondu yes).
      
          1. Modifiez le **Nom** sur **Confirmation de la température réussie**.
          1. Conservez la condition existante **Confirmation réussie**.
          1. Ajoutez une nouvelle condition en sélectionnant **Type** > **Paramètres obligatoires** > **Température**.
          1. Conservez la valeur par défaut de **État après exécution** sur **Exécuter les règles d’exécution**.

    1. Modifiez la règle d’interaction **Confirmation refusée** pour gérer les scénarios où une confirmation est refusée (l’utilisateur a répondu non).

          1. Modifiez le **Nom** sur **Confirmation de la température réfusée**.
          1. Conservez la condition existante **Confirmation refusée**.
          1. Ajoutez une nouvelle condition en sélectionnant **Type** > **Paramètres obligatoires** > **Température**.
          1. Ajoutez une nouvelle action en sélectionnant **Type** > **Envoyer une réponse vocale** > **Aucun problème. Quelle température ?**
          1. Conservez la valeur par défaut de **État après exécution** sur **Attente d'entrée de l'utilisateur**.

> [!IMPORTANT]
> Dans cet article, vous avez utilisé la fonction de confirmation intégrée. Vous pouvez également ajouter manuellement des règles d’interaction une par une.
   

### <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez **Former**, attendez la fin de la formation, puis sélectionnez **Tester**.

- **Entrée**: Régler la température sur 80 degrés.
- **Sortie**: OK 80 ?
- **Entrée**: Non.
- **Sortie**: Pas de problème. Quelle température ?
- **Entrée**: 83 degrés.
- **Sortie**: OK 83 ?
- **Entrée**: Oui.
- **Sortie**: OK, régler la température sur 83 degrés.


## <a name="implement-corrections-in-a-command"></a>Implémenter des corrections dans une commande

Dans cette section, vous configurez une correction en une étape, qui est utilisée une fois que l’action d’exécution a déjà été exécutée. Vous pouvez également afficher un exemple de la façon dont une correction est activée par défaut si la commande n’est pas encore exécutée. Pour ajouter une correction lorsque la commande n’est pas terminée, ajoutez le nouveau paramètre **AlarmTone**.

Sélectionnez la commande **SetAlarm** dans le volet gauche, puis ajoutez le nouveau paramètre **AlarmTone**.
        
- **Nom** > **AlarmTone**
- **Type** > **Chaîne**
- **Valeur par défaut** > **Carillon**
- **Configuration** > **Accepter les valeurs d’entrée prédéfinies du catalogue interne**
- **Valeurs d’entrée prédéfinies** > **Carillon**, **Jingle** et **Écho** en tant qu’entrées prédéfinies individuelles


Ensuite, mettez à jour la réponse pour le paramètre **DateTime** sur **Prêt à régler l’alarme avec une tonalité {AlarmTone}. Pour quelle heure ?** . Modifiez ensuite la règle d’exécution comme suit :

1. Sélectionnez la règle d’exécution **ConfirmationResponse** existante.
1. Dans le volet droit, pointez sur l’action existante et sélectionnez **Modifier**.
1. Mettez à jour la réponse vocale sur **OK, alarme définie pour {DateTime}. La tonalité de l’alarme est {AlarmTone}.**

### <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez **Former**, attendez la fin de la formation, puis sélectionnez **Tester**.
Testez les énoncés suivants :

- **Entrée**: Régler une alarme.
- **Sortie**: Prêt à régler l’alarme avec une tonalité carillon. Pour quelle heure ?
- **Entrée**: Régler une alarme avec la tonalité Jingle pour 9 h demain matin.
- **Sortie**: OK, alarme réglée pour 30-05-2020 09:00:00. La tonalité de l’alarme est Jingle.

> [!IMPORTANT]
> La tonalité de l’alarme peut être modifiée sans configuration explicite dans une commande en cours, par exemple, lorsque la commande n’est pas encore terminée. *Une correction est activée par défaut pour tous les paramètres de la commande, quel que soit le nombre de tours si la commande n’est pas encore terminée.*

### <a name="correction-when-command-is-completed"></a>Correction lorsque la commande est terminée

La plateforme Commandes personnalisées permet également d’effectuer une correction en une étape même lorsque la commande est terminée. Cette fonctionnalité n’est pas activée par défaut. Elle doit être configurée de manière explicite. Procédez comme suit pour configurer une correction en une étape.

1. Dans la commande **SetAlarm**, ajoutez une règle d’interaction de type **Mettre à jour la commande précédente** pour mettre à jour l’alarme précédemment définie. Renommez la valeur **Nom** par défaut de la règle d’interaction en **Mettre à jour l’alarme précédente**.
1. Conservez la condition par défaut **La commande précédente doit être mise à jour**.
1. Ajoutez une nouvelle condition en sélectionnant **Type** > **Paramètre obligatoire** > **DateHeure**.
1. Ajoutez une nouvelle action en sélectionnant **Type** > **Envoyer une réponse vocale** > **Éditeur simple** > **Mise à jour de l’heure d’alarme précédente sur {DateTime}.**
1. Pour **État après exécution**, conservez la valeur par défaut **Commande terminée**.

### <a name="try-out-the-changes"></a>Essayer les modifications

Sélectionnez **Former**, attendez la fin de la formation, puis sélectionnez **Tester**.

- **Entrée**: Régler une alarme.
- **Sortie**: Prêt à régler l’alarme avec une tonalité carillon. Pour quelle heure ?
- **Entrée**: Régler une alarme avec la tonalité Jingle pour 9 h demain matin.
- **Sortie**: OK, alarme réglée pour 21-05-2020 09:00:00. La tonalité de l’alarme est Jingle.
- **Entrée**: Non, 8 heures.
- **Sortie**: Mise à jour de l’alarme précédente à 29-05-2020 08:00.

> [!NOTE]
> Dans une application réelle, dans la section **Actions** de cette règle de correction, vous devrez également renvoyer une activité au client ou appeler un point de terminaison HTTP pour mettre à jour l’heure de l’alarme dans votre système. Cette action doit être seule responsable de la mise à jour de l’heure de l’alarme et non de tout autre attribut de la commande. Dans ce cas, il s’agirait de la tonalité de l’alarme.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Procédure : Ajouter des modèles de génération de langage pour les réponses vocales](./how-to-custom-commands-add-language-generation-templates.md)
