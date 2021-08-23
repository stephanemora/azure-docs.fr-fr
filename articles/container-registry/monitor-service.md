---
title: Surveiller Azure Container Registry
description: Commencez ici pour découvrir comment surveiller votre registre de conteneurs Azure à l’aide des fonctionnalités d’Azure Monitor
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 03/19/2021
ms.openlocfilehash: 1b7f8a13a7d372fe67e5ca7c235febfa52294432
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294944"
---
# <a name="monitor-azure-container-registry"></a>Surveiller Azure Container Registry

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de surveillance générées par Azure Container Registry ainsi que la façon dont vous pouvez utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes.

## <a name="monitor-overview"></a>Présentation de Monitor

La page **Vue d’ensemble** du portail Azure pour chaque registre comprend un aperçu de l’utilisation récente des ressources et de l’activité, telles que les opérations d’envoi (push) et de tirage (pull). Ces informations de haut niveau sont utiles, mais seule une petite partie des données de surveillance y est présentée. 

:::image type="content" source="media/monitor-service/metrics-overview.png" alt-text="Vue d’ensemble des métriques du registre"::: 

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?

Azure Container Registry crée des données de surveillance à l’aide d’[Azure Monitor](../azure-monitor/overview.md), un service de supervision de pile complète dans Azure qui fournit un ensemble exhaustif de fonctionnalités pour superviser vos ressources Azure en plus des ressources figurant dans d’autres clouds et en local.

Commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), qui décrit les concepts suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées à partir d’Azure Container Registry et en fournissant des exemples de configuration de la collecte de données et d’analyse de ces données avec les outils Azure.

## <a name="monitoring-data"></a>Données de surveillance 

