---
title: Surveiller Azure Cosmos DB | Microsoft Docs
description: Découvrez comment surveiller les performances et la disponibilité d’Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887246"
---
# <a name="monitoring-azure-cosmos-db"></a>Surveiller Azure Cosmos DB
Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par les bases de données Azure Cosmos et comment vous pouvez utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes.

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?
Azure Cosmos DB crée la surveillance des données avec [Azure Monitor](../azure-monitor/overview.md), un service de supervision de pile complète dans Azure qui fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure en plus des ressources figurant dans d’autres clouds et en local. 

Si vous n’êtes pas encore familiarisé avec la surveillance des services Azure, commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) qui décrit ce qui suit :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées à partir d’Azure Cosmos DB et en fournissant des exemples de configuration de collecte de données et d’analyse de ces données avec les outils Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor pour Cosmos DB (préversion)
[Azure Monitor pour Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) est basé sur la [fonctionnalité de classeurs de d’Azure Monitor](../azure-monitor/app/usage-workbooks.md) et utilise les mêmes données de surveillance collectées pour Cosmos DB que celles décrites dans les sections ci-dessous. Utilisez cet outil pour une vue des performances globales, des défaillances, de la capacité et de l’intégrité opérationnelle de toutes vos ressources Azure Cosmos DB dans une expérience interactive unifiée, et tirez parti des autres fonctionnalités d’Azure Monitor pour l’analyse détaillée et les alertes. 

