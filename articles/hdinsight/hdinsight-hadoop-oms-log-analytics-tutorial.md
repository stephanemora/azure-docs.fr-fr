---
title: Utiliser les journaux Azure Monitor pour surveiller les clusters Azure HDInsight
description: Découvrez comment utiliser les journaux d’activité Azure Monitor pour surveiller les travaux en cours d’exécution dans un cluster HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurepowershell, references_regions
ms.date: 09/21/2021
ms.openlocfilehash: c4fc351105c82213549fdb357d19b480c5a51ed4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648028"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Utiliser les journaux d’activité Azure Monitor pour superviser les clusters HDInsight

Découvrez comment activer les journaux Azure Monitor pour superviser les opérations de cluster Hadoop dans HDInsight et comment ajouter une solution de supervision HDInsight.

Les [journaux Azure Monitor](../azure-monitor/logs/log-query-overview.md) sont un service Azure Monitor qui supervise vos environnements Cloud et locaux. La supervision consiste à maintenir leur disponibilité et leurs performances. Elle collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils de supervision. Les données sont utilisées pour fournir une analyse englobant plusieurs sources.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

#### <a name="new-azure-monitor-experience"></a>[Nouvelle expérience Azure Monitor](#tab/new)

> [!Important]
> Une nouvelle expérience Azure Monitor est disponible uniquement aux USA Est et en Europe Ouest en tant que fonctionnalité en préversion.  
>

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Log Analytics. Considérez cet espace de travail comme un environnement des journaux d’activité Azure Monitor avec son propre référentiel de données, et ses propres sources de données et solutions. Pour obtenir des instructions, consultez la rubrique [Créer un espace de travail Log Analytics](../azure-monitor/vm/monitor-virtual-machine.md).