Azure Container Registry collecte les mêmes types de données de surveillance que d’autres ressources Azure, lesquelles sont décrites dans [Données de surveillance de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Pour obtenir des informations détaillées sur les métriques et les métriques de journaux créées par le service Azure Container Registry, consultez [Informations de référence sur les données de surveillance d’Azure Container Registry](monitor-service-reference.md).

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour Azure Container Registry sont répertoriées dans [Informations de référence sur les données de surveillance d’Azure Container Registry](monitor-service-reference.md#resource-logs).

> [!TIP]
> Vous pouvez également créer des paramètres de diagnostic du registre en accédant à votre registre dans le portail. Dans le menu, sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

L’image suivante montre les options lorsque vous activez les paramètres de diagnostic pour un registre.

:::image type="content" source="media/monitor-service/diagnostic-settings.png" alt-text="Paramètres de diagnostic pour un registre de conteneurs":::

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics-preview"></a>Analyse des métriques (préversion)

Vous pouvez analyser les métriques d’un registre de conteneurs Azure avec les métriques d’autres services Azure à l’aide de Metrics Explorer en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

> [!TIP]
> Vous pouvez également accéder à Metrics Explorer en vous rendant sur votre registre dans le portail. Dans le menu, sélectionnez **Métriques (préversion)** sous **Supervision**.

Pour obtenir la liste des métriques de plateforme collectées pour Azure Container Registry, consultez [Informations de référence sur les données de surveillance d’Azure Container Registry](monitor-service-reference.md#metrics).  

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="azure-cli"></a>Azure CLI

Les commandes Azure CLI suivantes peuvent être utilisées pour obtenir des informations sur les métriques d’Azure Container Registry.

* [az acr show-usage](/cli/azure/acr/#az_acr_show_usage) : Affiche le stockage actuel utilisé par un registre de conteneurs Azure
* [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) : Répertorie les définitions et les dimensions des métriques
* [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) : Récupère les valeurs des métriques

### <a name="rest-api"></a>API REST 

Vous pouvez utiliser l’API REST d’Azure Monitor pour obtenir des informations par programmation concernant les métriques d’Azure Container Registry.

* [Liste des définitions et les dimensions des métriques](/rest/api/monitor/metricdefinitions/list)
* [Récupération des valeurs des métriques](/rest/api/monitor/metrics/list)

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Tous les journaux de ressources dans Azure Monitor ont les mêmes champs suivis de champs spécifiques au service. Le schéma commun est décrit dans [Schéma des journaux des ressources Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Le schéma des journaux de ressources d’Azure Container Registry est disponible dans les [informations de référence sur les données d’Azure Container Registry](monitor-service-reference.md#schemas). 

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est un journal de plateforme dans Azure qui fournit des insights de tous les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.  

Pour obtenir la liste des types de journaux de ressources collectés pour Azure Container Registry, consultez [Informations de référence sur les données de surveillance d’Azure Container Registry](monitor-service-reference.md#resource-logs).  

Pour obtenir la liste des tables utilisées par Journaux d’activité Azure Monitor et interrogeables par Log Analytics, consultez [Informations de référence sur les données de surveillance d’Azure Container Registry](monitor-service-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu d’**Azure Container Registry**, Log Analytics s’ouvre avec l’étendue de la requête définie sur le registre actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres registres ou d’autres services Azure, sélectionnez **Journaux d’activité** dans le menu d’**Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

Par exemple, la requête suivante récupère les 24 heures de données les plus récentes à partir de la table **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

L’image suivante présente un exemple de sortie :

:::image type="content" source="media/monitor-service/azure-monitor-query.png" alt-text="Interroger des données de journal":::

Voici des requêtes que vous pouvez utiliser pour vous aider à superviser votre ressource de registre. 

### <a name="error-events-from-the-last-hour"></a>Événements d’erreur au cours de la dernière heure

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>Les 100 événements de registre les plus récents

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identité de l’utilisateur ou de l’objet qui a supprimé le référentiel

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identité de l’utilisateur ou de l’objet qui a supprimé la balise

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Échecs d’opérations au niveau du référentiel

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Échecs d’authentification du registre

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients.


<!-- only include next line if applications run on your service and work with App Insights. 

If you are creating or running an application which run on <*service*> [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) may offer additional types of alerts.
-->

Le tableau suivant répertorie les règles d’alerte courantes et recommandées pour Azure Container Registry.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->
| Type d’alerte | Condition | Description  |
|:---|:---|:---|
| Métrique | Signal : Stockage utilisé<br/>Opérateur : Supérieur à<br/>Type d’agrégation : Moyenne<br/>Valeur de seuil : 5 Go|  Signale si le stockage du registre utilisé dépasse une valeur spécifiée.|
| | | |

### <a name="example-send-email-alert-when-registry-storage-used-exceeds-a-value"></a>Exemple : Envoyer une alerte par e-mail lorsque le stockage du registre utilisé dépasse une valeur

1. Dans le portail Azure, accédez à votre registre.
1. Sélectionnez **Métriques (préversion)** sous **Supervision**.
1. Dans Metrics Explorer, dans **Métrique**, sélectionnez **Stockage utilisé**.
1. Sélectionnez **Nouvelle règle d’alerte**.
1. Dans **Étendue**, confirmez la ressource de registre pour laquelle vous souhaitez créer une règle d’alerte.
1. Dans **Condition**, sélectionnez **Ajouter une condition**.
    1. Dans **Nom du signal**, sélectionnez **Stockage utilisé**.
    1. Dans **Période du graphique**, sélectionnez **Au cours des dernières 24 heures**.
    1. Dans **Logique d’alerte**, dans **Valeur de seuil**, sélectionnez une valeur telle que *5*. Dans **Unité**, sélectionnez une valeur telle que *Go*.
    1. Acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Terminé**.
1. Dans **Actions**, sélectionnez **Ajouter des groupes d’actions** >  **+ Créer un groupe d’actions**.
    1. Entrez les détails du groupe d’actions.
    1. Sous l’onglet **Notifications**, sélectionnez **E-mail/SMS/Push/Voix**, puis entrez un destinataire tel que *admin@contoso.com* . Sélectionnez **Revoir + créer**.
1. Entrez un nom et une description de la règle d’alerte, puis sélectionnez le niveau de gravité.
1. Sélectionnez **Créer une règle d’alerte**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les métriques, les journaux et d’autres valeurs importantes créées par Azure Container Registry, consultez [Informations de référence sur les données de surveillance d’Azure Container Registry](monitor-service-reference.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).