---
title: Résolution des problèmes d’entrées pour Azure Stream Analytique
description: Cet article décrit des techniques pour résoudre les problèmes liés aux connexions d’entrée dans les travaux Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 20a161ffc82cb8f74cfcac838856434f83c4e258
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354279"
---
# <a name="troubleshoot-input-connections"></a>Résoudre les problèmes liés aux connexions d’entrée

Cette page décrit les problèmes courants avec les connexions d’entrée et comment les résoudre.

## <a name="input-events-not-received-by-job"></a>Événements d’entrée non reçus par le travail 
1.  Testez votre connectivité. Vérifiez la connectivité en entrée et en sortie à l’aide du bouton **Tester la connexion** pour chacune des entrées et des sorties.

2.  Examinez vos données d’entrée.

    Utilisez [l’Explorateur Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) pour établir la connexion à Azure Event Hub (si l’entrée Event Hub est utilisée) afin de vérifier que les données d’entrée sont communiquées à Event Hub.
        
    Utilisez le bouton [**Exemple de données**](stream-analytics-sample-data-input.md) pour chacune des entrées, puis téléchargez l’exemple de données d’entrée.
        
    Examinez l’exemple de données afin d’appréhender la forme des données : le schéma et les [types de données](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Les événements d’entrée incorrectement formés provoquent des erreurs de désérialisation 
Les problèmes de désérialisation proviennent de la présence de messages incorrectement formés dans le flux d’entrée de votre travail Stream Analytics. Par exemple, un message incorrectement formé peut être provoqué par une parenthèse ou une accolade manquante dans un objet JSON ou par un format d’horodatage incorrect dans le champ d’heure. 
 
Quand un travail Stream Analytics reçoit un message incorrectement formé d’une entrée, il dépose le message et vous envoie un avertissement. Un symbole d’avertissement s’affiche dans la vignette **Entrées** de votre travail Stream Analytics. Ce signe d’avertissement s’affiche tant que le travail est en cours d’exécution :

![Vignette d’entrées Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Activez les journaux de diagnostic pour afficher les détails de l’avertissement. Pour les événements d’entrée incorrectement formés, les journaux d’activité d’exécution contiennent une entrée avec un message semblable à celui-ci : 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Problème à l’origine de l’erreur de désérialisation
Vous pouvez effectuer les étapes suivantes pour analyser les événements d’entrée en détail et bien comprendre ce qui a provoqué l’erreur de désérialisation. Vous pouvez ensuite corriger la source de l’événement pour générer des événements au bon format et éviter que ce problème ne se reproduise.

1. Accédez à la vignette d’entrée, puis cliquez sur les symboles d’avertissement pour afficher la liste des problèmes.

2. La vignette Détails de l’entrée affiche la liste des avertissements avec des informations détaillées sur chaque problème. L’exemple de message d’avertissement ci-dessous indique la partition, le décalage et les numéros de séquence où se trouvent des données JSON incorrectement formées. 

   ![Message d’avertissement Stream Analytics avec décalage](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Pour trouver les données JSON dont le format est incorrect, exécutez le code CheckMalformedEvents.cs disponible dans le [dépôt d’exemples GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ce code lit l’ID de la partition et le décalage, puis imprime les données situées dans ce décalage. 

4. Une fois que vous avez lu les données, vous pouvez analyser et corriger le format de sérialisation.

5. Vous pouvez également [lire les événements à partir d’un IoT Hub avec l’Explorateur Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Le travail dépasse le nombre maximum de récepteurs Event Hub
Une bonne pratique concernant l’utilisation d’Event Hubs consiste à utiliser plusieurs groupes de consommateurs pour garantir l’extensibilité du travail. Le nombre de lecteurs dans le travail Stream Analytics pour une entrée spécifique affecte le nombre de lecteurs dans un groupe de consommateurs donné. Le nombre précis de récepteurs repose sur les détails d’implémentation interne de la logique de la topologie Scale-out. Il n’est pas exposé en externe. Le nombre de lecteurs peut changer au démarrage du travail ou pendant les mises à niveau du travail.

Quand le nombre de récepteurs dépasse la valeur maximale, l’erreur suivante s’affiche : `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Lorsque le nombre de lecteurs change lors des mises à niveau du travail, des avertissements temporaires sont journalisés dans des journaux d’audit. Les travaux Stream Analytics récupèrent automatiquement de ces problèmes temporaires.

### <a name="add-a-consumer-group-in-event-hubs"></a>Ajouter un groupe de consommateurs dans Event Hubs
Pour ajouter un nouveau groupe de consommateurs dans votre instance Event Hubs, procédez comme suit :

1. Connectez-vous au portail Azure.

2. Localisez votre instance Event Hubs.

3. Sous le titre **Entités**, sélectionnez **Event Hubs**.

4. Sélectionnez le nom du Event Hub.

5. Dans la page **Instance Event Hubs**, sous le titre **Entités**, sélectionnez **Groupes de consommateurs**. Un groupe de consommateurs nommé **$Default** est répertorié.

6. Sélectionnez **+ Groupe de consommateurs** pour ajouter un nouveau groupe de consommateurs. 

   ![Ajouter un groupe de consommateurs dans Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quand vous avez créé l’entrée pointant vers le Event Hub dans la tâche Stream Analytics, vous avez spécifié le groupe de consommateurs. $Default est utilisé quand aucun groupe n’est spécifié. Lorsque vous créez un nouveau groupe de consommateurs, modifiez l’entrée Event Hub dans la tâche Stream Analytics et spécifiez le nom du nouveau groupe de consommateurs.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Le nombre de lecteurs par partition dépasse la limite Event Hubs

Si la syntaxe de votre requête de diffusion en continu référence plusieurs fois la même ressource Event Hub d’entrée, le moteur de la tâche peut utiliser plusieurs lecteurs de ce même groupe de consommateurs. Quand les références à un même groupe de consommateurs sont trop nombreuses, la tâche est susceptible de dépasser la limite de cinq et de lever une erreur. Dans ce cas de figure, vous pouvez diviser votre requête en utilisant plusieurs entrées pour différents groupes de consommateurs à l’aide de la solution décrite dans la section suivante. 

Voici les scénarios dans lesquels le nombre de lecteurs par partition dépasse la limite de 5 dans Event Hubs :

* Plusieurs instructions SELECT : si vous utilisez plusieurs instructions SELECT qui font référence à la **même** entrée de hub d’événements, chaque instruction SELECT entraîne la création d’un destinataire.
* UNION : quand vous utilisez une opération UNION, il est possible d’avoir plusieurs entrées qui font référence aux **mêmes** hub d’événements et groupe de consommateurs.
* SELF JOIN : quand vous utilisez une opération SELF JOIN, il est possible de faire référence plusieurs fois au **même** hub d’événements.

Les meilleures pratiques suivantes peuvent aider à atténuer les scénarios dans lesquels le nombre de lecteurs par partition dépasse la limite de 5 dans Event Hubs.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Fractionner votre requête en plusieurs étapes à l’aide d’une clause WITH

Cette clause spécifie un jeu de résultats nommé de façon temporaire qui peut être référencé par une clause FROM de la requête. Définissez la clause WITH dans l’étendue d’exécution d’une instruction SELECT unique.

Par exemple, au lieu de cette requête :

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Utilisez cette requête :

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Vérifiez que les entrées sont liées à différents groupes de consommateurs.

Pour les requêtes dans lesquelles au moins trois entrées sont connectées au même groupe de consommateurs Event Hubs, créez des groupes de consommateurs distincts. Vous devez dans ce cas créer d’autres entrées Stream Analytics.

## <a name="get-help"></a>Obtenir de l’aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
