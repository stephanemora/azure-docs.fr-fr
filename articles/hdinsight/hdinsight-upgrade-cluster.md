---
title: Mettre à niveau le cluster HDInsight avec une version plus récente - Azure
description: Découvrez les instructions permettant de mettre à niveau vos clusters Azure HDInsight.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951154"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Mettre à niveau le cluster HDInsight

Pour tirer parti des dernières fonctionnalités proposées par HDInsight, nous vous recommandons de mettre à niveau les clusters HDInsight vers la version la plus récente. Suivez les instructions ci-dessous pour mettre à niveau vos clusters HDInsight.

> [!NOTE]  
> Pour obtenir des informations sur les versions HDInsight prises en charge, consultez [Quels sont les différents composants Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Tâches de mise à niveau

Le workflow pour mettre à niveau un cluster HDInsight est le suivant :
![Schéma du workflow de mise à niveau HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Lisez chaque section de ce document pour comprendre les modifications qui peuvent être nécessaires lors de la mise à jour de votre cluster HDInsight.
2. Créez un cluster comme environnement de test ou d’assurance qualité. Pour plus d’informations sur la création d’un cluster, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiez les travaux, sources de données et récepteurs existants dans le nouvel environnement.
4. Effectuez des tests de validation pour vérifier que vos tâches fonctionnent comme prévu sur le nouveau cluster.

Une fois que vous avez vérifié que tout fonctionne comme prévu, planifiez un temps d’arrêt pour la migration. Pendant ce temps d’arrêt, effectuez les actions suivantes :

1. Sauvegardez toutes les données temporaires stockées localement sur les nœuds du cluster, par exemple si vous avez des données stockées directement sur un nœud principal.
1. [Supprimez le cluster existant](./hdinsight-delete-cluster.md).
1. Créez un cluster dans le même sous-réseau de réseau virtuel avec la version HDI la plus récente (ou prise en charge) à l’aide du même magasin de données par défaut que le cluster précédent a utilisé. Cela permet au nouveau cluster de continuer à travailler sur vos données de production existantes.
1. Importez toutes les données temporaires que vous avez sauvegardées.
1. Démarrez des tâches ou poursuivez le traitement avec le nouveau cluster.

## <a name="next-steps"></a>Étapes suivantes

* [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Se connecter à HDInsight à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gérer un cluster Linux avec Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notes de publication HDInsight](./hdinsight-version-release.md)
