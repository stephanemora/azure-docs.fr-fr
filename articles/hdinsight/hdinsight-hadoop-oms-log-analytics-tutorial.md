---
title: Journaux d’utiliser Azure Monitor pour surveiller les clusters Azure HDInsight
description: Découvrez comment utiliser les journaux d’Azure Monitor pour surveiller les travaux en cours d’exécution dans un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 16659a335ef6126e75f5a9a99784e71afa056bef
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479262"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Journaux d’utiliser Azure Monitor pour surveiller les clusters HDInsight

Découvrez comment activer les journaux d’Azure Monitor surveiller les opérations de cluster Hadoop dans HDInsight et comment ajouter une solution de surveillance de HDInsight.

[Journaux d’analyse Azure](../log-analytics/log-analytics-overview.md) est un service dans Azure Monitor qui surveille votre cloud locales et dans des environnements pour maintenir leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

* **Un espace de travail Log Analytics**. Vous pouvez considérer cet espace de travail comme un environnement de journaux Azure Monitor unique avec son propre référentiel de données, sources de données et des solutions. Pour obtenir des instructions, consultez la rubrique [Créer un espace de travail Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Un **cluster Azure HDInsight**. Actuellement, vous pouvez utiliser les journaux Azure Monitor avec les types de cluster HDInsight suivants :

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Pour savoir comment créer un cluster HDInsight, consultez [Bien démarrer avec Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Module PowerShell Az Azure**.  Consultez [présentation du nouveau module Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Il est recommandé de placer le cluster HDInsight et l’espace de travail Log Analytics dans la même région pour obtenir de meilleures performances. Journaux d’Azure Monitor n’est pas disponible dans toutes les régions Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Activer les journaux d’Azure Monitor à l’aide du portail

Cette section vous explique comment configurer un cluster Hadoop HDInsight existant pour utiliser un espace de travail Azure Log Analytics pour surveiller les travaux, les journaux d’activité de débogage, etc.

1. À partir de la [Azure portal](https://portal.azure.com/), sélectionnez votre cluster.  Pour obtenir des instructions, consultez la page [Énumération et affichage des clusters](./hdinsight-administer-use-portal-linux.md#showClusters). Le cluster est ouvert dans une nouvelle page de portail.

1. À partir de la gauche, sous **Supervision**, sélectionnez **Operations Management Suite**.

1. À partir de la vue principale, sous **Supervision OMS**, sélectionnez **Activer**.

1. Dans la liste déroulante **Sélectionner un espace de travail**, sélectionnez un espace de travail Log Analytics existant.

1. Sélectionnez **Enregistrer**.  L’enregistrement du paramètre prend quelques instants.

    ![Activer la surveillance des clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Activer la surveillance des clusters HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Activer les journaux d’Azure Monitor à l’aide d’Azure PowerShell

Vous pouvez activer les journaux d’Azure Monitor à l’aide du module Azure PowerShell Az [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) applet de commande.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Pour désactiver, l’utilisation du [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) applet de commande :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installer des solutions de gestion de clusters HDInsight

HDInsight fournit des solutions de gestion de cluster spécifiques que vous pouvez ajouter pour les journaux d’Azure Monitor. [Solutions de gestion](../log-analytics/log-analytics-add-solutions.md) ajouter des fonctionnalités pour les journaux Azure Monitor, fournissant des données et les outils d’analyse. Ces solutions collectent des indicateurs de performance importants de vos clusters HDInsight et fournissent les outils pour rechercher les indicateurs. Elles fournissent également des visualisations et des tableaux de bord pour la plupart des types de cluster pris en charge dans HDInsight. En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes personnalisées de surveillance.

Voici les solutions HDInsight disponibles :

* HDInsight Hadoop Monitoring
* HDInsight HBase Monitoring
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

Pour obtenir des instructions sur l’installation d’une solution de gestion, consultez la rubrique sur les [solutions de gestion dans Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Pour faire des essais, installer une solution de surveillance de HDInsight Hadoop. Vous voyez alors une vignette **HDInsightHadoop** sous **Résumé**. Sélectionnez la vignette **HDInsightHadoop**. La solution HDInsightHadoop ressemble à ceci :

![Vue de la solution de supervision HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Étant donné que le cluster est tout nouveau, le rapport ne contient aucune activité.

## <a name="next-steps"></a>Étapes suivantes

* [Journaux de requête Azure Monitor pour surveiller des clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
