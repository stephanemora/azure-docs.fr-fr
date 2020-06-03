---
title: Optimiser les performances des travaux Spark - Azure HDInsight
description: Présente des stratégies courantes permettant d’optimiser les performances des clusters Apache Spark dans Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780107"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimiser les travaux Apache Spark dans Azure HDInsight

Cet article fournit une vue d’ensemble des stratégies permettant d’optimiser les tâches Apache Spark sur Azure HDInsight.

## <a name="overview"></a>Vue d’ensemble

Les performances de vos tâches Apache Spark dépendent de plusieurs facteurs. Ces facteurs de performances incluent la façon dont vos données sont stockées, la façon dont le cluster est configuré et les opérations qui sont utilisées lors du traitement des données.

Les problèmes courants que vous pouvez rencontrer incluent les contraintes de mémoire résultant d’un dimensionnement incorrect des exécuteurs, d’opérations de longue durée et de tâches qui entraînent des opérations cartésiennes.

Il existe également différentes stratégies qui peuvent vous aider à surmonter ces défis, telles que la mise en cache, et l’autorisation de l’asymétrie des données.

Dans chacun des articles suivants, vous trouverez des problèmes et solutions courants pour un aspect de l’optimisation de Spark.

* [Optimiser le stockage des données](optimize-data-storage.md)
* [Optimiser le traitement des données](optimize-data-processing.md)
* [Optimiser l’utilisation de la mémoire](optimize-memory-usage.md)
* [Optimiser la configuration de cluster](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Étapes suivantes

* [Déboguer des travaux Apache Spark en cours d’exécution sur Azure HDInsight](apache-spark-job-debugging.md)
* [Gérer les ressources d’un cluster Apache Spark sur HDInsight](apache-spark-resource-manager.md)
* [Configurer les paramètres d’Apache Spark](apache-spark-settings.md)
