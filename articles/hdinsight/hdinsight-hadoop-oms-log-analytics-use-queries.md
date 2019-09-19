---
title: Interroger les journaux Azure Monitor pour surveiller les clusters Azure HDInsight
description: Apprenez à interroger les journaux Azure Monitor pour surveiller les travaux exécutés dans un cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 031879ac1d0d2dd1148c0c37ee72c60d093f8a7d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809383"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Interroger les journaux Azure Monitor pour surveiller les clusters HDInsight

Découvrez des scénarios de base sur l'utilisation des journaux Azure Monitor pour surveiller les clusters Azure HDInsight :

* [Analyser les métriques du cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Rechercher des messages de journal spécifiques](#search-for-specific-log-messages)
* [Créer des alertes d’événement](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prérequis

* Vous devez avoir configuré un cluster HDInsight pour utiliser les journaux Azure Monitor, et ajouté les solutions de supervision des journaux Azure Monitor propres au cluster HDInsight à l'espace de travail. Pour obtenir des instructions, consultez [Utiliser les journaux Azure Monitor avec des clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analyser les métriques du cluster HDInsight

Découvrez comment rechercher des métriques spécifiques pour votre cluster HDInsight.

1. Ouvrez l’espace de travail Log Analytics qui est associé à votre cluster HDInsight à partir du portail Azure.
2. Sélectionnez la vignette **Recherche dans les journaux**.
3. Saisissez la requête suivante dans la zone de recherche afin de rechercher toutes les mesures disponibles dans tous les clusters HDInsight configurés pour utiliser les journaux Azure Monitor, puis sélectionnez **EXÉCUTER**.

        search *

    ![Rechercher toutes les mesures](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Rechercher toutes les mesures")

    Le résultat doit être semblable à ceci :

    ![Rechercher toutes les sorties de mesures](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Rechercher toutes les sorties de mesures")

5. Dans le volet de gauche, sous **Type**, sélectionnez une mesure à approfondir, puis cliquez sur **Appliquer**. La capture d’écran qui suit présente le type `metrics_resourcemanager_queue_root_default_CL` sélectionné.

    > [!NOTE]  
    > Vous devrez peut-être cliquer sur le bouton **[+]Plus** pour trouver la mesure que vous recherchez. En outre, comme le bouton **Appliquer** figure au bas de la liste, vous devez faire défiler la page vers le bas pour l’afficher.

    Notez que la requête dans la zone de texte apparaît dans la zone en surbrillance dans la capture d’écran suivante :

    ![Rechercher des mesures spécifiques](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Rechercher des mesures spécifiques")

6. Pour approfondir cette mesure spécifique. Par exemple, vous pouvez affiner les résultats en fonction de la moyenne des ressources utilisées dans un intervalle de 10 minutes, en les classant par nom de cluster à l’aide de la requête suivante :

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Au lieu d’affiner les résultats en fonction de la moyenne des ressources utilisées, vous pouvez utiliser la requête suivante pour affiner les résultats en fonction du moment où les ressources maximales ont été utilisées (ainsi qu’aux 90e et 95e centiles) dans une fenêtre de 10 minutes :

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Rechercher des messages de journal spécifiques

Découvrez comment rechercher des messages d’erreur pendant une fenêtre de temps spécifique. Ces étapes ne sont qu’un exemple d’une méthode permettant d’afficher le message d’erreur qui vous intéresse. Vous pouvez utiliser n’importe quelle propriété disponible pour rechercher les erreurs que vous voulez trouver.

1. Ouvrez l’espace de travail Log Analytics qui est associé à votre cluster HDInsight à partir du portail Azure.
2. Sélectionnez la vignette **Recherche dans les journaux**.
3. Saisissez la requête suivante afin de rechercher tous les messages d'erreur dans tous les clusters HDInsight configurés pour utiliser les journaux Azure Monitor, puis sélectionnez **EXÉCUTER**. 

         search "Error"

    Vous devez voir un résultat semblable à ce qui suit :

    ![Rechercher tous les résultats d’erreur](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Rechercher tous les résultats d’erreur")

4. Dans le volet de gauche, sous la catégorie **Type**, sélectionnez un type d’erreur à approfondir, puis cliquez sur **Appliquer**.  Notez que les résultats sont affinés pour n’afficher que l’erreur du type que vous avez sélectionné.
5. Vous pouvez approfondir cette liste d’erreurs spécifiques en utilisant les options disponibles dans le volet gauche. Par exemple :

    - Pour afficher les messages d’erreur d’un nœud de travail spécifique :

        ![Rechercher un résultat d’erreur spécifique - 1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Rechercher un résultat d’erreur spécifique - 1")

    - Pour afficher une erreur qui s’est produite à une heure donnée :

        ![Rechercher un résultat d’erreur spécifique - 2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Rechercher un résultat d’erreur spécifique - 2")

6. Pour afficher l’erreur spécifique. Vous pouvez cliquer sur **[+]Afficher plus** pour examiner le message d’erreur.

    ![Rechercher un résultat d’erreur spécifique - 3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Rechercher un résultat d’erreur spécifique - 3")

## <a name="create-alerts-for-tracking-events"></a>Créer des alertes pour des événements de suivi

La première étape pour créer une alerte consiste à définir une requête qui déclenche l’alerte. Vous pouvez utiliser la requête de votre choix pour créer une alerte.

1. Ouvrez l’espace de travail Log Analytics qui est associé à votre cluster HDInsight à partir du portail Azure.
2. Sélectionnez la vignette **Recherche dans les journaux**.
3. Exécutez la requête suivante là où vous souhaitez créer une alerte, puis sélectionnez **EXÉCUTER**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    La requête fournit la liste des applications en échec exécutées sur des clusters HDInsight.

4. Sélectionnez **Nouvelle règle d'alerte** en haut de la page.

    ![Saisir une requête pour créer une alerte - 1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Saisir une requête pour créer une alerte - 1")

5. Dans la fenêtre **Créer une règle**, saisissez la requête et d’autres détails pour créer une alerte, puis sélectionnez **Créer une règle d'alerte**.

    ![Saisir une requête pour créer une alerte - 2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Saisir une requête pour créer une alerte - 2")

Pour modifier ou supprimer une alerte existante :

1. Ouvrez l’espace de travail Log Analytics à partir du portail Azure.
2. Dans le menu de gauche, sélectionnez **Alerte**.
3. Sélectionnez l’alerte à modifier ou à supprimer.
4. Vous disposez des options suivantes : **Enregistrer**, **Ignorer**, **Désactiver** et **Supprimer**.

    ![HDInsight, journaux Azure Monitor, alerte, supprimer, modifier](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Pour plus d'informations, consultez [Créer, afficher et gérer des alertes de métrique à l'aide d'Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Voir aussi

* [Créer des vues personnalisées à l'aide du Concepteur de vues d'Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Créer, afficher et gérer des alertes de métrique à l'aide d'Azure Monitor](../azure-monitor/platform/alerts-metric.md)
