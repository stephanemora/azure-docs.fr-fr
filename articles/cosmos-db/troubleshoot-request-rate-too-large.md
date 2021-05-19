---
title: Résoudre des problèmes liés aux exceptions Taux de requêtes trop élevé Azure Cosmos DB
description: Découvrez comment diagnostiquer et corriger les exceptions Taux de requêtes trop élevé.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: ed75ad96346d4a98e947a6231714d19431aa2715
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109734363"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-429-exceptions"></a>Diagnostiquer et résoudre des problèmes liés aux exceptions Taux de requêtes Azure Cosmos DB trop élevé (429)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cet article contient des causes et des solutions connues pour différentes erreurs de code d’état 429 pour l’API SQL. Si vous utilisez l’API pour MongoDB, consultez l’article [Résoudre des problèmes courants dans l’API pour MongoDB](mongodb-troubleshoot.md) pour savoir comment déboguer le code d’état 16500.

Une exception « Taux de requêtes trop élevé », également connue sous le code d’erreur 429, indique que vos requêtes sur Azure Cosmos DB sont limitées.

Lorsque vous utilisez le débit approvisionné, vous définissez le débit, mesuré en unités de requête par seconde (RU/s), qui est requis pour votre charge de travail. Les opérations de base de données sur le service, telles que les lectures, les écritures et les requêtes, consomment un certain nombre d’unités de requête (RU). En savoir plus sur les [unités de requête](request-units.md).

Dans une seconde donnée, si les opérations consomment plus que les unités de requête approvisionnées, Azure Cosmos DB renverra une exception 429. Chaque seconde, le nombre d’unités de requête disponibles est réinitialisé.

Avant de prendre une mesure pour modifier le RU/s, il est important de comprendre la cause racine de la limitation du débit et de résoudre le problème sous-jacent.  

