---
title: Comment surveiller les pools Apache Spark dans Synapse Studio
description: Découvrez comment surveiller vos pools Apache Spark à l’aide de Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: c6148f2bd5d3b1555ae61d2da3e922c9cfe632cb
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539656"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Utiliser Synapse Studio pour surveiller vos pools Apache Spark

Azure Synapse Analytics vous permet d’utiliser Apache Spark pour exécuter des blocs-notes, des travaux et d’autres types d’applications sur des pools Apache Spark dans votre espace de travail.

Cet article explique comment surveiller vos pools Apache Spark, ce qui vous permet de garder un œil sur leur état, notamment le nombre de vCore utilisés par les différents utilisateurs de l’espace de travail.

## <a name="access-apache-spark-pools-list"></a>Accéder à la liste des pools Apache Spark

Pour voir la liste des pools Apache Spark dans votre espace de travail, commencez par [ouvrir Synapse Studio](https://web.azuresynapse.net/), puis sélectionnez votre espace de travail.

![Se connecter à l’espace de travail](./media/common/login-workspace.png)

Une fois que vous avez ouvert votre espace de travail, sélectionnez la section **Monitor** sur la gauche.

![Sélectionner un hub Monitor](./media/common/left-nav.png)

Sélectionnez **Pools Apache Spark** pour afficher la liste des pools de Apache Spark.

 ![Sélectionner Pools Apache Spark](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-apache-spark-pools"></a>Filtrer vos pools Apache Spark

Vous pouvez filtrer la liste des pools Apache Spark pour retenir ceux qui vous intéressent. Les filtres en haut de l’écran vous permettent de spécifier un champ sur lequel filtrer.

Par exemple, vous pouvez filtrer l’affichage pour voir uniquement les pools Apache Spark contenant le nom « dataprep » :

![Exemple de filtre](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-pool"></a>Affichere des détails d’un pool Apache Spark spécifique

Pour afficher les détails de l’un de vos pools Apache Spark, sélectionnez-le.

![Détails du pool Apache Spark](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la surveillance des exécutions de pipeline, consultez l’article [Surveiller les exécutions de pipeline dans Synapse Studio](how-to-monitor-pipeline-runs.md). 

Pour plus d’informations sur la surveillance d’applications Apache Spark, consultez l’article [Superviser les applications Apache Spark dans Synapse Studio](how-to-monitor-spark-applications.md).
