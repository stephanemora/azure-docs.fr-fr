---
title: Optimiser les performances des travaux Spark - Azure HDInsight
description: Présente des stratégies courantes permettant d’optimiser les performances des clusters Apache Spark dans Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117961"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimiser les travaux Apache Spark dans Azure HDInsight

Cet article fournit une vue d’ensemble des stratégies permettant d’optimiser les tâches Apache Spark sur Azure HDInsight.

## <a name="overview"></a>Vue d’ensemble

Les performances de vos tâches Apache Spark dépendent de plusieurs facteurs. Ces facteurs de performances incluent la façon dont vos données sont stockées, la façon dont le cluster est configuré et les opérations qui sont utilisées lors du traitement des données.

Les problèmes courants que vous pouvez rencontrer incluent les contraintes de mémoire résultant d’un dimensionnement incorrect des exécuteurs, d’opérations de longue durée et de tâches qui entraînent des opérations cartésiennes.

Il existe également plusieurs optimisations qui peuvent vous aider à surmonter ces défis, telles que la mise en cache et l’autorisation de l’asymétrie des données.

Dans chacun des articles suivants, vous trouverez des informations sur les différents aspects de l’optimisation de Spark.

* [Optimiser le stockage des données pour Apache Spark](optimize-data-storage.md)
* [Optimiser le traitement de données pour Apache Spark](optimize-data-processing.md)
* [Optimiser l’utilisation de la mémoire pour Apache Spark](optimize-memory-usage.md)
* [Optimiser la configuration de cluster HDInsight pour Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Étapes suivantes

* [Déboguer des travaux Apache Spark en cours d’exécution sur Azure HDInsight](apache-spark-job-debugging.md)
* [Gérer les ressources d’un cluster Apache Spark sur HDInsight](apache-spark-resource-manager.md)
* [Configurer les paramètres d’Apache Spark](apache-spark-settings.md)
