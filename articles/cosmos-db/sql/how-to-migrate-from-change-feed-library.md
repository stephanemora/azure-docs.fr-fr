---
title: Migrer de la bibliothèque du processeur de flux de modification vers le kit de développement logiciel (SDK) Azure Cosmos DB .NET V3
description: Découvrez comment migrer votre application à l’aide de la bibliothèque du processeur de flux de modification vers le kit de développement logiciel (SDK) Azure Cosmos DB V3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ad060c3fce28ef70137e0f25e09a1e4ea5fb9a09
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561475"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrer de la bibliothèque du processeur de flux de modification vers le kit de développement logiciel (SDK) Azure Cosmos DB .NET V3
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cet article décrit la procédure de migration d'un code d'application existante utilisant la [bibliothèque du processeur de flux de modification](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) vers la fonctionnalité de [flux de modification](../change-feed.md) de la dernière version du kit de développement logiciel (SDK) .NET (également appelé kit de développement logiciel (SDK) .NET V3).

## <a name="required-code-changes"></a>Modifications de code requises

Le kit de développement logiciel (SDK) .NET V3 présente plusieurs changements cassants et vous trouverez ci-dessous les principales étapes pour migrer votre application :

1. Convertissez les instances `DocumentCollectionInfo` en références `Container` pour les conteneurs surveillés et de baux.
1. Les personnalisations utilisant `WithProcessorOptions` doivent être mises à jour pour utiliser `WithLeaseConfiguration` et `WithPollInterval` pour les intervalles, `WithStartTime` [pour l'heure de début](./change-feed-processor.md#starting-time) et `WithMaxItems` pour définir le nombre maximal d’éléments.
1. Définissez `processorName` sur `GetChangeFeedProcessorBuilder` à des fins de correspondance avec la valeur configurée sur `ChangeFeedProcessorOptions.LeasePrefix` ou utilisez `string.Empty`.
1. Les modifications ne sont plus fournies sous forme de `IReadOnlyList<Document>`, mais de `IReadOnlyCollection<T>` où `T` correspond à un type que vous devez définir. Il n'existe plus de classe d'élément de base.
1. Pour gérer les modifications, vous n’avez plus besoin d’une implémentation de `IChangeFeedObserver` , mais vous devez [définir un délégué](change-feed-processor.md#implementing-the-change-feed-processor). Le délégué peut être une fonction statique ou, s'il vous faut maintenir l'état entre les exécutions vous pouvez créer votre propre classe et transmettre une méthode d’instance en tant que délégué.

Par exemple, si le code d’origine pour créer le processeur de flux de modification se présente comme suit :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Le code migré se présentera comme suit :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

Pour le délégué, vous pouvez avoir une méthode statique pour recevoir les événements. Si vous avez consommé des informations à partir du `IChangeFeedObserverContext` vous pouvez migrer pour utiliser le `ChangeFeedProcessorContext` :

* `ChangeFeedProcessorContext.LeaseToken` peut être utilisé à la place de `IChangeFeedObserverContext.PartitionKeyRangeId`
* `ChangeFeedProcessorContext.Headers` peut être utilisé à la place de `IChangeFeedObserverContext.FeedResponse`
* `ChangeFeedProcessorContext.Diagnostics` contient des informations détaillées sur la latence des demandes de résolution des problèmes

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>État et conteneur de baux

À l’instar de la bibliothèque du processeur de flux de modification, la fonctionnalité de flux de modification du kit de développement logiciel (SDK) .NET V3 utilise un [conteneur de baux](change-feed-processor.md#components-of-the-change-feed-processor) pour stocker l’état. Toutefois, les schémas sont différents.

Le processeur de flux de modification du kit de développement logiciel (SDK) V3 détecte tout état de bibliothèque antérieur et le migre automatiquement vers le nouveau schéma lors de la première exécution du code de l’application migrée. 

Vous pouvez arrêter en toute sécurité l’application à l’aide de l’ancien code, migrer le code vers la nouvelle version, démarrer l’application migrée, après quoi toutes les modifications apportées pendant l’arrêt de l’application sont récupérées et traitées par la nouvelle version.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Exemples d’utilisation sur GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Exemples supplémentaires sur GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processeur de flux de modification, consultez les articles suivants :

* [Vue d’ensemble du processeur de flux de modification](change-feed-processor.md)
* [Utilisation de l’estimateur de flux de modification](how-to-use-change-feed-estimator.md)
* [Heure de début du processeur de flux de modification](./change-feed-processor.md#starting-time)
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ?
    * Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
    * Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)