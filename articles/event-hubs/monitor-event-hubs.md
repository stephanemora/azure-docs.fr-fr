---
title: Supervision d’Azure Event Hubs
description: Découvrez comment utiliser Azure Monitor pour afficher et analyser les métriques provenant d’Azure Event Hubs et créer des alertes à leur sujet.
ms.topic: conceptual
ms.custom: subject-monitoring
ms.date: 06/13/2021
ms.openlocfilehash: 1a7ea209b3aaf47095723f8d2831fff4a94ffe54
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063643"
---
# <a name="monitor-azure-event-hubs"></a>Superviser Azure Event Hubs
Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de surveillance générées par Azure Event Hubs et explique comment les analyser et créer des alertes à leur sujet avec Azure Monitor.

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?
Azure Event Hubs crée des données de surveillance à l’aide d’[Azure Monitor](../azure-monitor/overview.md), qui est un service de supervision de pile complète dans Azure. Azure Monitor fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure. Il peut également superviser des ressources dans d’autres clouds et localement.

Commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), qui décrit les concepts suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées pour Azure Event Hubs. Ces sections fournissent également des exemples de configuration de la collecte de données et d’analyse de ces données à l’aide des outils Azure.

> [!TIP]
> Pour comprendre les coûts associés à Azure Monitor, consultez [Utilisation et estimation des coûts](../azure-monitor//usage-estimated-costs.md). Pour comprendre le temps nécessaire à l’affichage de vos données dans Azure Monitor, consultez [Durée d’ingestion des données de journal](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-event-hubs"></a>Données de surveillance d’Azure Event Hubs
Azure Event Hubs collecte les mêmes types de données de surveillance que d’autres ressources Azure, lesquelles sont décrites dans [Données de surveillance de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consultez [Informations de référence sur les données de surveillance d’Azure Event Hubs](monitor-event-hubs-reference.md) pour obtenir une référence détaillée des journaux et des métriques créés par Azure Event Hubs.

## <a name="collection-and-routing"></a>Collecte et routage
Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour Azure Event Hubs sont répertoriées dans [Informations de référence sur les données de surveillance d’Azure Event Hubs](monitor-event-hubs-reference.md#resource-logs).

Si vous utilisez **Stockage Azure** pour stocker les informations de journalisation des diagnostics, les informations sont stockées dans les conteneurs nommés **insights-logs-operationlogs** et **insights-metrics-pt1m**. Exemple d’URL pour un journal des opérations : `https://<Azure Storage account>.blob.core.windows.net/insights-logs-operationallogs/resourceId=/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<Namespace name>/y=<YEAR>/m=<MONTH-NUMBER>/d=<DAY-NUMBER>/h=<HOUR>/m=<MINUTE>/PT1H.json`. L’URL d’un journal de métriques est similaire. 

Si vous utilisez **Azure Event Hubs** pour stocker les informations de journalisation des diagnostics, les informations sont stockées dans les Event hubs nommés **insights-logs-operationlogs** et **insights-metrics-pt1m**. Vous pouvez également sélectionner votre propre instance Event Hub. 

Si vous utilisez **Log Analytics** pour stocker les informations de journalisation des diagnostics, les informations sont stockées dans les tables nommées **AzureDiagnostics** et **AzureMetrics**. 

> [!IMPORTANT]
> L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

> [!NOTE]
> Quand vous activez les métriques dans un paramètre de diagnostic, les informations de dimension ne sont actuellement pas incluses dans les informations envoyées à un compte de stockage, un hub d’événements ou Log Analytics.

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques
Vous pouvez analyser les métriques d’Azure Event Hubs, ainsi que les métriques d’autres services Azure, en sélectionnant **Métriques** dans la section **Azure Monitor** de la page d’accueil de votre espace de noms Event Hubs. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). Pour obtenir la liste des métriques de plateforme collectées, consultez [Informations de référence sur les données de surveillance d’Azure Event Hubs– Métriques](monitor-event-hubs-reference.md#metrics).

![Metrics Explorer avec l’espace de noms Event Hubs sélectionné](./media/monitor-event-hubs/metrics.png)

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Les données des métriques d’Azure Monitor sont disponibles pendant 90 jours. Toutefois, la création de graphiques ne permet de visualiser que 30 jours. Par exemple, si vous souhaitez visualiser une période de 90 jours, vous devez la diviser en 3 graphiques de 30 jours sur la période de 90 jours en question.

### <a name="filtering-and-splitting"></a>Filtrage et fractionnement
Pour les métriques qui prennent en charge les dimensions, vous pouvez appliquer des filtres à l’aide d’une valeur de dimension. Par exemple, ajoutez un filtre avec `EntityName` défini sur le nom d’un Event Hub. Vous pouvez également fractionner une métrique par dimension pour comparer différents segments de celle-ci entre eux. Pour plus d’informations sur le filtrage et le fractionnement, consultez [Fonctionnalités avancées d’Azure Monitor](../azure-monitor/essentials/metrics-charts.md).

:::image type="content" source="./media/monitor-event-hubs/metrics-filter-split.png" alt-text="Image décrivant le filtrage et le fractionnement des métriques":::

## <a name="analyzing-logs"></a>Analyse des journaux d’activité
L’utilisation d’Azure Monitor Log Analytics vous oblige à créer une configuration de diagnostic et à activer __Envoyer des informations à Log Analytics__. Pour plus d’informations, consultez la section [Collecte et routage](#collection-and-routing). Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques. Azure Event Hubs stocke les données dans les tables suivantes : **AzureDiagnostics** et **AzureMetrics**.

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Event Hubs, Log Analytics s’ouvre avec l’étendue de requête définie sur l’espace de travail actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données d’autres bases de données ou des données provenant d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](../azure-monitor/logs/scope.md).

Pour obtenir une référence détaillée des journaux et des métriques, consultez [Informations de référence sur les données de surveillance d’Azure Event Hubs](monitor-event-hubs-reference.md).

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Event Hubs, Log Analytics s’ouvre avec l’étendue de requête définie sur l’espace de noms Azure Event Hubs. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres espaces de travail ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](../azure-monitor/logs/scope.md).

Voici des exemples de requêtes que vous pouvez utiliser pour vous aider à superviser vos ressources Azure Event Hubs : 

+ Obtenir les erreurs des 7 derniers jours

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.EVENTHUB"
    | where Category == "OperationalLogs"
    | summarize count() by "EventName"
    ```

+ Obtenir les tentatives d’accès à un coffre de clés ayant provoqué une erreur « clé introuvable ».

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.EVENTHUB" 
    | where Category == "Error" and OperationName == "wrapkey"
    | project Message
    ```

+ Obtenir les opérations effectuées avec un coffre de clés pour désactiver ou restaurer la clé.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.EVENTHUB"
    | where Category == "info" and OperationName == "disable" or OperationName == "restore"
    | project Message
    ```
+ Obtenir les échecs de capture et leur durée en secondes

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.EVENTHUB"
    | where Category == "ArchiveLogs"
    | summarize count() by "failures", "durationInSeconds"    
    ```
    
## <a name="alerts"></a>Alertes
Vous pouvez accéder aux alertes pour Azure Event Hubs en sélectionnant **Alertes** dans la section **Azure Monitor** de la page d’accueil de votre espace de noms Event Hubs. Pour plus d’informations sur la création d’alertes, consultez [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](../azure-monitor/alerts/alerts-metric.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une référence des journaux et des métriques, consultez [Informations de référence sur les données de surveillance d’Azure Event Hubs](monitor-event-hubs-reference.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