![Azure Monitor pour Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Afficher les métriques de niveau opération pour Azure Cosmos DB

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Surveillance** dans la barre de navigation gauche, puis sélectionnez **Métriques**.

   ![Volet Métriques dans Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. À partir du volet **Métriques** > **Sélectionner une ressource** > choisissez l’**abonnement** exigé, puis **Groupe de ressources**. Pour le **type de ressource**, sélectionnez **Comptes Azure Cosmos DB**, choisissez une de vos comptes Azure Cosmos existants, puis sélectionnez **Appliquer**.

   ![Choisir un compte Cosmos DB pour voir les métriques](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Ensuite, vous pouvez sélectionner une métrique dans la liste des métriques disponibles. Vous pouvez sélectionner des métriques propres aux unités de requête, au stockage, à la latence, à la disponibilité, à Cassandra, etc. Pour découvrir de plus près toutes les métriques disponibles dans cette liste, consultez l’article [Métriques par catégorie](monitor-cosmos-db-reference.md). Dans cet exemple, nous allons sélectionner **Unités de requête** et **Moy** comme valeur d’agrégation.

   En plus de ces détails, vous pouvez également sélectionner l’**intervalle de temps** et la **granularité temporelle** des métriques. Au maximum, vous pouvez voir les métriques des 30 derniers jours.  Une fois que vous avez appliqué le filtre, un graphique s’affiche. Vous pouvez voir le nombre moyen d’unités de requête consommées par minute pendant la période sélectionnée.  

   ![Choisir une métrique à partir du portail Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Ajouter des filtres aux métriques

Vous pouvez également filtrer les métriques et le graphique affiché par une valeur **CollectionName**, **DatabaseName**, **OperationType**, **Region** et **StatusCode** spécifique. Pour filtrer les métriques, sélectionnez **Ajouter un filtre** et choisissez la propriété nécessaire comme **OperationType**, puis sélectionnez une valeur comme **Requête**. Le graphique affiche alors les unités de requête consommées pour l’opération de requête pendant la période sélectionnée. Les opérations exécutées par procédure stockée ne sont pas journalisées si bien qu’elles ne sont pas disponibles sous la métrique OperationType.

![Ajouter un filtre pour sélectionner la granularité des métriques](./media/monitor-cosmos-db/add-metrics-filter.png)

Vous pouvez regrouper des métriques à l’aide de l’option **Appliquer la division**. Par exemple, vous pouvez regrouper les unités de requête par type d’opération et voir le graphique pour toutes les opérations comme illustré dans l’image suivante :

![Ajouter un filtre d’application de la division](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Analyse des données collectées à partir d’Azure Cosmos DB

Azure Cosmos DB collecte les mêmes types de données de supervision que les autres ressources Azure, qui sont décrites dans [Supervision de des données à partir de ressources Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Pour obtenir une référence détaillée des journaux et des métriques créés par Azure Cosmos DB, consultez [Informations de référence sur les données de supervision Azure Cosmos DB](monitor-cosmos-db-reference.md).

La page **Vue d’ensemble** du portail Azure pour chaque base de données Azure Cosmos comprend un bref aperçu de l’utilisation de la base de données, y compris l’utilisation des requêtes et la facturation horaire. Il s’agit d’informations utiles, mais qui ne constituent qu’une petite quantité des données de surveillance disponibles. Certaines de ces données sont collectées automatiquement et disponibles pour analyse dès que vous créez la base de données, tandis que vous pouvez activer la collecte de données supplémentaires par configuration.

![Page Vue d’ensemble](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analyse des données de métrique

Azure Cosmos DB fournit une expérience personnalisée pour l’utilisation des métriques. Pour plus d’informations sur l’utilisation de cette expérience et sur l’analyse de différents scénarios Azure Cosmos DB, consultez [Superviser et déboguer des métriques Azure Cosmos DB à partir d’Azure Monitor](cosmos-db-azure-monitor-metrics.md).

Vous pouvez analyser les métriques d’Azure Cosmos DB avec les métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md). Toutes les métriques d’Azure Cosmos DB se trouvent dans l’espace de noms **Métriques standard Cosmos DB**. Vous pouvez utiliser les dimensions suivantes avec ces métriques lorsque vous ajoutez un filtre à un graphique :

- CollectionName
- nom_base_de_données
- OperationType
- Région
- StatusCode


## <a name="analyzing-log-data"></a>Analyse des données de journal
Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques. Azure Cosmos DB stocke les données dans les tables suivantes.

| Table de charge de travail | Description |
|:---|:---|
| AzureDiagnostics | Table commune utilisée par plusieurs services pour stocker les journaux de ressources. Les journaux de ressources d’Azure Cosmos DB peuvent être identifiés avec `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Table commune qui stocke tous les enregistrements du journal d’activité. 


> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Cosmos DB, Log Analytics est ouvert avec l’étendue de requête définie sur la base de données Azure Cosmos. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données d’autres bases de données ou des données provenant d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](../azure-monitor/log-query/scope.md).

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Requêtes Azure Cosmos DB Log Analytics dans Azure Monitor

Voici quelques requêtes que vous pouvez entrer dans la zone **Recherche dans les journaux**. Elles vous aideront à superviser vos conteneurs Azure Cosmos. Ces requêtes fonctionnent avec le [nouveau langage](../log-analytics/log-analytics-log-search-upgrade.md).

Voici des requêtes que vous pouvez utiliser pour vous aider à superviser vos bases de données Azure Cosmos.

* Pour rechercher tous les journaux de diagnostic issus d’Azure Cosmos DB pour la période spécifiée :

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Pour rechercher les 10 derniers événements journalisés :

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Pour rechercher toutes les opérations regroupées par type :

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Pour rechercher toutes les opérations regroupées par ressource :

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Pour rechercher toutes les activités utilisateur regroupées par ressource :

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Pour récupérer toutes les requêtes supérieures à 100 RU jointes à des données de **DataPlaneRequests** et **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Pour rechercher les opérations durant plus de 3 millisecondes :

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Pour rechercher l’agent exécutant les opérations :

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Pour recherche le moment d’exécution des opérations de longue durée :

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Pour obtenir des statistiques de clé de partition afin d’évaluer le décalage entre les 3 premières partitions pour le compte de base de données :

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Surveiller un compte Azure Cosmos DB par programme
Les métriques au niveau du compte disponibles dans le portail, par exemple les demandes d'utilisation du stockage du compte et de total, ne sont pas disponibles via les API SQL. Toutefois, vous pouvez extraire des données d'utilisation au niveau de la collection à l’aide des API SQL. Pour extraire des données au niveau de la collection, procédez comme suit :

* Pour utiliser l’API REST, [effectuez une opération GET sur la collection](https://msdn.microsoft.com/library/mt489073.aspx). Les informations de quota et d'utilisation de la collection sont retournées dans les en-têtes x-ms-resource-quota et x-ms-resource-usage de la réponse.
* Pour utiliser le Kit de développement logiciel (SDK) .NET, utilisez la méthode [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), qui retourne une valeur [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenant un nombre de propriétés d’utilisation, notamment **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** et plus encore.

Pour accéder à des mesures supplémentaires, utilisez le [Kit de développement logiciel (SDK) Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Les définitions de mesures disponibles peuvent être récupérées en appelant :

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Les requêtes permettant de récupérer des mesures individuelles utilisent le format suivant :

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une référence des journaux et des métriques créés par Azure Cosmos DB, consultez [Informations de référence sur les données de supervision Azure Cosmos DB](monitor-cosmos-db-reference.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
