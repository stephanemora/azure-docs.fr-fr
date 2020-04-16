---
title: Migrer de la bibliothèque d’exécuteur en bloc vers la prise en charge de l’exécution en bloc dans le SDK Azure Cosmos DB .NET v3
description: Découvrez comment migrer votre application de l’utilisation de la bibliothèque d’exécuteur en bloc vers la prise en charge de l’exécution en bloc dans le SDK Azure Cosmos DB v3.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755978"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrer de la bibliothèque d’exécuteur en bloc vers la prise en charge de l’exécution en bloc dans le SDK Azure Cosmos DB .NET v3

Cet article décrit les étapes nécessaires pour migrer le code d’une application existante qui utilise la [bibliothèque d’exécuteur en bloc .NET](bulk-executor-dot-net.md) vers la fonctionnalité de [prise en charge de l’exécution en bloc](tutorial-sql-api-dotnet-bulk-import.md) de la dernière version du SDK .NET.

## <a name="enable-bulk-support"></a>Activer la prise en charge de l’exécution en bloc

Activez la prise en charge de l’exécution en bloc sur l’instance de `CosmosClient` par le biais de la configuration [AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Créer des tâches pour chaque opération

La prise en charge de l’exécution en bloc dans le SDK .NET fonctionne en tirant parti de la [bibliothèque parallèle de tâches](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) et des opérations de regroupement qui se produisent simultanément. 

Il n’existe pas de méthode unique qui prend votre liste de documents ou d’opérations comme paramètre d’entrée. Au lieu de cela, vous devez créer une tâche pour chaque opération que vous souhaitez exécuter en bloc.

Par exemple, si votre entrée initiale est une liste d’éléments où chaque élément a le schéma suivant :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Si vous souhaitez effectuer une importation en bloc (comme lors de l’utilisation de BulkExecutor.BulkImportAsync), vous devez effectuer des appels simultanés à `CreateItemAsync` avec chaque valeur d’élément. Par exemple :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Si vous souhaitez effectuer une *mise à jour* en bloc (comme lors de l’utilisation de [BulkExecutor.BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), vous devez effectuer des appels simultanés à la méthode `ReplaceItemAsync` après la mise à jour de la valeur de l’élément. Par exemple :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

Et si vous souhaitez effectuer une *suppression* en bloc (comme lors de l’utilisation de [BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), vous devez effectuer des appels simultanés à `DeleteItemAsync`, avec l’`id` et la clé de partition de chaque élément. Par exemple :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Capturer l’état du résultat de la tâche

Dans les exemples de code précédents, vous avez créé une liste de tâches simultanées et appelé la méthode `CaptureOperationResponse` sur chacune de ces tâches. Cette méthode est une extension qui nous permet de conserver un *schéma de réponse similaire* à BulkExecutor, en capturant les erreurs et en effectuant le suivi de l’[utilisation des unités de requête](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Où `OperationResponse` est déclaré comme suit :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Exécuter des opérations simultanément

Une fois la liste des tâches définie, patientez jusqu’à ce qu’elles soient toutes terminées. Vous pouvez suivre l’achèvement des tâches en définissant l’étendue de votre opération en bloc comme indiqué dans l’extrait de code suivant :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Capturer les statistiques

Le code précédent attend que toutes les opérations soient terminées et calcule les statistiques requises. Ces statistiques sont similaires à celles de la classe [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse) de la bibliothèque d’exécuteur en bloc.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse` contient les éléments suivants :

1. Temps total nécessaire pour traiter la liste des opérations par le biais de la prise en charge de l’exécution en bloc
1. Nombre d’opérations réussies
1. Nombre total d’unités de requête consommées
1. En cas d’échec, il affiche une liste de tuples qui contiennent l’exception et l’élément associé à des fins de journalisation et d’identification

## <a name="retry-configuration"></a>Configuration de nouvelle tentative

La bibliothèque d’exécuteur en bloc contenait des [recommandations](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) indiquant qu’il fallait affecter la valeur `0` aux paramètres `MaxRetryWaitTimeInSeconds` et `MaxRetryAttemptsOnThrottledRequests` de [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) pour déléguer le contrôle à la bibliothèque.

Pour la prise en charge de l’exécution en bloc dans le SDK .NET, il n’existe aucun comportement masqué. Vous pouvez configurer les options de nouvelle tentative directement par le biais de [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) et [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Dans les cas où les unités de requête provisionnées sont beaucoup plus faibles que celles attendues d’après la quantité de données, vous pouvez les définir sur des valeurs élevées. L’opération en bloc prendra plus de temps, mais elle aura plus de chances de se terminer correctement en raison du nombre plus élevé de nouvelles tentatives.

## <a name="performance-improvements"></a>Optimisation des performances

Comme pour d’autres opérations effectuées avec le SDK .NET, l’utilisation des API de flux améliore les performances et évite toute sérialisation inutile. 

L’utilisation des API de flux n’est possible que si la nature des données que vous utilisez correspond à celle d’un flux d’octets (par exemple des flux de fichiers). Dans ce cas, l’utilisation des méthodes `CreateItemStreamAsync`, `ReplaceItemStreamAsync` ou `DeleteItemStreamAsync` et l’utilisation de `ResponseMessage` (au lieu de `ItemResponse`) augmente le débit qui peut être atteint.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les versions du SDK .NET, consultez l’article [Kit SDK Azure Cosmos DB](sql-api-sdk-dotnet.md).
* Procurez-vous le [code source de migration](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) complet sur le site web de GitHub.
* [Exemples supplémentaires d’exécution en bloc sur GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
