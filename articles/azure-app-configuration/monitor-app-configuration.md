---
title: Superviser Azure App Configuration
description: Commencez ici pour découvrir comment superviser App Configuration.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: 7edddaac52c569bf29c1be7e173ce966adb0c17b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969741"
---
# <a name="monitoring-app-configuration"></a>Supervision d’App Configuration
Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article décrit les données de surveillance générées par App Configuration. App Configuration utilise [Azure Monitor](../azure-monitor/overview.md). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-overview-page-in-azure-portal"></a>Page de présentation de la supervision dans Portail Azure
La page **Vue d’ensemble** du portail Azure comprend un aperçu de l’utilisation des ressources, comme le nombre total de demandes, le nombre de demandes limitées et la durée des demandes par magasin de configuration. Ces informations sont utiles, mais ne représentent qu’une petite partie des données de surveillance disponibles. Certaines de ces données de surveillance sont collectées automatiquement et peuvent être analysées dès que vous créez la ressource. Vous pouvez activer d’autres types de collecte de données avec une certaine configuration.

> [!div class="mx-imgBorder"]
> ![Supervision sur la page Vue d’ensemble](./media/monitoring-overview-page.png)

## <a name="monitoring-data"></a>Données de surveillance 
App Configuration collecte les mêmes types de données de surveillance que d’autres ressources Azure, lesquelles sont décrites dans [Données de surveillance de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). Pour plus d’informations sur les métriques et les métriques de journaux créées par App Configuration, consultez [Informations de référence sur les données de surveillance d’App Configuration](./monitor-app-configuration-reference.md).

## <a name="collection-and-routing"></a>Collecte et routage
Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements. Par exemple, pour afficher les journaux et les métriques d’un magasin de configuration en quasi-temps réel dans Azure Monitor, collectez les journaux des ressources dans un espace de travail Log Analytics. Si vous n’en avez pas encore, créez un [espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md) et procédez comme suit pour créer et activer un paramètre de diagnostic. 

 #### <a name="portal"></a>[Portail](#tab/portal)

1. Connectez-vous au portail Azure.

1. Accédez à votre magasin App Configuration.

1. Dans la section **Supervision**, sélectionnez **Paramètres de diagnostic**, puis **+Ajouter un paramètre de diagnostic**. 
    > [!div class="mx-imgBorder"]
    > ![Ajouter un paramètre de diagnostic](./media/diagnostic-settings-add.png)

1. Dans la page **Paramètres de diagnostic**, entrez un nom pour votre paramètre, puis sélectionnez **HttpRequest** et choisissez la destination vers laquelle envoyer vos journaux. Pour les envoyer à un espace de travail Log Analytics, choisissez **Envoyer à un espace de travail Log Analytics**.
 
    > [!div class="mx-imgBorder"]
    > ![Détails des paramètres de diagnostic](./media/monitoring-diagnostic-settings-details.png)

1. Entrez le nom de votre **abonnement** et de votre **espace de travail Log Analytics**. 
1. Sélectionnez **Enregistrer** et vérifiez que la page Paramètres de diagnostic affiche désormais le nouveau paramètre de diagnostic. 
    

 ### <a name="azure-cli"></a>[Azure CLI](#tab/cli)
    
1. Ouvrez Azure Cloud Shell ou, si vous avez installé Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

1. Si votre identité est associée à plusieurs abonnements, définissez l’abonnement du compte de stockage pour lequel vous souhaitez activer les journaux comme abonnement actif.

    ```Azure CLI
    az account set --subscription <your-subscription-id>
    ```

1. Activez les journaux à l’aide de la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create&preserve-view=true).

    ```Azure CLI
    az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <app-configuration-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
    ```

 ### <a name="powershell"></a>[PowerShell](#tab/PowerShell)
    
1. Ouvrez une fenêtre de commande Windows PowerShell, puis connectez-vous à votre abonnement Azure à l’aide de la commande Connect-AzAccount. Ensuite, suivez les instructions à l’écran.

    ```PowerShell
    Connect-AzAccount
    ```

1. Définissez l’abonnement du compte App Configuration pour lequel vous souhaitez activer la journalisation comme abonnement actif.

    ```PowerShell
    Set-AzContext -SubscriptionId <subscription-id>
    ```
    
1. Pour activer les journaux pour un espace de travail Log Analytics, utilisez la cmdlet PowerShell[Set-AzDiagnosticSetting](/previous-versions/azure/mt631625(v=azure.100)?redirectedfrom=MSDN). 

    ```PowerShell
    Set-AzDiagnosticSetting -ResourceId <app-configuration-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true
    ```
