---
title: Supervision d’Azure Service Bus
description: Découvrez comment utiliser Azure Monitor pour voir, analyser et créer des alertes sur des métriques à partir d’Azure Service Bus.
ms.topic: conceptual
ms.custom: subject-monitoring
ms.date: 05/18/2021
ms.openlocfilehash: 90930a5f76fd1aaccd7968febdf7dbdbd684ab28
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110192068"
---
# <a name="monitor-azure-service-bus"></a>Surveiller Azure Service Bus
Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par Azure Service Bus, ainsi que l’analyse et les alertes sur ces données à l’aide d’Azure Monitor.

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?
Azure Service Bus crée des données de supervision à l’aide d’[Azure Monitor](../azure-monitor/overview.md), qui est un service de supervision de pile complète dans Azure. Azure Monitor fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure. Il peut également superviser des ressources dans d’autres clouds et localement.

Commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), qui décrit les concepts suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées pour Azure Service Bus. Ces sections fournissent également des exemples de configuration de la collecte de données et d’analyse de ces données à l’aide des outils Azure.

> [!TIP]
> Pour comprendre les coûts associés à Azure Monitor, consultez [Utilisation et estimation des coûts](../azure-monitor//usage-estimated-costs.md). Pour comprendre le temps nécessaire à l’affichage de vos données dans Azure Monitor, consultez [Durée d’ingestion des données de journal](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-service-bus"></a>Données de supervision issues d’Azure Service Bus
Azure Service Bus collecte les mêmes types de données de supervision que d’autres ressources Azure, lesquelles sont décrites dans [Supervision des données de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consultez [Informations de référence sur les données de supervision Azure Service Bus](monitor-service-bus-reference.md) pour obtenir une référence détaillée des journaux et des métriques créés par Azure Service Bus.

## <a name="collection-and-routing"></a>Collecte et routage
Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour Azure Service Bus sont répertoriées dans la [Référence des données de surveillance d’Azure Service Bus](monitor-service-bus-reference.md#resource-logs).

### <a name="azure-storage"></a>Stockage Azure 
Les informations de journalisation des diagnostics sont stockées dans des conteneurs nommé **insights-logs-operationlogs** et **insights-metrics-pt1m**.

Exemple d’URL pour un journal des opérations : `https://<Azure Storage account>.blob.core.windows.net/insights-logs-operationallogs/resourceId=/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<Namespace name>/y=<YEAR>/m=<MONTH-NUMBER>/d=<DAY-NUMBER>/h=<HOUR>/m=<MINUTE>/PT1H.json`. L’URL d’un journal de métriques est similaire. 

### <a name="azure-event-hubs"></a>Hubs d'événements Azure
Les informations de journalisation des diagnostics sont stockées dans des instances Event Hubs nommées **insights-logs-operationlogs** et **insights-metrics-pt1m**. Vous pouvez également sélectionner votre propre instance Event Hub. 

### <a name="log-analytics"></a>Log Analytics 
Les informations de journalisation des diagnostics sont stockées dans des tables nommées **AzureDiagnostics** et **AzureMetrics**. 

### <a name="sample-operational-log-output-formatted"></a>Exemple de sortie de journal opérationnel (formatée)

```json
{
    "Environment": "PROD",
    "Region": "East US",
    "ScaleUnit": "PROD-BL2-002",
    "ActivityId": "a097a88a-33e5-4c9c-9c64-20f506ec1375",
    "EventName": "Retrieve Namespace",
    "resourceId": "/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/SPSBUS0213RG/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SPSBUS0213NS",
    "SubscriptionId": "<Azure subscription ID>",
    "EventTimeString": "5/18/2021 3:25:55 AM +00:00",
    "EventProperties": "{\"SubscriptionId\":\"<Azure subscription ID>\",\"Namespace\":\"spsbus0213ns\",\"Via\":\"https://spsbus0213ns.servicebus.windows.net/$Resources/topics?api-version=2017-04&$skip=0&$top=100\",\"TrackingId\":\"a097a88a-33e5-4c9c-9c64-20f506ec1375_M8CH3_M8CH3_G8\"}",
    "Status": "Succeeded",
    "Caller": "rpfrontdoor",
    "category": "OperationalLogs"
}
```

### <a name="sample-metric-log-output-formatted"></a>Exemple de sortie de journal des métriques (mise en forme)

```json
{
    "count": 1,
    "total": 4,
    "minimum": 4,
    "maximum": 4,
    "average": 4,
    "resourceId": "/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/SPSBUS0213RG/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SPSBUS0213NS",
    "time": "2021-05-18T03:27:00.0000000Z",
    "metricName": "IncomingMessages",
    "timeGrain": "PT1M"
}
```

> [!IMPORTANT]
> L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

> [!NOTE]
> Quand vous activez les métriques dans un paramètre de diagnostic, les informations de dimension ne sont actuellement pas incluses dans les informations envoyées à un compte de stockage, un hub d’événements ou Log Analytics.

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques
Vous pouvez analyser les métriques d’Azure Service Bus, ainsi que les métriques d’autres services Azure, en sélectionnant **Métriques** dans la section **Azure Monitor** de la page d’hébergement de votre espace de noms Service Bus. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). Pour obtenir la liste des métriques de plateforme collectées, consultez [Informations de référence sur l’analyse des données Azure Service Bus – Métriques](monitor-service-bus-reference.md#metrics).

![Metrics Explorer avec l’espace de noms Service Bus sélectionné](./media/monitor-service-bus/metrics.png)

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Les données des métriques d’Azure Monitor sont disponibles pendant 90 jours. Toutefois, la création de graphiques ne permet de visualiser que 30 jours. Par exemple, si vous souhaitez visualiser une période de 90 jours, vous devez la diviser en 3 graphiques de 30 jours sur la période de 90 jours en question.

### <a name="filtering-and-splitting"></a>Filtrage et fractionnement
Pour les métriques qui prennent en charge les dimensions, vous pouvez appliquer des filtres à l’aide d’une valeur de dimension. Par exemple, ajoutez un filtre avec `EntityName` défini sur le nom d’une file d’attente ou d’une rubrique. Vous pouvez également fractionner une métrique par dimension pour comparer différents segments de celle-ci entre eux. Pour plus d’informations sur le filtrage et le fractionnement, consultez [Fonctionnalités avancées d’Azure Monitor](../azure-monitor/essentials/metrics-charts.md).

## <a name="analyzing-logs"></a>Analyse des journaux d’activité
L’utilisation d’Azure Monitor Log Analytics vous oblige à créer une configuration de diagnostic et à activer __Envoyer des informations à Log Analytics__. Pour plus d’informations, consultez la section [Collecte et routage](#collection-and-routing). Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques. Azure Service Bus stocke les données dans les tables suivantes : **AzureDiagnostics** et **AzureMetrics**.

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Service Bus, Log Analytics est ouvert avec l’étendue de requête définie sur l’espace de travail actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données d’autres bases de données ou des données provenant d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](../azure-monitor/logs/scope.md).

Pour obtenir une référence détaillée des journaux et des métriques, consultez [Informations de référence sur les données de supervision Azure Service Bus](monitor-service-bus-reference.md).

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Cosmos DB, Log Analytics est ouvert avec l’étendue de requête définie sur l’espace de noms Azure Service Bus. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres espaces de travail ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](../azure-monitor/logs/scope.md).

Voici des exemples de requêtes que vous pouvez utiliser pour vous aider à superviser vos ressources Azure Service Bus : 

+ Obtenir les opérations de gestion au cours des 7 derniers jours. 

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.SERVICEBUS"
    | where Category == "OperationalLogs"
    | summarize count() by EventName_s, _ResourceId
    ```

+ Obtenir les tentatives d’accès à un coffre de clés ayant provoqué une erreur « clé introuvable ».

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS" 
    | where Category == "Error" and OperationName == "wrapkey"
    | project Message, _ResourceId
    ```

+ Obtenir les erreurs des 7 derniers jours

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.SERVICEBUS"
    | where Category == "Error" 
    | summarize count() by EventName_s, _ResourceId
    ```

+ Obtenir les opérations effectuées avec un coffre de clés pour désactiver ou restaurer la clé.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS"
    | where (Category == "info" and (OperationName == "disable" or OperationName == "restore"))
    | project Message, _ResourceId
    ```

+ Obtenir toutes les entités qui ont été supprimées automatiquement

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.SERVICEBUS"
    | where Category == "OperationalLogs"
    | where EventName_s startswith "AutoDelete"
    | summarize count() by EventName_s, _ResourceId    
    ```
    
## <a name="alerts"></a>Alertes
Vous pouvez accéder aux alertes pour Azure Service Bus en sélectionnant **Alertes** dans la section **Azure Monitor** de la page d’hébergement de votre espace de noms Service Bus. Pour plus d’informations sur la création d’alertes, consultez [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-metric.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une référence des journaux et des métriques, consultez [Informations de référence sur l’analyse des données Azure Service Bus](monitor-service-bus-reference.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