Différents messages d’erreur correspondent aux différents types d’exceptions 429 :
- [Le taux de requêtes est élevé. Un plus grand nombre d’unités de requête peut être nécessaire, donc aucune modification n’a été apportée.](#request-rate-is-large)
- [La requête n’a pas abouti en raison d’un taux élevé de requêtes de métadonnées.](#rate-limiting-on-metadata-requests)
- [La requête n’a pas abouti en raison d’une erreur de service temporaire.](#rate-limiting-due-to-transient-service-error)


## <a name="request-rate-is-large"></a>Le taux de demandes est élevé
Il s’agit du scénario le plus courant. Il se produit lorsque les unités de requête consommées par les opérations sur les données dépassent le nombre de RU/s approvisionné. 

### <a name="step-1-check-the-metrics-to-determine-the-percentage-of-requests-with-429-error"></a>Étape 1 : Vérifier les métriques pour déterminer le pourcentage de requêtes avec une erreur 429
Le fait de voir des messages d’erreur 429 ne signifie pas nécessairement qu’il y a un problème avec votre base de données ou votre conteneur.

#### <a name="how-to-investigate"></a>Comment examiner

Déterminez le pourcentage de requêtes adressées à votre base de données ou à votre conteneur qui ont donné lieu à des messages d’erreur 429, par rapport au nombre total de requêtes réussies. Dans le panneau de votre compte Azure Cosmos DB, accédez à **Insights** > **Requêtes** > **Nombre total des requêtes par code d’état**. Filtrez sur une base de données et un conteneur spécifiques. 

Par défaut, les Kits de développement logiciel (SDK) du client Azure Cosmos DB et les outils d’importation de données, tels qu’Azure Data Factory et la bibliothèque d’exécuteur en bloc, réessayent automatiquement les requêtes en cas d’erreur 429. Ils réessaient généralement jusqu’à neuf fois. Par conséquent, bien que vous puissiez voir des exceptions 429 dans les métriques, ces erreurs peuvent ne pas avoir été renvoyées à votre application. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/insights-429-requests.png" alt-text="Graphique du nombre total de requêtes par code d’état qui indique le nombre de requêtes 429 et 2xx.":::


#### <a name="recommended-solution"></a>Solution recommandée
En général, pour une charge de travail de production, si vous constatez entre 1 % et 5 % de requêtes avec des exceptions 429 et que la latence de bout en bout est acceptable, il s’agit d’un signe sain que les RU/s sont pleinement utilisées. Aucune action n'est requise. Dans le cas contraire, passez aux étapes suivantes de la résolution des problèmes.

### <a name="step-2-determine-if-there-is-a-hot-partition"></a>Étape 2 : Déterminer s’il existe une partition chaude
Une partition chaude se présente quand une ou plusieurs clés de partition logique consomment une quantité disproportionnée du nombre total de RU/s en raison d’un volume de requêtes plus élevé. Cela peut être dû à une conception de clé de partition qui ne distribue pas les requêtes de manière égale. Il en résulte que de nombreuses requêtes sont dirigées vers un petit sous-ensemble de partitions logiques (ce qui implique des partitions physiques) qui deviennent « chaudes ». Étant donné que toutes les données d’une partition logique résident sur une seule partition physique et que le nombre total de RU/s est distribué uniformément entre les partitions physiques, une partition chaude peut entraîner des exceptions 429 et une utilisation inefficace du débit. 

Voici quelques exemples de stratégies de partitionnement qui mènent à des partitions chaudes :
- Vous disposez d’un conteneur qui stocke des données d’appareil IoT pour une charge de travail gourmande en écriture qui est partitionnée par date. Toutes les données d’une même date se trouveront sur la même partition logique et physique. Comme toutes les données écrites chaque jour ont la même date, cela se traduirait par une partition chaude tous les jours. 
    - Au lieu de cela, pour ce scénario, une clé de partition comme ID (soit un GUID, soit ou un ID d’appareil) ou une [clé de partition synthétique](/synthetic-partition-keys.md) qui associe ID et date donnerait une cardinalité plus élevée de valeurs et une meilleure distribution du volume de requêtes.
- Vous avez un scénario multilocataire avec un conteneur partitionné par tenantId. Si un locataire est beaucoup plus actif que les autres, il en résulte une partition chaude. Par exemple, si le plus grand locataire a 100 000 utilisateurs, mais que la plupart des locataires ont moins de 10 utilisateurs, vous aurez une partition chaude lorsqu’ils seront partitionnés par le tenantID. 
    - Pour le scénario précédent, envisagez d’avoir un conteneur dédié pour le plus grand locataire, partitionné par une propriété plus granulaire telle que UserId. 
    
#### <a name="how-to-identify-the-hot-partition"></a>Comment identifier la partition chaude

Pour vérifier s’il existe une partition chaude, accédez à **Insights** > **Débit** > **Consommation normalisée de RU (%) par PartitionKeyRangeID**. Filtrez sur une base de données et un conteneur spécifiques. 

Chaque PartitionKeyRangeId correspond à une partition physique. Si un PartitionKeyRangeId présente une consommation normalisée de RU nettement plus élevée que les autres (par exemple, l’une d’entre elles est toujours à 100 %, alors que les autres sont à 30 % ou moins), cela peut être le signe d’une partition chaude. En savoir plus sur la [métrique de consommation normalisée de RU](monitor-normalized-request-units.md).

:::image type="content" source="media/troubleshoot-request-rate-too-large/split-norm-utilization-by-pkrange-hot-partition.png" alt-text="Graphique de la consommation normalisée de RU par PartitionKeyRangeId avec une partition chaude.":::

Pour déterminer quelles clés de partition logique consomment le plus de RU/s, utilisez les [journaux de diagnostic Azure](cosmosdb-monitor-resource-logs.md). Cet exemple de requête additionne le nombre total d’unités de requête consommées par seconde sur chaque clé de partition logique. 

> [!IMPORTANT]
> L’activation des journaux de diagnostic entraîne des frais distincts pour le service Log Analytics, qui est facturé en fonction du volume de données ingérées. Nous vous recommandons d’activer les journaux de diagnostic pendant une période limitée à des fins de débogage et de les désactiver lorsque vous n’en avez plus besoin. Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/monitor/).

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24hour)
| where Category == "PartitionKeyRUConsumption"
| where collectionName_s == "CollectionName" 
| where isnotempty(partitionKey_s)
// Sum total request units consumed by logical partition key for each second
| summarize sum(todouble(requestCharge_s)) by partitionKey_s, operationType_s, bin(TimeGenerated, 1s)
| order by sum_requestCharge_s desc
```
Cet exemple de sortie montre qu’au cours d’une minute donnée, la clé de partition logique ayant pour valeur « Contoso » a consommé environ 12 000 RU/s, tandis que la clé de partition logique ayant pour valeur « Fabrikam » a consommé moins de 600 RU/s. Si ce modèle était cohérent pendant la période où la limitation de débit s’est produite, cela indiquerait une partition chaude. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/hot-logical-partition-key-results.png" alt-text="Clés de partition logique consommant le plus d’unités de requête par seconde.":::

> [!TIP]
> Dans toute charge de travail, il y aura une variation naturelle du volume des requêtes entre les partitions logiques. Vous devez déterminer si la partition chaude est causée par une asymétrie fondamentale due au choix de la clé de partition (ce qui peut nécessiter de changer la clé) ou par un pic temporaire dû à la variation naturelle des modèles de charge de travail.

#### <a name="recommended-solution"></a>Solution recommandée
Consultez l’aide relative au [choix d’une clé de partition correcte](/partitioning-overview.md#choose-partitionkey).

Si le pourcentage de requêtes à débit limité est élevé et qu’il n’y a pas de partition chaude :
- Vous pouvez [augmenter les RU/s](set-throughput.md) sur la base de données ou le conteneur à l’aide des Kits de développement logiciel (SDK) clients, du portail Azure, de PowerShell, de l’interface CLI ou du modèle ARM.  

Si le pourcentage de requêtes à débit limité est élevé et qu’il existe une partition chaude sous-jacente :
-  À long terme, pour optimiser les coûts et les performances, envisagez de **modifier la clé de partition**. La clé de partition ne pouvant pas être mise à jour sur place, cela nécessite de migrer les données vers un nouveau conteneur avec une clé de partition différente. Azure Cosmos DB prend en charge un [outil de migration des données en direct](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/) à cet effet.
- À court terme, vous pouvez augmenter temporairement les RU/s pour permettre un débit plus important vers la partition chaude. Cette stratégie n’est pas recommandée à long terme, car elle entraîne un surapprovisionnement des RU/s et un coût plus élevé. 

> [!TIP]
>  Lorsque vous augmentez le débit, l’opération de scale-up se termine instantanément ou nécessite jusqu’à cinq à six heures, en fonction du nombre de RU/s pour lesquelles vous souhaitez effectuer un scale-up. Pour connaître le nombre maximal de RU/s que vous pouvez définir sans déclencher l’opération de scale-up asynchrone (qui nécessite qu’Azure Cosmos DB approvisionne davantage de partitions physiques), multipliez le nombre de PartitionKeyRangeIds distincts par 10 000 RU/s. Par exemple, si vous avez 30 000 RU/s approvisionnées et 5 partitions physiques (6000 RU/s allouées par partition physique), vous pouvez passer à 50 000 RU/s (10 000 RU/s par partition physique) dans une opération de scale-up instantané. Une augmentation supérieure à 50 000 RU/s nécessite une opération de scale-up asynchrone.

### <a name="step-3-determine-what-requests-are-returning-429s"></a>Étape 3 : Déterminer les requêtes qui renvoient des exceptions 429

#### <a name="how-to-investigate-requests-with-429s"></a>Comment examiner les requêtes avec exceptions 429
Utilisez les [journaux de diagnostic Azure](cosmosdb-monitor-resource-logs.md) pour identifier les requêtes qui renvoient des exceptions 429 et le nombre de RU/s qu’elles ont consommées. Cet exemple de requête agrège les données au niveau de la minute. 

> [!IMPORTANT]
> L’activation des journaux de diagnostic entraîne des frais distincts pour le service Log Analytics, qui est facturé en fonction du volume de données ingérées. Nous vous recommandons d’activer les journaux de diagnostic pendant une période limitée à des fins de débogage et de les désactiver lorsque vous n’en avez plus besoin. Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/monitor/).

```kusto
AzureDiagnostics
| where TimeGenerated >= ago(24h)
| where Category == "DataPlaneRequests"
| summarize throttledOperations = dcountif(activityId_g, statusCode_s == 429), totalOperations = dcount(activityId_g), totalConsumedRUPerMinute = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, OperationName, requestResourceType_s, bin(TimeGenerated, 1min)
| extend averageRUPerOperation = 1.0 * totalConsumedRUPerMinute / totalOperations 
| extend fractionOf429s = 1.0 * throttledOperations / totalOperations
| order by fractionOf429s desc
```
Par exemple, cet exemple de sortie montre que, chaque minute, 30 % des requêtes Create Document ont vu leur débit limité, chaque demande consommant en moyenne 17 RU.
:::image type="content" source="media/troubleshoot-request-rate-too-large/throttled-requests-diagnostic-logs-results.png" alt-text="Requêtes avec une exception 429 dans les journaux de diagnostic.":::

#### <a name="recommended-solution"></a>Solution recommandée
##### <a name="429s-on-create-replace-or-upsert-document-requests"></a>Exceptions 429 sur les requêtes Create, Replace ou Upsert document
- Par défaut, dans l’API SQL, toutes les propriétés sont indexées. Ajustez la [stratégie d’indexation](index-policy.md) pour indexer uniquement les propriétés nécessaires.
Cela réduit le nombre d’unités de requête requises par opération de création de document, ce qui réduit la probabilité de voir des exceptions 429 ou vous permet d’effectuer un plus grand nombre d’opérations par seconde pour la même quantité de RU/s approvisionnée. 

##### <a name="429s-on-query-document-requests"></a>Exceptions 429 sur les requêtes Query Document
- Suivez l’aide pour [résoudre les problèmes liés aux requêtes dont les frais de RU sont élevés](troubleshoot-query-performance.md#querys-ru-charge-is-too-high).

##### <a name="429s-on-execute-stored-procedures"></a>Exceptions 429 sur les procédures stockées d’exécution
- Les [procédures stockées](stored-procedures-triggers-udfs.md) sont destinées aux opérations qui nécessitent des transactions d’écriture sur une valeur de clé de partition. Il n’est pas recommandé d’utiliser les procédures stockées pour un grand nombre d’opérations de lecture ou d’interrogation. Pour des performances optimales, ces opérations de lecture ou d’interrogation doivent être effectuées côté client, à l’aide des Kits de développement logiciel (SDK) Cosmos. 

## <a name="rate-limiting-on-metadata-requests"></a>Limitation du débit des requêtes de métadonnées

Une limitation du débit des métadonnées peut se produire lorsque vous effectuez un volume élevé d’opérations de métadonnées sur des bases de données et/ou des conteneurs. Les opérations sur les métadonnées comprennent :
- Créer, lire, mettre à jour ou supprimer un conteneur ou une base de données
- Répertorier les bases de données ou les conteneurs dans un compte Cosmos
- Interroger des offres pour voir le débit approvisionné actuel 

Il existe une limite de RU réservées au système pour ces opérations. L’augmentation du nombre de RU/s approvisionnées sur la base de données ou le conteneur n’a aucun impact et n’est donc pas recommandée. Voir [Limites sur les opérations de métadonnées](concepts-limits.md#metadata-request-limits).

#### <a name="how-to-investigate"></a>Comment examiner
Accédez à **Insights** > **Système** > **Requêtes de métadonnées par code d’état**. Filtrez sur une base de données et un conteneur spécifiques, le cas échéant. 

:::image type="content" source="media/troubleshoot-request-rate-too-large/metadata-throttling-insights.png" alt-text="Graphique des requêtes de métadonnées par code d’état dans Insights.":::

#### <a name="recommended-solution"></a>Solution recommandée
- Si votre application doit effectuer des opérations de métadonnées, envisagez d’implémenter une stratégie de backoff pour envoyer ces requêtes à un débit inférieur. 

- Utilisez des instances statiques du client Cosmos DB. Quand DocumentClient ou CosmosClient est initialisé, le Kit de développement logiciel (SDK) Cosmos DB extrait des métadonnées sur le compte, y compris des informations sur le niveau de cohérence, les bases de données, les conteneurs, les partitions et les offres. Cette initialisation peut consommer un grand nombre d’unités de requête et devrait rarement être effectuée. Utilisez une seule instance DocumentClient et utilisez-la pour la durée de vie de votre application.

- Mettez en cache les noms des bases de données et des conteneurs. Récupérez les noms de vos bases de données et de vos conteneurs à partir de la configuration ou mettez-les en cache au démarrage. Les appels comme ReadDatabaseAsync/ReadDocumentCollectionAsync ou CreateDatabaseQuery/CreateDocumentCollectionQuery entraînent des appels de métadonnées au service, qui consomment la limite de RU réservées au système. Ces opérations ne doivent pas être effectuées fréquemment.

## <a name="rate-limiting-due-to-transient-service-error"></a>Limitation du débit en raison d’une erreur de service temporaire

Cette erreur 429 est renvoyée lorsque la requête rencontre une erreur de service temporaire. L’augmentation du nombre de RU/s sur la base de données ou le conteneur n’a aucun impact et n’est donc pas recommandée.

#### <a name="recommended-solution"></a>Solution recommandée
Relancez la requête. Si l’erreur persiste pendant plusieurs minutes, créez un ticket de support à partir du [portail Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Étapes suivantes
* [Surveiller la consommation normalisée de RU/s](monitor-normalized-request-units.md) de votre base de données ou de votre conteneur.
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) des problèmes lors de l’utilisation du kit de développement logiciel (SDK) .NET Azure Cosmos DB.
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md).
* [Diagnostiquer et résoudre](troubleshoot-java-sdk-v4-sql.md) des problèmes lors de l'utilisation du SDK Java v4 Azure Cosmos DB.
* Découvrez les recommandations relatives aux performances pour le [SDK Java v4](performance-tips-java-sdk-v4-sql.md).