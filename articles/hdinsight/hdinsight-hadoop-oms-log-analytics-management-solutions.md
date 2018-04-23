---
title: Ajouter des solutions de gestion de clusters HDInsight à Azure Log Analytics | Microsoft Docs
description: Découvrez comment utiliser Azure Log Analytics pour créer des vues personnalisées pour des clusters HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: caa10682f8419cfbc0837e5069357e02ee1e5f64
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Ajouter des solutions de gestion de clusters HDInsight à Log Analytics

HDInsight fournit des solutions de gestion de clusters que vous pouvez ajouter pour Azure Log Analytics. Les [solutions de gestion](../log-analytics/log-analytics-add-solutions.md) ajoutent des fonctionnalités à Log Analytics en fournissant des données et des outils d’analyse supplémentaires. Ces solutions collectent des indicateurs de performance importants de vos clusters HDInsight et fournissent les outils pour rechercher les indicateurs. Elles fournissent également des visualisations et des tableaux de bord pour la plupart des types de cluster pris en charge dans HDInsight. En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes personnalisées de surveillance. 

Dans cet article, vous allez découvrir comment ajouter des solutions de gestion de clusters à un espace de travail Log Analytics.

## <a name="prerequisites"></a>Prérequis


* Vous devez avoir configuré un cluster HDInsight pour utiliser Azure Log Analytics. Pour obtenir des instructions, consultez [Utiliser Azure Log Analytics avec des clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Ajouter des solutions de gestion de clusters

Dans cette section, vous allez apprendre à ajouter une solution de gestion de clusters HBase à un espace de travail Log Analytics existant.

1. Ouvrez un cluster HDInsight dans le portail Azure, cliquez sur **Surveillance**, puis sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

1. Dans le tableau de bord, cliquez sur **Galerie de solutions** ou sur l’icône du **Concepteur de vues** dans le volet gauche.

    ![Ajouter la solution de gestion dans Log Analytics](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Ajouter la solution de gestion dans Log Analytics")

2. Dans la galerie de solutions, cliquez sur l’une des vignettes suivantes :

    - HDInsight Hadoop Monitoring
    - HDInsight HBase Monitoring (version préliminaire)
    - HDInsight Kafka Monitoring
    - HDInsight Storm Monitoring
    - HDInsight Spark Monitoring

3. Dans l’écran suivant, cliquez sur **Ajouter**.  La capture d’écran suivante montre le bouton Ajouter pour HBase Monitoring.

     ![Ajouter la solution de gestion HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Ajouter la solution de gestion HBase")

4. Vous pouvez voir une vignette sur le tableau de bord pour la solution de gestion HBase. Si le cluster que vous avez associé à Operations Management Suite (dans le cadre de la configuration requise pour cet article) est un cluster HBase, la vignette affiche le nom du cluster et le nombre de nœuds du cluster.

    ![Solution de gestion HBase ajoutée](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Solution de gestion HBase ajoutée")

## <a name="next-steps"></a>Étapes suivantes

* [Requête Azure Log Analytics pour surveiller les clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Voir aussi

* [Utilisation de Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Création de règles d’alerte dans Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