* Un cluster Azure HDInsight. Vous pouvez actuellement utiliser les journaux d’activité Azure Monitor avec les types de cluster HDInsight suivants :

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Pour savoir comment créer un cluster HDInsight, consultez [Bien démarrer avec Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Si vous utilisez PowerShell, vous aurez besoin du [module Az](/powershell/azure/). Vérifiez que vous avez la version la plus récente. Si nécessaire, exécutez `Update-Module -Name Az`.

* Si vous voulez utiliser Azure CLI et que vous ne l’avez pas encore installé, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Une nouvelle expérience Azure Monitor est disponible uniquement aux USA Est et en Europe Ouest en tant que fonctionnalité d’évaluation. Il est recommandé de placer le cluster HDInsight et l’espace de travail Log Analytics dans la même région pour obtenir de meilleures performances. Notez que les journaux d’activité Azure Monitor ne sont pas disponibles dans toutes les régions Azure.
>

## <a name="enable-azure-monitor-using-the-portal"></a>Activer Azure Monitor à l’aide du portail

Cette section vous explique comment configurer un cluster Hadoop HDInsight existant pour utiliser un espace de travail Azure Log Analytics afin de superviser les travaux, les journaux de débogage, etc.

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre cluster. Le cluster est ouvert dans une nouvelle page du portail.

2. Depuis la gauche, sous Analyse, sélectionnez  **Intégration de l’analyse**. 

3. À partir de l’affichage principal, sous  **Azure Monitor pour l’intégration de clusters HDInsight**, sélectionnez  **Activer**. 

4. Dans la liste déroulante  **Sélectionner un espace de travail** , sélectionnez un espace de travail Log Analytics existant. 

5. Sélectionnez  **Enregistrer**. L’enregistrement du paramètre prend quelques instants. 

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-azure-monitor.png" alt-text="Activer la supervision pour les clusters HDInsight":::

Si vous souhaitez désactiver Azure Monitor, vous pouvez faire de même dans ce portail. 

## <a name="enable-azure-monitor-using-azure-powershell"></a>Activer Azure Monitor à l’aide d’Azure PowerShell

Vous pouvez activer des journaux Azure Monitor à l’aide de la cmdlet [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) du module Az Azure PowerShell.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster
```

Pour désactiver, utilisez la cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) :

```powershell
Disable-AzHDInsightAzureMonitor -ResourceGroupName $resourceGroup `
-ClusterName $cluster
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Activer Azure Monitor à l’aide d’Azure CLI

Vous pouvez activer les journaux Azure Monitor à l’aide de la commande Azure CLI [`az hdinsight azure-monitor enable`](/cli/azure/hdinsight/azure-monitor?view=azure-cli-latest&preserve-view=true).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor show --name $cluster --resource-group $resourceGroup
```

Pour les désactiver, utilisez la commande [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable).

```azurecli
az hdinsight azure-monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="use-hdinsight-out-of-box-insights-to-monitor-a-single-cluster"></a>Utilisez des insights HDInsight prêts à l’emploi pour analyser un seul cluster

HDInsight fournit un classeur spécifique à la charge de travail pour vous aider à obtenir rapidement des Insights. Ce classeur collecte des mesures de niveau de performance importantes à partir de votre cluster HDInsight et fournit des visualisations et des tableaux de bord pour les scénarios les plus courants. Les insights prêts à l’emploi offrent une vue complète d’un cluster HDInsight unique, y compris l’utilisation des ressources et l’état des applications.

Classeurs HDInsight disponibles :

- Classeur HDInsight Spark
- Classeur HDInsight Kafka
- Classeur HDInsight HBase
- Classeur HDInsight Hive/LLAP
- Classeur Storm HDInsight

Capture d’écran du classeur Spark :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-spark-workbook.png" alt-text="capture d’écran du classeur Spark":::

## <a name="use-at-scale-insights-to-monitor-multiple-clusters"></a>Utiliser des insights à l’échelle pour analyser plusieurs clusters

Vous pouvez vous connecter au Portail Azure et sélectionner Analyse. Dans la section **Insight**, vous pouvez sélectionner **Hub Insights**. Vous trouverez ensuite les clusters HDInsight.

Dans cette affichage, vous pouvez analyser plusieurs clusters HDInsight dans un même emplacement.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-monitor-insights.png" alt-text="Capture d’écran des insights du moniteur de clusters":::

Vous pouvez sélectionner l’abonnement et les clusters HDInsight que vous souhaitez analyser. 
 - L’écran **Clusters analysés** affiche le nombre de clusters pour lesquels l’intégration Azure Monitor a été activée.
 - L’écran **Clusters non analysés** affiche le nombre de clusters pour lesquels l’intégration Azure Monitor n’a pas été activée.

Vous pouvez voir la liste détaillée des clusters dans chaque section. 

Dans l’onglet **Vue d’ensemble**, sous **Clusters analysés**, vous pouvez voir le type de cluster, les alertes critiques et les utilisations de ressources.
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-cluster-alerts.png" alt-text="Capture d’écran des alertes du moniteur de clusters":::

Vous pouvez également voir les clusters dans chaque type de charge de travail, notamment Spark, HBase, Hive, Kafka et Storm.

Les mesures de haut niveau de chaque type de charge de travail sont présentées, y compris le nombre de managers de nœuds actifs, le nombre d’applications en cours d’exécution, etc.

:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/spark-metrics.png" alt-text="Mesures Spark du moniteur de clusters":::

## <a name="configuring-performance-counters"></a>Configuration des compteurs de performances

Azure Monitor prend en charge la collecte et l’analyse des métriques de performances pour les nœuds de votre cluster. Pour plus d’informations, consultez [Sources de données de performances Linux dans Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Audit de cluster

HDInsight prend en charge l’audit de cluster avec des journaux Azure Monitor en important les types de journaux suivants :

* `log_gateway_audit_CL` : cette table répertorie les journaux d’audit des nœuds de passerelle de clusters qui affichent les réussites et les échecs des tentatives de connexion.
* `log_auth_CL` : cette table répertorie les journaux SSH contenant les réussites et les échecs des tentatives de connexion.
* `log_ambari_audit_CL` : ce tableau répertorie les journaux d’audit d’Ambari.
* `log_ranger_audti_CL` : ce tableau répertorie les journaux d’audit d’Apache Ranger sur des clusters ESP.


#### <a name="classic-azure-monitor-experience"></a>[Expérience Azure Monitor classique](#tab/previous)

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Log Analytics. Considérez cet espace de travail comme un environnement des journaux d’activité Azure Monitor avec son propre référentiel de données, et ses propres sources de données et solutions. Pour obtenir des instructions, consultez la rubrique [Créer un espace de travail Log Analytics](../azure-monitor/vm/monitor-virtual-machine.md).

* Si vous prévoyez d’utiliser l’intégration d’Azure Monitor sur un cluster derrière un pare-feu, vous devez satisfaire aux [prérequis pour les clusters derrière un pare-feu](#oms-with-firewall).

* Un cluster Azure HDInsight. Vous pouvez actuellement utiliser les journaux d’activité Azure Monitor avec les types de cluster HDInsight suivants :

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Pour savoir comment créer un cluster HDInsight, consultez [Bien démarrer avec Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Si vous utilisez PowerShell, vous aurez besoin du [module Az](/powershell/azure/). Vérifiez que vous avez la version la plus récente. Si nécessaire, exécutez `Update-Module -Name Az`.

* Si vous voulez utiliser Azure CLI et que vous ne l’avez pas encore installé, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Il est recommandé de placer le cluster HDInsight et l’espace de travail Log Analytics dans la même région pour obtenir de meilleures performances. Notez que les journaux d’activité Azure Monitor ne sont pas disponibles dans toutes les régions Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Activer Azure Monitor à l’aide du portail

Cette section vous explique comment configurer un cluster Hadoop HDInsight existant pour utiliser un espace de travail Azure Log Analytics afin de superviser les travaux, les journaux de débogage, etc.

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre cluster. Le cluster est ouvert dans une nouvelle page du portail.

1. À gauche, sous **Supervision**, sélectionnez **Azure Monitor**.

1. Dans la vue principale, sous **Intégration à Azure Monitor**, sélectionnez **Activer**.

1. Dans la liste déroulante **Sélectionner un espace de travail**, sélectionnez un espace de travail Log Analytics existant.

1. Sélectionnez **Enregistrer**.  L’enregistrement du paramètre prend quelques instants.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Activer la supervision pour les clusters HDInsight":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Activer Azure Monitor à l’aide d’Azure PowerShell

Vous pouvez activer les journaux Azure Monitor à l’aide de l’applet de commande [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) du module Az Azure PowerShell.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Pour désactiver, utilisez l’applet de commande [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Activer Azure Monitor à l’aide d’Azure CLI

Vous pouvez activer les journaux d’activité d’Azure Monitor à l’aide de la commande Azure CLI `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Pour les désactiver, utilisez la commande [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable).

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```
## <a name=""></a><a name="oms-with-firewall">Prérequis pour les clusters derrière un pare-feu</a>

Pour pouvoir configurer correctement l’intégration d’Azure Monitor à HDInsight derrière un pare-feu, certains clients peuvent avoir besoin d’activer les points de terminaison suivants :

|Ressource de l'agent | Ports | Sens | Ignorer l’inspection HTTPS |
|---|---|---|---|
| \*.ods.opinsights.azure.com | Port 443 | Règle de trafic sortant | Oui |
| \*.oms.opinsights.azure.com |Port 443 | Règle de trafic sortant | Oui |
| \*.azure-automation.net | Port 443 | Règle de trafic sortant | Oui |

Si vous avez des restrictions de sécurité liées à l’activation de points de terminaison de stockage génériques, il existe une autre option. Vous pouvez effectuer à la place les actions suivantes :

1. Créer un compte de stockage dédié
2. Configurer le compte de stockage dédié sur son espace de travail Log Analytics
3. Activer ce compte de stockage dédié dans son pare-feu

### <a name="data-collection-behind-a-firewall"></a>Collecte de données derrière un pare-feu
Une fois la configuration effectuée, l’activation des points de terminaison nécessaires pour l’ingestion des données est importante. Il est recommandé d’activer le point de terminaison \*.blob.core.windows.net pour que l’ingestion des données réussisse.


## <a name="install-hdinsight-cluster-management-solutions"></a>Installer des solutions de gestion de clusters HDInsight

HDInsight fournit des solutions de gestion de clusters que vous pouvez ajouter pour les journaux d’activité Azure Monitor. Les [solutions de gestion](../azure-monitor/insights/solutions.md) ajoutent des fonctionnalités aux journaux Azure Monitor en fournissant des données et des outils d’analyse supplémentaires. Ces solutions collectent des métriques de performance importantes de vos clusters HDInsight et fournissent les outils permettant d’explorer les métriques. Elles fournissent également des visualisations et des tableaux de bord pour la plupart des types de cluster pris en charge dans HDInsight. En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes personnalisées de surveillance.

Solutions HDInsight disponibles :

* HDInsight Hadoop Monitoring
* HDInsight HBase Monitoring
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

Pour obtenir des instructions sur les solutions de gestion, consultez [Solutions de gestion dans Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Pour faire des essais, installez une solution de supervision HDInsight Hadoop. Vous voyez alors une vignette **HDInsightHadoop** sous **Résumé**. Sélectionnez la vignette **HDInsightHadoop**. La solution HDInsightHadoop ressemble à ceci :

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="Vue de la solution de supervision HDInsight":::

Étant donné que le cluster est tout nouveau, le rapport ne contient aucune activité.

## <a name="configuring-performance-counters"></a>Configuration des compteurs de performances

Azure Monitor prend en charge la collecte et l’analyse des métriques de performances pour les nœuds de votre cluster. Pour plus d’informations, consultez [Sources de données de performances Linux dans Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Audit de cluster

HDInsight prend en charge l’audit de cluster avec des journaux Azure Monitor en important les types de journaux suivants :

* `log_gateway_audit_CL` : cette table répertorie les journaux d’audit des nœuds de passerelle de clusters qui affichent les réussites et les échecs des tentatives de connexion.
* `log_auth_CL` : cette table répertorie les journaux SSH contenant les réussites et les échecs des tentatives de connexion.
* `log_ambari_audit_CL` : ce tableau répertorie les journaux d’audit d’Ambari.
* `log_ranger_audti_CL` : ce tableau répertorie les journaux d’audit d’Apache Ranger sur des clusters ESP.

---

## <a name="update-the-log-analytics-oms-agent-used-by-hdinsight-azure-monitor-integration"></a>Mettre à jour l’agent Log Analytics (OMS) utilisé par l’intégration d’Azure Monitor à HDInsight

Quand l’intégration d’Azure Monitor est activée sur un cluster, l’agent Log Analytics ou l’agent Operations Management Suite (OMS) est installé sur le cluster et n’est pas mis à jour, sauf si vous désactivez et que vous réactivez l’intégration d’Azure Monitor. Effectuez les étapes suivantes si vous devez mettre à jour l’agent OMS sur le cluster. Si vous êtes derrière un pare-feu, il peut être nécessaire de répondre aux [prérequis pour les clusters derrière un pare-feu](#oms-with-firewall) avant d’effectuer ces étapes.

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre cluster. Le cluster est ouvert dans une nouvelle page du portail.
1. À gauche, sous **Supervision**, sélectionnez **Azure Monitor**.
1. Prenez note du nom de votre espace de travail Log Analytics actuel.
1. Dans la vue principale, sous **Intégration à Azure Monitor**, désactivez le commutateur, puis sélectionnez **Enregistrer**. 
1. Une fois le paramètre enregistré, réactivez le commutateur **Intégration à Azure Monitor** et vérifiez que le même espace de travail Log Analytics est sélectionné, puis sélectionnez **Enregistrer**.

Si vous avez Intégration à Azure Monitor activé sur un cluster, la mise à jour de l’agent OMS met également à jour la version d’Open Management Infrastructure (OMI). Vous pouvez vérifier la version d’OMI sur le cluster en exécutant la commande suivante : 

```
 sudo /opt/omi/bin/omiserver –version
```

## <a name="next-steps"></a>Étapes suivantes

* [Interroger les journaux Azure Monitor pour surveiller les clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Superviser la disponibilité des clusters à l’aide d’Apache Ambari et des journaux Azure Monitor](./hdinsight-cluster-availability.md)
