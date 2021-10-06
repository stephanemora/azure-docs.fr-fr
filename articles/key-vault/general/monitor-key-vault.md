---
title: Supervision d’Azure Key Vault
description: Commencer ici pour découvrir comment surveiller Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/21/2021
ms.author: mbaldwin
ms.custom: subject-monitoring
ms.openlocfilehash: fbb92ac1d1d6992a78577a8dfc062d6730302f2d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353351"
---
# <a name="monitoring-azure-key-vault"></a>Supervision d’Azure Key Vault

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Par Azure Key Vault, il est important de surveiller votre service au fur et à mesure que vous commencez à mettre à l’échelle, car le nombre de demandes envoyées à votre coffre de clés augmentera. Cela risque d'augmenter la latence de vos requêtes et, dans les cas les plus extrêmes, de les limiter, ce qui aura un impact sur les performances de votre service.

Cet article décrit les données de surveillance générées par Key Vault. Key Vault utilise [Azure Monitor](/azure/azure-monitor/overview). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource).

## <a name="monitoring-overview-page-in-azure-portal"></a>Page de présentation de la supervision dans Portail Azure

La page de **présentation** du Portail Azure comprend les mesures suivantes dans l'onglet « Surveillance » pour chaque coffre de clés :

- Nombre total de requêtes
- Latence moyenne
- Taux de réussite

Vous pouvez sélectionner « mesures supplémentaires » (ou l’onglet « métriques » dans le volet gauche, sous « analyse ») pour afficher ces mesures également :

- Latence globale de l'API de service
- Disponibilité globale du coffre
- Saturation globale du coffre
- Correspondances totales de l'API de service
- Résultats totaux de l'API de service

## <a name="key-vault-insights"></a>Insights Key Vault

Dans Azure, certains services disposent d’un tableau de bord de surveillance prédéfini spécial dans le portail Azure qui constitue un point de départ pour la surveillance de votre service. Ces tableaux de bord spéciaux sont appelés « insights ».

