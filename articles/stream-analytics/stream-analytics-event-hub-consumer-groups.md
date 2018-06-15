---
title: Résoudre les problèmes liés aux destinataires Event Hub dans Azure Stream Analytics
description: Cet article explique comment utiliser plusieurs groupes de consommateurs pour les entrées Event Hubs dans des tâches Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312954"
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Résoudre les problèmes liés aux destinataires Event Hub dans Azure Stream Analytics

Vous pouvez utiliser Azure Event Hubs dans Azure Stream Analytics pour recevoir ou générer des données à partir d’un travail. Une meilleure pratique pour l’utilisation de Event Hubs consiste à utiliser plusieurs groupes de consommateurs pour garantir l’extensibilité du travail. En effet, le nombre de lecteurs dans le travail Stream Analytics pour une entrée spécifique affecte le nombre de lecteurs dans un groupe de consommateurs donné. Le nombre précis de destinataires repose sur les détails d’implémentation interne de la logique de la topologie d’augmentation de la taille des instances. Le nombre de destinataires n’est pas exposé en externe. Le nombre de lecteurs peut changer au démarrage du travail ou pendant les mises à niveau du travail.

Quand le nombre de destinataires dépasse la valeur maximale, l’erreur suivante s’affiche : `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Lorsque le nombre de lecteurs change lors des mises à niveau du travail, des avertissements temporaires sont journalisés dans des journaux d’audit. Les travaux Stream Analytics récupèrent automatiquement de ces problèmes temporaires.

## <a name="add-a-consumer-group-in-event-hubs"></a>Ajouter un groupe de consommateurs dans Event Hubs
Pour ajouter un nouveau groupe de consommateurs dans votre instance Event Hubs, procédez comme suit :

1. Connectez-vous au portail Azure.

2. Localisez votre instance Event Hubs.

3. Sous le titre **Entités**, sélectionnez **Event Hubs**.

4. Sélectionnez le nom du Event Hub.

5. Dans la page **Instance Event Hubs**, sous le titre **Entités**, sélectionnez **Groupes de consommateurs**. Un groupe de consommateurs nommé **$Default** est répertorié.

6. Sélectionnez **+ Groupe de consommateurs** pour ajouter un nouveau groupe de consommateurs. 

   ![Ajouter un groupe de consommateurs dans Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quand vous avez créé l’entrée pointant vers le Event Hub dans la tâche Stream Analytics, vous avez spécifié le groupe de consommateurs. $Default est utilisé quand aucun groupe n’est spécifié. Lorsque vous créez un nouveau groupe de consommateurs, modifiez l’entrée Event Hub dans la tâche Stream Analytics et spécifiez le nom du nouveau groupe de consommateurs.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Le nombre de lecteurs par partition dépasse la limite de 5 définie dans Event Hubs.

Si la syntaxe de votre requête de diffusion en continu référence plusieurs fois la même ressource Event Hub d’entrée, le moteur de la tâche peut utiliser plusieurs lecteurs de ce même groupe de consommateurs. Quand les références à un même groupe de consommateurs sont trop nombreuses, la tâche est susceptible de dépasser la limite de cinq et de lever une erreur. Dans ce cas de figure, vous pouvez diviser votre requête en utilisant plusieurs entrées pour différents groupes de consommateurs à l’aide de la solution décrite dans la section suivante. 

Voici les scénarios dans lesquels le nombre de lecteurs par partition dépasse la limite de 5 dans Event Hubs :

* Plusieurs instructions SELECT : si vous utilisez plusieurs instructions SELECT qui font référence au **même** Event Hub d’entrée, chaque instruction SELECT entraîne la création d’un destinataire.
* UNION : lorsque vous utilisez une opération UNION, il est possible d’avoir plusieurs entrées qui font référence aux **mêmes** Event Hub et groupe de consommateurs.
* SELF JOIN : lorsque vous utilisez cette opération, il est possible de faire référence plusieurs fois au **même** Event Hub.

## <a name="solution"></a>Solution

Les meilleures pratiques suivantes peuvent aider à atténuer les scénarios dans lesquels le nombre de lecteurs par partition dépasse la limite de 5 dans Event Hubs.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Fractionner votre requête en plusieurs étapes à l’aide d’une clause WITH

Cette clause spécifie un jeu de résultats nommé de façon temporaire qui peut être référencé par une clause FROM de la requête. Définissez la clause WITH dans l’étendue d’exécution d’une instruction SELECT unique.

Par exemple, au lieu de cette requête :

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Utilisez cette requête :

```
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


## <a name="next-steps"></a>Étapes suivantes
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
