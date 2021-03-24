---
title: Superviser des machines virtuelles Azure avec Azure Monitor
description: Décrit comment collecter et analyser des données de supervision sur des machines virtuelles dans Azure en utilisant Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 2c93471436030f9260f4fa0d95d656c27d382346
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047041"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Supervision de machines virtuelles Azure avec Azure Monitor
Cet article explique comment utiliser Azure Monitor pour collecter et analyser des données de supervision sur des machines virtuelles Azure pour maintenir leur intégrité. Les machines virtuelles peuvent être supervisées pour vérifier leur disponibilité et leurs performances avec Azure Monitor comme n’importe quelle [autre ressource Azure](../essentials/monitor-azure-resource.md), mais elles se distinguent des autres ressources, car vous devez également superviser le système d’exploitation invité et les charges de travail qui y sont exécutées. 

> [!NOTE]
> Cet article fournit une vue d’ensemble complète des concepts et des options de supervision des machines virtuelles dans Azure Monitor. Pour commencer la supervision de vos machines virtuelles rapidement sans vous attarder sur les concepts sous-jacents, consultez [Démarrage rapide : Superviser une machine virtuelle Azure avec Azure Monitor](./quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Différences par rapport aux autres ressources Azure
[Supervision des ressources Azure avec Azure Monitor](../essentials/monitor-azure-resource.md) décrit les données de supervision générées par les ressources Azure et comment utiliser les fonctionnalités d’Azure Monitor pour analyser ces données et créer des alertes. Vous pouvez collecter et utiliser les mêmes données de supervision sur des machines virtuelles Azure avec les différences suivantes :

-  Les [métriques de plateforme](../essentials/data-platform-metrics.md) sont collectées automatiquement pour les machines virtuelles, mais uniquement pour l’[hôte de machine virtuelle](#monitoring-data). Vous avez besoin d’un agent pour collecter les données de performances du système d’exploitation invité. 
- Les machines virtuelles ne génèrent pas de [journaux de ressources](../essentials/platform-logs-overview.md) qui fournissent des insights sur les opérations qui ont été effectuées au sein d’une ressource Azure. Vous utilisez un agent pour collecter les données de journal du système d’exploitation invité.
- Vous pouvez créer des [paramètres de diagnostic](../essentials/diagnostic-settings.md) pour qu’une machine virtuelle envoie des métriques de plateforme à d’autres destinations comme le stockage et les hubs d’événements, mais vous ne pouvez pas configurer ces paramètres de diagnostic dans le portail Azure. 

## <a name="monitoring-data"></a>Données de surveillance
Les machines virtuelles dans Azure génèrent des [journaux](../logs/data-platform-logs.md) et des [métriques](../essentials/data-platform-metrics.md) affichés dans le schéma suivant.

![Vue d’ensemble](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Hôte de machine virtuelle
Les machines virtuelles dans Azure génèrent les données suivantes pour l’hôte de machine virtuelle comme pour les autres ressources Azure, comme décrit dans [Données de supervision](../essentials/monitor-azure-resource.md#monitoring-data).

- [Métriques de plateforme](../essentials/data-platform-metrics.md) – Valeurs numériques qui sont collectées automatiquement à intervalles réguliers et qui décrivent un certain aspect d’une ressource à un moment donné. Les métriques de plateforme sont collectées pour l’hôte de machine virtuelle, mais vous avez besoin de l’extension de diagnostic pour collecter les métriques du système d’exploitation invité.
- [Journal d’activité](../essentials/platform-logs-overview.md) : fournit des insights sur les opérations effectuées sur chaque ressource Azure dans l’abonnement à partir de l’extérieur (plan de gestion). Pour une machine virtuelle, cela comprend des informations comme le moment où elle a été démarrée et les changements de configuration.


### <a name="guest-operating-system"></a>Système d’exploitation invité
Pour collecter les données du système d’exploitation invité d’une machine virtuelle, vous avez besoin d’un agent qui s’exécute localement sur chaque machine virtuelle et envoie des données à Azure Monitor. Plusieurs agents sont disponibles pour Azure Monitor, chacun collectant des données différentes et écrivant les données dans différents emplacements. Obtenez une comparaison détaillée des différents agents dans l’article [Vue d’ensemble des agents Azure Monitor](../agents/agents-overview.md). 

- [Agent Log Analytics](../agents/agents-overview.md#log-analytics-agent) : disponible pour les machines virtuelles dans Azure, dans d’autres environnements cloud et localement. Collecte les données dans les journaux Azure Monitor. Prend en charge les solutions VM Insights et de surveillance. Il s’agit du même agent que celui utilisé pour System Center Operations Manager.
- [Dependency Agent](../agents/agents-overview.md#dependency-agent) : collecte les données des processus exécutés sur la machine virtuelle et leurs dépendances. S’appuie sur l’agent Log Analytics pour transmettre des données dans Azure, et prend en charge les solutions VM Insights, Service Map et Wire Data 2.0.
- [Extension de diagnostic Azure](../agents/agents-overview.md#azure-diagnostics-extension) : disponible pour les machines virtuelles Azure Monitor uniquement. Peut collecter des données dans plusieurs emplacements, mais principalement utilisé pour collecter des données de performances d’invité dans les métriques Azure Monitor pour les machines virtuelles Windows.
- [Agent Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) : collecte les données de performances des machines virtuelles Linux dans les métriques Azure Monitor.


## <a name="configuration-requirements"></a>Exigences de configuration
Pour activer toutes les fonctionnalités d’Azure Monitor afin de superviser une machine virtuelle, vous devez collecter les données de supervision de l’hôte de machine virtuelle et du système d’exploitation invité dans les [métriques Azure Monitor](../logs/data-platform-logs.md) et les [journaux Azure Monitor](../logs/data-platform-logs.md). Le tableau suivant liste la configuration à appliquer pour activer cette collecte. Vous pouvez choisir de ne pas effectuer toutes ces étapes en fonction de vos besoins spécifiques.

| Étape de configuration | Actions effectuées | Fonctionnalités activées |
|:---|:---|:---|
| Pas de configuration | - Métriques de plateforme hôte collectées dans les métriques.<br>- Journal d’activité collecté. | - Metrics Explorer pour l’hôte.<br>- Alertes de métriques pour l’hôte.<br>- Alertes de journal d’activité. |
| [Activer VM Insights](#enable-vm-insights) | - Agent Log Analytics installé.<br>- Dependency Agent installé.<br>- Données de performances d’invité collectées dans les journaux.<br>- Détails des processus et des dépendances collectés dans les journaux. | - Graphiques et classeurs de performances pour les données de performances d’invité.<br>- Requêtes de journal pour les données de performances d’invité.<br>- Alertes de journal pour les données de performances d’invité.<br>- Carte des dépendances. |
| [Installer l’extension de diagnostic et l’agent Telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Données de performances d’invité collectées dans les métriques. | - Metrics Explorer pour l’invité.<br>- Alertes de métriques pour l’invité.  |
| [Configurer l’espace de travail Log Analytics](#configure-log-analytics-workspace) | - Événements collectés à partir de l’invité. | - Requêtes de journal pour les événements d’invité.<br>- Alertes de journal pour les événements d’invité. |
| [Créer un paramètre de diagnostic pour la machine virtuelle](#collect-platform-metrics-and-activity-log) | - Métriques de plateforme collectées dans les journaux.<br>- Journal d’activité collecté dans les journaux. | - Requêtes de journal pour les métriques de l’hôte.<br>- Alertes de journal pour les métriques de l’hôte.<br>- Requêtes de journal pour le journal d’activité.

Chacune de ces étapes de configuration est décrite dans les sections suivantes.

### <a name="enable-vm-insights"></a>Activer VM Insights
[VM Insights](../vm/vminsights-overview.md) est une [insight](../monitor-reference.md) dans Azure Monitor, qui est le principal outil de surveillance de machines virtuelles dans Azure Monitor. Il fournit la valeur ajoutée suivante par rapport aux fonctionnalités standard d’Azure Monitor.

- Intégration simplifiée de l’agent Log Analytics et de Dependency Agent pour pouvoir superviser le système d’exploitation invité et les charges de travail d’une machine virtuelle. 
- Classeurs et graphiques de performances des tendances prédéfinis qui vous permettent d’analyser les principales métriques de performances du système d’exploitation invité de la machine virtuelle.
- Carte de dépendances qui affiche les processus en cours d’exécution sur chaque machine virtuelle, et les composants interconnectés avec d’autres machines et sources externes.

![Affichage de performance de VM Insights](media/monitor-vm-azure/vminsights-01.png)

![Affichage de carte de VM Insights](media/monitor-vm-azure/vminsights-02.png)


Activez VM Insights à partir de l’option **Insights** dans le menu de machine virtuelle du portail Azure. Pour plus d’informations et d’autres méthodes de configuration, consultez [Activer la vue d’ensemble de VM Insights](vminsights-enable-overview.md).

![Activer VM Insights](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Configurer l’espace de travail Log Analytics
L’agent Log Analytics que VM Insights utilise envoie des données à un [espace de travail Log Analytics](../logs/data-platform-logs.md). Vous pouvez activer la collecte de données de performances et d’événements supplémentaires, et d’autres données de supervision à partir de l’agent en configurant l’espace de travail Log Analytics. Vous avez à le configurer une seule fois, dans la mesure où tout agent qui se connecte à l’espace de travail télécharge automatiquement la configuration et commence immédiatement à collecter les données définies. 

Vous pouvez accéder à la configuration de l’espace de travail directement à partir de VM Insights en sélectionnant **Configuration de l’espace de travail** dans **Démarrer**. Cliquez sur le nom de l’espace de travail pour ouvrir son menu.

![Configuration de l’espace de travail](media/monitor-vm-azure/workspace-configuration.png)

Sélectionnez **Paramètres avancés** dans le menu de l’espace de travail, puis **Données** pour configurer les sources de données. Pour les agents Windows, sélectionnez **Journaux des événements Windows** et ajoutez des journaux d’événements courants, comme *Système* et *Application*. Pour les agents Linux, sélectionnez **Syslog** et ajoutez des fonctionnalités communes, comme *crénage* et *démon*. Pour obtenir la liste des sources de données disponibles et savoir comment les configurer, consultez [Sources de données d’agent dans Azure Monitor](../agents/agent-data-sources.md). 

![Configurer des événements](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Vous pouvez configurer les compteurs de performances à collecter dans la configuration de l’espace de travail, mais cela peut être redondant avec les compteurs de performance que VM Insights collecte. VM Insights collecte l’ensemble de compteurs le plus courant toutes les minutes. Configurez uniquement les compteurs de performance à collecter par l’espace de travail si vous voulez collecter des compteurs que VM Insights ne collecte pas encore ou si vous avez des requêtes qui utilisent des données de performances.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Activer l’extension de diagnostic et l’agent Telegraf
VM Insights est basé sur l’agent Log Analytics qui envoie des données à un espace de travail Log Analytics. Il prend en charge plusieurs fonctionnalités d’Azure Monitor comme les [requêtes de journal](../logs/log-query-overview.md), les [alertes de journal](../alerts/alerts-log.md) et les [classeurs](../visualize/workbooks-overview.md). L’[extension de diagnostic](../agents/diagnostics-extension-overview.md) collecte les données de performances du système d’exploitation invité des machines virtuelles Windows dans le stockage Azure et envoie éventuellement des données de performances aux [métriques Azure Monitor](../essentials/data-platform-metrics.md). Pour les machines virtuelles Linux, l’[agent Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) est nécessaire pour envoyer des données aux métriques Azure.  Il active d’autres fonctionnalités d’Azure Monitor comme [Metrics Explorer](../essentials/metrics-getting-started.md) et les [alertes de métriques](../alerts/alerts-metric.md). Vous pouvez également configurer l’extension de diagnostic pour envoyer des événements et des données de performances en dehors d’Azure Monitor à l’aide d’Azure Event Hubs.

Installez l’extension de diagnostic pour une seule machine virtuelle Windows dans le portail Azure à partir de l’option **Paramètre de diagnostic** dans le menu de machine virtuelle. Sélectionnez l’option pour activer **Azure Monitor** sous l’onglet **Récepteurs**. Pour activer l’extension à partir d’un modèle ou d’une ligne de commande pour plusieurs machines virtuelles, consultez [Installation et configuration](../agents/diagnostics-extension-overview.md#installation-and-configuration). Contrairement à l’agent Log Analytics, les données à collecter sont définies dans la configuration de l’extension sur chaque machine virtuelle.

![Paramètre de diagnostic](media/monitor-vm-azure/diagnostic-setting.png)

Pour plus d’informations sur la configuration des agents Telegraf sur les machines virtuelles Linux, consultez [Installer et configurer Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf). L’option de menu **Paramètre de diagnostic** est disponible pour Linux, mais elle vous permet uniquement d’envoyer des données au stockage Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Collecter les métriques de plateforme et le journal d’activité
Vous pouvez voir les métriques de plateforme et le journal d’activité collectés pour chaque hôte de machine virtuelle dans le portail Azure. Collectez ces données dans le même espace de travail Log Analytics que celui de VM Insights afin de les analyser avec les autres données de surveillance collectées pour la machine virtuelle. Cette collection est configurée avec un [paramètre de diagnostic](../essentials/diagnostic-settings.md). Collectez le journal d’activité avec un [paramètre de diagnostic pour l’abonnement](../essentials/diagnostic-settings.md#create-in-azure-portal).

Collectez les métriques de plateforme avec un paramètre de diagnostic pour la machine virtuelle. Contrairement aux autres ressources Azure, vous ne pouvez pas créer de paramètre de diagnostic pour une machine virtuelle dans le portail Azure et devez utiliser une [autre méthode](../essentials/diagnostic-settings.md#create-using-powershell). Les exemples suivants montrent comment collecter des métriques pour une machine virtuelle à l’aide de PowerShell et de l’interface CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```azurecli
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Surveillance dans le portail Azure 
Une fois que vous avez configuré la collecte des données de supervision pour une machine virtuelle, vous avez plusieurs options pour y accéder dans le portail Azure :

- Utilisez le menu **Azure Monitor** pour accéder aux données de toutes les ressources supervisées. 
- Utilisez VM Insights pour surveiller des ensembles de machines virtuelles à grande échelle.
- Analysez les données d’une seule machine virtuelle à partir de son menu dans le portail Azure. Le tableau ci-dessous liste les différentes options de supervision du menu de machines virtuelles.

![Surveillance dans le portail Azure](media/monitor-vm-azure/monitor-menu.png)

| Option de menu | Description |
|:---|:---|
| Vue d’ensemble | Affiche les [métriques de plateforme](../essentials/data-platform-metrics.md) pour l’hôte de machine virtuelle. Cliquez sur un graphique pour utiliser ces données dans [Metrics Explorer](../essentials/metrics-getting-started.md). |
| Journal d’activité | Les entrées du [journal d’activité](../essentials/activity-log.md#view-the-activity-log) sont filtrées pour la machine virtuelle actuelle. |
| Insights | Ouvre [VM Insights](../vm/vminsights-overview.md) avec la carte de la machine virtuelle active sélectionnée. |
| Alertes | Montre les [alertes](../alerts/alerts-overview.md) de la machine virtuelle actuelle.  |
| Mesures | Ouvre [Metrics Explorer](../essentials/metrics-getting-started.md) avec l’étendue définie sur la machine virtuelle actuelle. |
| Paramètres de diagnostic | Active et configure l’[extension de diagnostic](../agents/diagnostics-extension-overview.md) pour la machine virtuelle actuelle. |
| Recommandations d’Advisor | Recommandations d’[Azure Advisor](../../advisor/index.yml) pour la machine virtuelle actuelle. |
| Journaux d’activité | Ouvre [Log Analytics](../logs/log-analytics-overview.md) avec l’[étendue](../logs/scope.md) définie sur la machine virtuelle actuelle. |
| Moniteur de connexion | Ouvre le [moniteur de connexion Network Watcher](../../network-watcher/connection-monitor-overview.md) pour superviser les connexions entre la machine virtuelle actuelle et les autres machines virtuelles. |


## <a name="analyzing-metric-data"></a>Analyse des données de métrique
Vous pouvez analyser les métriques des machines virtuelles à l’aide de Metrics Explorer en ouvrant **Métriques** à partir du menu de la machine virtuelle. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../essentials/metrics-getting-started.md). 

Deux espaces de noms sont utilisés par les machines virtuelles pour les métriques :

| Espace de noms | Description | Condition requise |
|:---|:---|:---|
| Hôte de machine virtuelle | Les métriques d’hôte sont automatiquement collectées pour toutes les machines virtuelles Azure. Liste détaillée des métriques sur [Microsoft.Compute/virtualMachines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines). | Informations collectées automatiquement sans configuration requise. |
| Invité (classique) | Ensemble limité de données sur les performances du système d’exploitation invité et les applications. Disponible dans Metrics Explorer, mais non avec d’autres fonctionnalités de Microsoft Azure Monitor telles que les alertes de mesures.  | [Extension de diagnostic](../agents/diagnostics-extension-overview.md) installée. Les données sont lues à partir du stockage Azure.  |
| Invité de machine virtuelle | Les données sur les performances des applications et du système d’exploitation invité sont disponibles pour toutes les fonctionnalités de Microsoft Azure Monitor, via des mesures. | Pour Windows : [extension de diagnostic installée](../agents/diagnostics-extension-overview.md) avec récepteur Azure Monitor activé. Pour Linux : [agent Telegraf installé](../essentials/collect-custom-metrics-linux-telegraf.md). |

![Explorateur de métriques dans le portail Azure](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analyse des données de journal
Les machines virtuelles Azure collectent les données suivantes dans les journaux Azure Monitor. 

VM Insights active la collecte d’un ensemble prédéterminé de compteurs de performances écrits dans la table *InsightsMetrics*. Il s’agit de la table qu’utilise [Container insights](../containers/container-insights-overview.md). 

| Source de données | Spécifications | Tables |
|:---|:---|:---|
| Insights de machine virtuelle | Activez-la sur chaque machine virtuelle. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Pour plus de détails, consultez [Comment interroger des journaux à partir de VM Insights](../vm/vminsights-log-search.md). |
| Journal d’activité | Paramètre de diagnostic pour l’abonnement. | AzureActivity |
| Métriques d’hôte | Paramètre de diagnostic pour la machine virtuelle. | AzureMetrics |
| Sources de données du système d’exploitation invité | Activez l’agent Log Analytics et configurez les sources de données. | Consultez la documentation de chaque source de données. |


> [!NOTE]
> Les données de performances que l’agent Log Analytics collecté sont écrites dans la table *Perf*, tandis que VM Insights les collecte dans la table *InsightsMetrics*. Il s’agit des mêmes données, mais les tables ont une structure différente. Si vous avez des requêtes basées sur *Perf*, elles doivent être réécrites pour utiliser *InsightsMetrics*.


## <a name="alerts"></a>Alertes
Les [alertes](../alerts/alerts-overview.md) dans Azure Monitor vous avertissent de manière proactive quand des conditions importantes sont trouvées dans vos données de supervision, et lancent éventuellement une action comme le démarrage d’une application logique ou l’appel d’un webhook. Les règles d’alerte définissent la logique utilisée pour déterminer quand une alerte doit être créée. Azure Monitor collecte les données utilisées par les règles d’alerte, mais vous devez créer des règles pour définir les conditions d’alerte dans votre abonnement Azure.

Les sections suivantes décrivent les types de règles d’alerte et les recommandations d’utilisation de chacune d’elles. Cette recommandation est basée sur les fonctionnalités et le coût du type de règle d’alerte. Pour plus d’informations sur les prix des alertes, consultez [Prix Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Règles d’alerte de journal d’activité
Les [règles d’alerte du journal d’activité](../alerts/alerts-activity-log.md) se déclenchent quand une entrée correspondant à des critères particuliers est créée dans le journal d’activité. Elles n’ont pas de coût et doivent être votre premier choix si la logique dont vous avez besoin est dans le journal d’activité. 

La ressource cible pour les alertes du journal d’activité peut être une machine virtuelle spécifique, toutes les machines virtuelles d’un groupe de ressources ou toutes les machines virtuelles d’un abonnement.

Par exemple, créez une alerte si une machine virtuelle critique est arrêtée en sélectionnant *Mettre hors tension la machine virtuelle* comme nom du signal.

![Alerte du journal d’activité](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Règles d’alerte de métrique
Les [règles d’alerte de métrique](../alerts/alerts-metric.md) se déclenchent quand une valeur de métrique dépasse un seuil. Vous pouvez définir une valeur de seuil spécifique ou autoriser Azure Monitor à déterminer de façon dynamique un seuil basé sur des données d’historique.  Utilisez des alertes de métrique dans la mesure du possible avec des données de métrique, car elles coûtent moins cher et sont plus réactives que les règles d’alerte de journal. Elles sont également associées à un état, ce qui signifie qu’elles se résolvent elle-mêmes quand la métrique descend en dessous du seuil.

La ressource cible pour les alertes du journal d’activité peut être une machine virtuelle spécifique ou toutes les machines virtuelles d’un groupe de ressources.

Par exemple, pour créer une alerte quand le processeur d’une machine virtuelle dépasse une valeur particulière, créez une règle d’alerte de métrique en utilisant *Pourcentage de processeur* comme type de signal. Définissez une valeur de seuil spécifique ou autorisez Azure Monitor à définir un seuil dynamique. 

![Alerte de métrique](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alertes de journal
Les [règles d’alerte de journal](../alerts/alerts-log.md) se déclenchent quand les résultats d’une requête de journal planifiée correspondent à certains critères. Les alertes de requête de journal sont les plus coûteuses et les moins réactives des règles d’alerte, mais elles ont accès aux données les plus variées et peuvent exécuter une logique complexe qui ne peut pas être exécutée par les autres règles d’alerte. 

La ressource cible d’une requête de journal est un espace de travail Log Analytics. Filtrez des ordinateurs spécifiques dans la requête.

Par exemple, pour créer une alerte qui vérifie si des machines virtuelles dans un groupe de ressources en particulier sont hors connexion, utilisez la requête suivante qui retourne un enregistrement pour chaque ordinateur qui a manqué une pulsation au cours des dix dernières minutes. Utilisez un seuil de 1 qui déclenche l’alerte si au moins un ordinateur manque une pulsation.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alerte de journal pour les pulsations manquées](media/monitor-vm-azure/log-alert-01.png)

Pour créer une alerte si un nombre excessif d’échecs d’ouverture de session se sont produits sur des machines virtuelles Windows de l’abonnement, utilisez la requête suivante qui retourne un enregistrement pour chaque événement d’échec d’ouverture de session dans la dernière heure. Utilisez un seuil défini sur le nombre d’échecs d’ouverture de session que vous autorisez. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Alerte de journal pour les échecs d’ouverture de session](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager fournit une supervision granulaire des charges de travail sur les machines virtuelles. Pour obtenir une comparaison des plateformes de supervision et les différentes stratégies d’implémentation, consultez le [Guide de supervision du cloud](/azure/cloud-adoption-framework/manage/monitor/).

Si vous voulez utiliser un de vos environnements Operations Manager existants, vous pouvez l’intégrer à Azure Monitor pour fournir des fonctionnalités supplémentaires. L’agent Log Analytics utilisé par Azure Monitor est le même que celui utilisé pour Operations Manager. De cette façon vos machines virtuelles supervisées peuvent envoyer des données aux deux. Vous devez toujours ajouter l’agent à VM Insights et configurer l’espace de travail pour collecter des données supplémentaires comme indiqué ci-dessus, mais les machines virtuelles peuvent continuer à exécuter leurs packs d’administration existants dans un environnement Operations Manager sans modification.

Les fonctionnalités d’Azure Monitor qui s’ajoutent à celles déjà existantes d’Operations Manager sont les suivantes :

- Utilisez Log Analytics pour analyser de manière interactive vos données de journal et de performances.
- Utilisez les alertes de journal pour définir des conditions d’alerte sur plusieurs machines virtuelles et l’utilisation de tendances à long terme qui ne sont pas possibles avec les alertes dans Operations Manager.   

Pour plus d’informations sur la connexion de votre groupe d’administration Operations Manager existant à votre espace de travail Log Analytics, consultez [Connecter Operations Manager à Azure Monitor](../agents/om-agents.md).


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment analyser des données dans des journaux Azure Monitor à l’aide de requêtes de journal.](../logs/get-started-queries.md)
* [Découvrez plus d’informations sur les alertes à l’aide des métriques et des journaux dans Azure Monitor.](../alerts/alerts-overview.md)