Key Vault Insights permet une supervision complète de vos coffres de clés en fournissant une vue unifiée des informations relatives aux requêtes, au niveau de performance, aux échecs et à la latence de Key Vault. Pour plus d’informations, consultez [Superviser votre service de coffre de clés avec Key Vault Insights](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="monitoring-data"></a>Données de surveillance

Key Vault collecte les mêmes types de données d’analyse que d’autres ressources Azure, lesquelles sont décrites dans [Analyse des données de ressources Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data-from-Azure-resources).

Consultez [Analyse *des références des données* Key Vault](monitor-key-vault-reference.md) pour obtenir des informations détaillées sur les mesures et les mesures de journaux créées par Key Vault.

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../../azure-monitor/platform/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour [Key Vault](monitor-key-vault-reference.md#resource-logs) sont répertoriées dans la *Référence des données d’analyse Key Vault*.

Pour créer un paramètre de diagnostic pour votre coffre de clés, consultez [Activer la journalisation des Key Vault](howto-logging.md).  Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les mesures pour Key Vault avec les mesures d’autres services Azure à l’aide de l’explorateur des mesures en ouvrant **Mesures** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

Pour obtenir la liste des métriques de plateforme collectées pour Key Vault, consultez [Surveillance des métriques de référence des données Key Vault](monitor-key-vault-reference.md#metrics).  

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../../azure-monitor/platform/diagnostic-logs-schema.md#top-level-resource-logs-schema). 

Le [journal d’activité](../../azure-monitor/platform/activity-log.md) est un type de journal de plateforme dans Azure qui fournit des insights de tous les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.  

Pour obtenir la liste des journaux de ressources collectés pour Key Vault, consultez [Surveillance des métriques de référence des données Key Vault](monitor-key-vault-reference.md#resource-logs).  

Pour obtenir la liste des tables utilisées par les Journaux de surveillance Azure Monitor et interrogeables par Log Analytics, consultez [Informations de référence sur les données de surveillance de Key Vault](monitor-key-vault-reference.md#azure-monitor-logs-tables)  

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Key Vault, Log Analytics est ouvert avec l’étendue de requête définie sur le coffre de clés actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres coffres de clé ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

Voici quelques requêtes que vous pouvez entrer dans la barre de recherche **Recherche dans les journaux** pour superviser plus facilement vos ressources Key Vault. Ces requêtes fonctionnent avec le [nouveau langage](/azure/azure-monitor/logs/log-query-overview).

* Des demandes sont-elles lentes ?

    ```Kusto
    // List of KeyVault requests that took longer than 1sec. 
    // To create an alert for this query, click '+ New alert rule'
    let threshold=1000; // let operator defines a constant that can be further used in the query

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where DurationMs > threshold
    | summarize count() by OperationName, _ResourceId
    ```

* Y a-t-il des échecs ?

    ```Kusto
    // Count of failed KeyVault requests by status code. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where httpStatusCode_d >= 300 and not(OperationName == "Authentication" and httpStatusCode_d == 401)
    | summarize count() by requestUri_s, ResultSignature, _ResourceId
    // ResultSignature contains HTTP status, e.g. "OK" or "Forbidden"
    // httpStatusCode_d contains HTTP status code returned
    ```

* Erreurs de désérialisation d’entrée

    ```Kusto
    // Shows errors caused due to malformed events that could not be deserialized by the job. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.KEYVAULT" and parse_json(properties_s).DataErrorType in ("InputDeserializerError.InvalidData", "InputDeserializerError.TypeConversionError", "InputDeserializerError.MissingColumns", "InputDeserializerError.InvalidHeader", "InputDeserializerError.InvalidCompressionType")
    | project TimeGenerated, Resource, Region_s, OperationName, properties_s, Level, _ResourceId
    ```

* Quelle a été l'activité de ce KeyVault ?

    ```Kusto
    //  
    // Line chart showing trend of KeyVault requests volume, per operation over time. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize count() by bin(TimeGenerated, 1h), OperationName // Aggregate by hour
    | render timechart

    ```

* Qui appelle ce Key Vault ? 

    ```Kusto
    // List of callers identified by their IP address with their request count.  
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT"
    | summarize count() by CallerIPAddress
    ```

* Quel est le rythme auquel ce KeyVault traite les demandes ? 

    ```Kusto
    // Line chart showing trend of request duration over time using different aggregations. 
 
    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize avg(DurationMs) by requestUri_s, bin(TimeGenerated, 1h) // requestUri_s contains the URI of the request
    | render timechart
    ```

* Quelles modifications ont été effectuées le mois dernier ? 

    ```Kusto
    // Lists all update and patch requests from the last 30 days. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where TimeGenerated > ago(30d) // Time range specified in the query. Overrides time picker in portal.
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where OperationName == "VaultPut" or OperationName = "VaultPatch"
    | sort by TimeGenerated desc
    ```


## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../../azure-monitor/platform/alerts-metric-overview.md), sur des [journaux](../../azure-monitor/platform/alerts-unified-log.md) et sur le [journal d’activité](../../azure-monitor/platform/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients

Si vous créez ou exécutez une application qui utilise Azure Key Vault, les [Insights d’application Azure Monitor](/azure/azure-monitor/overview#application-insights) peuvent vous proposer des types d’alertes supplémentaires.

Voici quelques règles d’alerte courantes et recommandées pour Azure Key Vault.

- La disponibilité du coffre de clés est inférieure à 100 % (seuil statique)
- La latence du coffre de clés est supérieure à 500 ms (seuil statique)
- La saturation globale du coffre est supérieure à 75 % (seuil statique)
- La saturation globale du coffre est supérieure à la moyenne (seuil dynamique)
- Le nombre total de codes d'erreur est supérieur à la moyenne (seuil dynamique)

Pour plus d’informations, consultez [Alertes pour Azure Key Vault](alert.md).

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous surveillez Azure Key Vault. Si vous souhaitez également définir des alertes, consultez [Alerte pour Azure Key Vault](alert.md).

- Pour plus d’informations sur les métriques, les journaux et d’autres valeurs importantes créées par Key Vault, consultez [Surveillance des informations de référence sur les données de surveillance d’Azure Key Vault](monitor-key-vault-reference.md) .
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).