1. Vérifiez que votre paramètre de diagnostic est correctement défini et que les catégories de journaux sont activées. 

    ```PowerShell
    Get-AzureRmDiagnosticSetting -ResourceId <app-configuration-resource-id> 
    ```
---
Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). 

Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Pour plus d’informations sur les catégories de journaux pour App Configuration, consultez [Informations de référence sur les données de surveillance d’App Configuration](./monitor-app-configuration-reference.md#resourcelogs).

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques d’App Configuration avec les métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). Pour App Configuration, les métriques suivantes sont collectées : 

* Nombre de requêtes HTTP entrantes 
* Durée des requêtes HTTP entrantes 
* Nombre de requêtes HTTP limitées (réponses avec le code d’état HTTP 429)

Dans le portail, accédez à la section **Métriques** et sélectionnez les **espaces de noms des métriques** et les **métriques** que vous souhaitez analyser. Cette capture d’écran montre l’affichage des métriques lorsque vous sélectionnez **Nombre de requêtes HTTP entrantes** pour votre magasin de configurations.

> [!div class="mx-imgBorder"]
> ![Comment utiliser les métriques d’App Configuration](./media/monitoring-analyze-metrics.png)

Pour obtenir la liste des métriques de plateforme collectées pour App Configuration, consultez [Informations de référence sur les données de surveillance d’App Configuration – Métriques](./monitor-app-configuration-reference.md#metrics). Pour référence, vous pouvez également consulter une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Analyse des journaux d’activité
Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). 

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est un journal de plateforme dans Azure qui fournit des insights de tous les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.  
Pour obtenir la liste des types de journaux de ressources collectés pour App Configuration, consultez [Informations de référence sur les données de surveillance d’App Configuration](./monitor-app-configuration-reference.md#resourcelogs). Pour obtenir la liste des tables utilisées par Journaux d’activité Azure Monitor et interrogeables par Log Analytics, consultez [Informations de référence sur les données de surveillance d’App Configuration](./monitor-app-configuration-reference.md#azuremonitorlogstables).  

>[!IMPORTANT]
> Lorsque vous sélectionnez **Journaux d’activité** dans le menu d’App Configuration, Log Analytics s’ouvre avec l’étendue de requête définie sur la ressource App Configuration actuelle. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. 


Si vous voulez exécuter une requête qui inclut des données provenant d’une autre configuration ou d’autres services Azure, sélectionnez **Journaux d’activité** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

Dans le portail, accédez à la section **Journaux d’activité**, puis à l’éditeur de requête. Sur la gauche sous l’onglet **Tables**, sélectionnez **AACHttpRequest** pour afficher les journaux de votre magasin de configuration. Entrez une requête Kusto dans l’éditeur et les résultats seront affichés dessous.  

> [!div class="mx-imgBorder"]
> ![Écriture de requêtes Kusto dans nos journaux](./media/monitoring-writing-queries.png)

Voici des exemples de requêtes que vous pouvez utiliser pour vous aider à superviser votre ressource App Configuration. 



* Répertorier toutes les requêtes HTTP au cours des trois derniers jours 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
    ```

* Répertorier toutes les requêtes limitées (renvoi du code d’état HTTP 429 pour un trop grand nombre de requêtes) au cours des trois derniers jours 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | where StatusCode == "429"
    ```

* Répertorier le nombre de requêtes envoyées au cours des trois derniers jours par adresse IP 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount= count() by ClientIPAddress
        | order by requestCount desc 
    ```

* Créer un graphique à secteurs des types de codes d’état reçus au cours des trois derniers jours
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount=count() by StatusCode
        | order by requestCount desc 
        | render piechart 
    ```

* Répertorier le nombre de requêtes envoyées par jour au cours des 14 derniers jours
    ```Kusto
    AACHttpRequest
        | where TimeGenerated > ago(14d)
        | extend Day = startofday(TimeGenerated)
        | summarize requestcount=count() by Day
        | order by Day desc  
    ```

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients.
Le tableau suivant répertorie les règles d’alerte courantes et recommandées pour App Configuration.

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
|Limite de débit sur les requêtes HTTP | Code d’état = 429  | Le magasin de configuration a dépassé le [quota horaire de requêtes](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration). Effectuez une mise à niveau vers un magasin standard ou suivez les [meilleures pratiques](./howto-best-practices.md#reduce-requests-made-to-app-configuration) pour optimiser votre utilisation. |



## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les métriques, les journaux et d’autres valeurs importantes créées par App Configuration, consultez [Informations de référence sur les données de surveillance d’App Configuration](./monitor-app-configuration-reference.md).

* Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).