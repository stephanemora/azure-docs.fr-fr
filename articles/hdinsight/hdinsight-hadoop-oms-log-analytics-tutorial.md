---
title: Utiliser Log Analytics pour surveiller les clusters Azure HDInsight
description: Découvrez comment utiliser Azure Log Analytics pour surveiller les travaux en cours d’exécution dans un cluster HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cd129ea68315223516ac1cd3e7577b5ee4bf92e5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005112"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Utiliser Azure Log Analytics pour surveiller les clusters HDInsight

Découvrez comment activer Azure Log Analytics pour surveiller les opérations de cluster Hadoop dans HDInsight, et comment ajouter une solution de surveillance HDInisght.

[Log Analytics](../log-analytics/log-analytics-overview.md) est un service Azure Monitor qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* **Un espace de travail Log Analytics**. Considérez cet espace de travail comme un environnement Log Analytics unique avec son propre référentiel de données, et ses propres sources de données et solutions. Pour obtenir des instructions, consultez la rubrique [Créer un espace de travail Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Un **cluster Azure HDInsight**. Vous pouvez actuellement utiliser Log Analytics avec les types de clusters HDInsight suivants :

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Pour savoir comment créer un cluster HDInsight, consultez [Bien démarrer avec Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]  
> Il est recommandé de placer le cluster HDInsight et l’espace de travail Log Analytics dans la même région pour obtenir de meilleures performances. Notez qu’Azure Log Analytics n’est pas disponible dans toutes les régions Azure.

## <a name="enable-log-analytics-by-using-the-portal"></a>Activer Log Analytics à l’aide du portail

Cette section vous explique comment configurer un cluster Hadoop HDInsight existant pour utiliser un espace de travail Azure Log Analytics pour surveiller les travaux, les journaux de débogage, etc.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, sélectionnez **Tous les services**.

3. Sous **ANALYTIQUE**, sélectionnez **Clusters HDInsight**.

4. À partir de la gauche, sous **Supervision**, sélectionnez **Operations Management Suite**.

5. À partir de la vue principale, sous **Supervision OMS**, sélectionnez **Activer**.

6. Dans la liste déroulante **Sélectionner un espace de travail**, sélectionnez un espace de travail Log Analytics existant.

7. Sélectionnez **Enregistrer**.

    ![Activer la surveillance des clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Activer la surveillance des clusters HDInsight")

    L’enregistrement du paramètre prend quelques instants.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Activer Log Analytics à l’aide d’Azure PowerShell

Vous pouvez activer Log Analytics à l’aide d’Azure PowerShell. L’applet de commande est :

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

Consultez [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Pour la désactivation, la cmdlet est :

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

Consultez [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Installer des solutions de gestion de clusters HDInsight

HDInsight fournit des solutions de gestion de clusters que vous pouvez ajouter pour Azure Log Analytics. Les [solutions de gestion](../log-analytics/log-analytics-add-solutions.md) ajoutent des fonctionnalités à Log Analytics en fournissant des données et des outils d’analyse supplémentaires. Ces solutions collectent des indicateurs de performance importants de vos clusters HDInsight et fournissent les outils pour rechercher les indicateurs. Elles fournissent également des visualisations et des tableaux de bord pour la plupart des types de cluster pris en charge dans HDInsight. En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes personnalisées de surveillance.

Voici les solutions HDInsight disponibles :

* HDInsight Hadoop Monitoring
* HDInsight HBase Monitoring
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

Pour obtenir des instructions sur l’installation d’une solution de gestion, consultez la rubrique sur les [solutions de gestion dans Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Pour faire des essais, installez une solution HDInsight Hadoop Monotiring. Vous voyez alors une vignette **HDInsightHadoop** sous **Résumé**. Sélectionnez la vignette **HDInsightHadoop**. La solution HDInsightHadoop ressemble à ceci :

![Vue de la solution de surveillance HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Étant donné que le cluster est tout nouveau, le rapport ne contient aucune activité.

## <a name="next-steps"></a>Étapes suivantes

* [Requête Azure Log Analytics pour surveiller les clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
