---
title: Comment surveiller les applications Apache Spark dans Synapse Studio
description: Utiliser Synapse Studio pour surveiller vos applications Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: b5ad9e6a448747ac8f33829b1137b1af434e7a9c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385583"
---
# <a name="use-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Utiliser Synapse Studio (préversion) pour surveiller vos applications Apache Spark

Azure Synapse Analytics vous permet d’utiliser Spark pour exécuter des bloc-notes, des travaux et d’autres types d’applications sur vos pools Spark dans votre espace de travail.

Cet article explique comment superviser vos applications Apache Spark, et ainsi garder un œil sur leur état, leurs problèmes et leur progression.

## <a name="access-apache-spark-applications-list"></a>Accéder à la liste des applications Apache Spark

Pour voir la liste des applications Apache Spark dans votre espace de travail, commencez par [ouvrir Synapse Studio](https://web.azuresynapse.net/) et sélectionnez votre espace de travail.

![Se connecter à l’espace de travail](./media/common/login-workspace.png)

Une fois que vous avez ouvert votre espace de travail, sélectionnez la section **Monitor** sur la gauche.

![Sélectionner un hub Monitor](./media/common/left-nav.png)

Sélectionnez **Applications Apache Spark** pour voir la liste de ces applications.

 ![Sélectionner des applications Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrer vos applications Apache Spark

Vous pouvez filtrer la liste des applications Apache Spark en la limitant à celles qui vous intéressent. Les filtres en haut de l’écran vous permettent de spécifier un champ sur lequel filtrer.

Par exemple, vous pouvez filtrer la vue pour voir uniquement les applications Apache Spark contenant le nom « sales » :

![Bouton Filtrer](./media/common/filter-button.png)

![Exemple de filtre](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Affichere des détails d’une application Apache Spark spécifique

Pour voir les détails sur l’une de vos applications Apache Spark, sélectionnez l’application Apache Spark concernée. Si l’application Apache Spark est toujours en cours d’exécution, vous pouvez superviser la progression. [En savoir plus](apache-spark-applications.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la surveillance des exécutions de pipeline, consultez l’article [Surveiller les exécutions de pipeline avec Synapse Studio](how-to-monitor-pipeline-runs.md). 

Pour plus d’informations sur le débogage d’une application Apache Spark, consultez l’article [Superviser les applications Apache Spark sur Synapse Studio](apache-spark-applications.md).