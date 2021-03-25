---
title: Planifier l’exécution des indexeurs
titleSuffix: Azure Cognitive Search
description: Planifiez des indexeurs Recherche cognitive Azure pour indexer le contenu à intervalles périodiques ou à des moments donnés.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097974"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Comment planifier des indexeurs dans la Recherche cognitive Azure

Un indexeur s’exécute normalement une fois, immédiatement après sa création. Ensuite, vous pouvez le réexécuter à la demande à l’aide du portail Azure, de l’[indexeur d’exécution (REST)](/rest/api/searchservice/run-indexer) ou d’un Kit de développement logiciel (SDK) Azure. Vous pouvez également configurer un indexeur pour qu’il s’exécute selon une planification. Voici des situations où la planification d’un indexeur est utile :

* Les données sources changent au fur et à mesure, et vous souhaitez que l’indexeur de recherche traite automatiquement les différences.

* Les données sources sont très volumineuses et vous souhaitez étaler le traitement de l’indexeur dans le temps. Les tâches de l’indexeur sont soumises à une durée d’exécution maximale de 24 heures pour les sources de données normales, et à 2 heures pour les indexeurs avec ensembles de compétences. Si l’indexation ne peut pas se terminer dans l’intervalle maximal, vous pouvez configurer une planification qui s’exécute toutes les 2 heures. Les indexeurs peuvent reprendre automatiquement là où ils se sont arrêtés, comme le prouve la présence d’une borne haute interne marquant l’emplacement où la dernière indexation s’est terminée. L’exécution d’un indexeur lors d’une planification récurrente de 2 heures lui permet de traiter un jeu de données très volumineux (plusieurs millions de documents) au-delà de l’intervalle autorisé pour une même tâche. Pour plus d’informations sur l’indexation de grands volumes de données, consultez [Comment indexer des grands volumes de données dans la Recherche cognitive Azure](search-howto-large-index.md).

* Un index de recherche est généré à partir de plusieurs sources de données et vous souhaitez que les indexeurs s’exécutent à des moments différents afin de réduire les conflits.

Visuellement, une planification peut se présenter comme suit : exécution à partir du 1er janvier, toutes les 50 minutes.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Le planificateur est une fonctionnalité intégrée à la Recherche cognitive Azure. Il n’existe aucune prise en charge de planificateurs externes.

## <a name="schedule-property"></a>Propriété schedule

Une planification fait partie de la définition d’un indexeur. En cas d’omission de la propriété **schedule**, l’indexeur ne s’exécute qu’une seule fois immédiatement après sa création. Si vous ajoutez une propriété **schedule**, vous spécifiez deux parties.

| Propriété | Description |
|----------|-------------|
|**Intervalle** | (facultatif) Intervalle de temps s’écoulant entre le début de deux exécutions consécutives de l’indexeur. L’intervalle de temps le plus court autorisé est de 5 minutes, et le plus long de 1440 minutes (24 heures). Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. <br/><br/>Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.|
| **Heure de début (UTC)** | (facultatif) Indique quand les exécutions planifiées doivent commencer. En cas d’omission, l’heure UTC actuelle est utilisée. Cette heure peut être passée, auquel cas la première exécution est planifiée comme si l’indexeur s’exécutait en continu depuis l’**heure de début** originale.<br/><br/>Exemples : `2021-01-01T00:00:00Z` à partir du 1er janvier à minuit, `2021-01-05T22:28:00Z` à partir du 5 janvier à 20h28 .|

## <a name="scheduling-behavior"></a>Comportement de planification

L’indexeur ne peut s’exécuter qu’une seule fois simultanément. Si un indexeur est déjà en cours d’exécution au moment de sa deuxième exécution planifiée, celle-ci est différée jusqu’à la prochaine date planifiée.

Pour être plus clair, prenons un exemple. Supposons que nous configurons une planification de l’indexeur avec un **intervalle** par heure et une **heure de début** le 1er juin 2019 à 08:00:00 UTC. Voici ce qui peut se produire lorsque l’exécution de l’indexeur prend plus d’une heure :

* La première exécution de l’indexeur commence à ou autour du 1er juin 2019 à 8h00 UTC. Supposons que cette exécution prend 20 minutes (ou en tout cas, moins de 1 heure).
* La deuxième exécution de l’indexeur commence à ou autour du 1er juin 2019 à 9h00 UTC. Supposons que cette exécution dure 70 minutes (plus d’une heure), et qu’elle ne se terminera pas avant 10h10 UTC.
* La troisième exécution est planifiée pour démarrer à 10h00 UTC, mais à ce moment l’exécution précédente est toujours en cours. Cette exécution planifiée est donc ignorée. La prochaine exécution de l’indexeur ne va pas démarrer avant 11h00 UTC.

> [!NOTE]
> Si un indexeur est défini sur une certaine planification, mais échoue à plusieurs reprises sur le même document, l’indexeur commence à s’exécuter à un intervalle moins fréquent (jusqu’à l’intervalle maximal d’au moins une fois toutes les 24 heures) jusqu’à ce qu’il progresse correctement à nouveau. Si vous pensez que vous avez résolu le problème sous-jacent, vous pouvez exécuter l’indexeur manuellement et, si l’indexation réussit, l’indexeur revient à sa planification habituelle.

## <a name="schedule-using-rest"></a>Planifier à l'aide de REST

Spécifiez la propriété **schedule** lors de la création ou de la mise à jour de l’indexeur.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Planifier à l'aide de .NET

L’exemple C# suivant crée un indexeur de base de données Azure SQL en utilisant une source de données et un index prédéfinis, et définit sa planification de manière à ce qu’il s’exécute une fois par jour à partir de maintenant :

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

La planification est définie à l’aide de la classe [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) lorsque vous créez ou mettez à jour un indexeur à l’aide de [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Le constructeur **IndexingSchedule** requiert un paramètre **Interval** spécifié à l’aide d’un objet **TimeSpan**. Comme indiqué précédemment, la plus petite valeur d’intervalle autorisée est de 5 minutes, et la plus grande de 24 heures. Le second paramètre **StartTime**, spécifié comme un objet **DateTimeOffset**, est facultatif.

## <a name="next-steps"></a>Étapes suivantes

Pour les indexeurs qui s’exécutent selon une planification, vous pouvez surveiller les opérations en extrayant l’état du service de recherche, ou obtenir des informations détaillées en activant la journalisation des diagnostics.

* [Surveiller l’état de l’indexeur de recherche](search-howto-monitor-indexers.md)
* [Collecter et analyser des données de journal](search-monitor-logs.md)