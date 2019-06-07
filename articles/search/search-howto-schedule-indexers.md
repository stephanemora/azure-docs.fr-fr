---
title: Comment planifier des indexeurs - recherche Azure
description: Planifier des indexeurs Azure Search pour indexer le contenu périodiquement ou à des moments donnés.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755379"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Comment planifier des indexeurs pour Azure Search
Un indexeur s’exécute normalement une fois, immédiatement après sa création. Vous pouvez l’exécuter à nouveau à la demande avec le portail, l’API REST ou le kit SDK .NET. Vous pouvez également configurer un indexeur pour s’exécuter périodiquement selon une planification.

Certaines situations où il est utile de planification de l’indexeur :

* Source de données changera au fil du temps, et vous souhaitez que les indexeurs recherche Azure pour traiter automatiquement les données modifiées.
* L’index est renseignée à partir de plusieurs sources de données et vous souhaitez vous assurer que les indexeurs s’exécuter à des moments différents afin de réduire les conflits.
* La source de données est très volumineux et que vous souhaitez répartir l’indexeur au fil du temps de traitement. Pour plus d’informations sur l’indexation de grands volumes de données, consultez [comment indexer des jeux de données volumineux dans Azure Search](search-howto-large-index.md).

Le planificateur est une fonctionnalité intégrée d’Azure Search. Vous ne pouvez pas utiliser un planificateur externe pour contrôler les indexeurs de recherche.

## <a name="define-schedule-properties"></a>Définir les propriétés de la planification

Une planification de l’indexeur a deux propriétés :
* **Intervalle**, qui définit la quantité de temps entre les deux planifiée exécutions d’indexeur. Le plus petit intervalle autorisé est de 5 minutes, et le plus grand est 24 heures.
* **Démarrer l’heure UTC**, ce qui indique la première fois au niveau duquel l’indexeur doit être exécuté.

Vous pouvez spécifier une planification lorsque vous créez tout d’abord l’indexeur, ou en mettant à jour les propriétés de l’indexeur ultérieurement. Planifications de l’indexeur peuvent être définies à l’aide de la [portail](#portal), le [API REST](#restApi), ou le [.NET SDK](#dotNetSdk).

L’indexeur ne peut s’exécuter qu’une seule fois simultanément. Si un indexeur est déjà en cours d’exécution lors de sa prochaine exécution est planifiée, que l’exécution est différée jusqu'à ce que la prochaine heure planifiée.

Pour être plus clair, prenons un exemple. Supposons que nous configurer une planification de l’indexeur avec un **intervalle** de toutes les heures et un **l’heure de début** de 1 juin 2019 à 8 h 00:00 UTC. Voici ce qui peut se produire quand un exécution de l’indexeur prend une heure :

* La première exécution de l’indexeur commence à ou autour du 1 juin 2019 à 8 h 00 UTC. Supposons que cette exécution prend 20 minutes (ou en tout cas, moins de 1 heure).
* La deuxième exécution commence à ou autour du 1 juin 2019 9 h 00 UTC. Supposons que cette exécution dure 70 minutes - plus d’une heure, et il ne se terminera pas tant que 10:10:00 UTC.
* La troisième exécution est planifiée pour démarrer à 10 h 00 UTC, mais à ce moment l’exécution précédente est toujours en cours d’exécution. Cela a planifié l’exécution est ensuite ignorée. La prochaine exécution de l’indexeur ne démarrera pas tant que 11 h 00 UTC.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Définir une planification dans le portail

L’Assistant Importer des données dans le portail vous permet de définir la planification pour un indexeur lors de la création. Le paramètre de planification par défaut est **horaire**, ce qui signifie que l’indexeur s’exécute une fois qu’il est créé et s’exécute à nouveau toutes les heures par la suite.

Vous pouvez modifier le paramètre de planification pour **une fois** si vous ne souhaitez pas l’indexeur à exécuter à nouveau automatiquement, ou la valeur **quotidienne** pour exécuter une fois par jour. Affectez-lui la valeur **personnalisé** si vous souhaitez spécifier un intervalle différent ou une heure de début ultérieure spécifique.

Lorsque vous définissez la planification sur **personnalisé**, champs s’affichent pour vous permettre de spécifier le **intervalle** et **heure de début (UTC)** . Délai le plus court intervalle autorisé est de 5 minutes et la plus longue est de 1 440 minutes (24 heures).

   ![Planification de l’indexeur dans l’Assistant Importation de données paramètre](media/search-howto-schedule-indexers/schedule-import-data.png "planification de l’indexeur paramètre dans l’Assistant Importation de données")

Une fois un indexeur a été créé, vous pouvez modifier les paramètres de planification à l’aide du panneau d’édition de l’indexeur. Les champs de planification sont les mêmes que dans l’Assistant Importer des données.

   ![Définition de la planification dans le volet d’édition indexeur](media/search-howto-schedule-indexers/schedule-edit.png "définition de la planification dans le panneau de modification d’indexeur")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Définir une planification à l’aide de l’API REST

Vous pouvez définir la planification pour un indexeur à l’aide de l’API REST. Pour cela, incluez le **planification** propriété pendant la création ou la mise à jour de l’indexeur. L’exemple ci-dessous montre une demande PUT pour mettre à jour un indexeur existant :

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Le paramètre **interval** est obligatoire. Il correspond à la durée entre le début de deux exécutions consécutives de l’indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.

Le paramètre facultatif **startTime** indique quand les exécutions planifiées doivent commencer. S’il est omis, l’heure UTC actuelle est utilisée. Cette heure peut être dans le passé, auquel cas la première exécution est planifiée comme si l’indexeur s’exécutait en continu depuis la version d’origine **startTime**.

Vous pouvez également exécuter un indexeur à la demande à tout moment à l’aide de l’appel d’exécuter un indexeur. Pour plus d’informations sur l’exécution d’indexeurs et en définissant des planifications de l’indexeur, consultez [Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer), et [indexeur de la mise à jour](https://docs.microsoft.com/rest/api/searchservice/update-indexer) dans la référence d’API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Définir une planification à l’aide du SDK .NET

Vous pouvez définir la planification pour un indexeur à l’aide du SDK .NET Azure Search. Pour cela, incluez le **planification** propriété pendant la création ou la mise à jour un indexeur.

Ce qui suit C# exemple crée un indexeur, à l’aide d’une source de données prédéfinie et un index et définit sa planification pour exécuter une fois par jour à partir de 30 minutes à partir de maintenant :

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Si le **planification** paramètre est omis, l’indexeur s’exécute uniquement une fois immédiatement après sa création.

Le **startTime** paramètre peut être défini sur une heure dans le passé. Dans ce cas, la première exécution est planifiée comme si l’indexeur s’exécutait en continu depuis la donnée **startTime**.

La planification est définie à l’aide de la [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) classe. Le **IndexingSchedule** constructeur requiert une **intervalle** paramètre spécifié à l’aide un **TimeSpan** objet. La plus petite valeur d’intervalle autorisée est de 5 minutes, et le plus grand est 24 heures. La seconde **startTime** paramètre, spécifié comme un **DateTimeOffset** l’objet, est facultatif.

Le kit SDK .NET vous permet des opérations d’indexeur de contrôle à l’aide de la [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) classe et ses [indexeurs](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) propriété, qui implémente les méthodes à partir de la **IIndexersOperations**interface. 

Vous pouvez exécuter un indexeur à la demande à tout moment en utilisant l’une de le [exécuter](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), ou [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) méthodes.

Pour plus d’informations sur la création, la mise à jour et l’exécution des indexeurs, consultez [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
