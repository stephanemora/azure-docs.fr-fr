---
title: Comment superviser les applications Apache Spark
description: Utilisez Azure Synapse Studio pour surveiller vos applications Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970895"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Utiliser Azure Synapse Studio (préversion) pour surveiller vos applications Apache Spark

Azure Synapse Analytics vous permet d’utiliser Spark pour exécuter des bloc-notes, des travaux et d’autres types d’applications sur vos pools Spark dans votre espace de travail.

Cet article explique comment superviser vos applications Apache Spark, et ainsi garder un œil sur leur état, leurs problèmes et leur progression.

## <a name="accessing-the-list-of-apache-spark-applications"></a>Accès à la liste des applications Apache Spark

Pour voir la liste des applications Apache Spark dans votre espace de travail, commencez par [ouvrir Azure Synapse Studio](https://web.azuresynapse.net/) et sélectionner votre espace de travail.

![Se connecter à l’espace de travail](./media/common/login-workspace.png)

Une fois que vous avez ouvert votre espace de travail, sélectionnez la section **Monitor** sur la gauche.

![Sélectionner un hub Monitor](./media/common/left-nav.png)

Sélectionnez **Applications Apache Spark** pour voir la liste de ces applications.

 ![Sélectionner des applications Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>Filtrage de vos applications Apache Spark

Vous pouvez filtrer la liste des applications Apache Spark en la limitant à celles qui vous intéressent. Les filtres en haut de l’écran vous permettent de spécifier un champ sur lequel filtrer.

Par exemple, vous pouvez filtrer la vue pour voir uniquement les applications Apache Spark contenant le nom « sales » :

![Bouton Filtrer](./media/common/filter-button.png)

![Exemple de filtre](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>Affichage des détails d’une application Apache Spark spécifique

Pour voir les détails sur l’une de vos applications Apache Spark, sélectionnez l’application Apache Spark concernée. Si l’application Apache Spark est toujours en cours d’exécution, vous pouvez superviser la progression. [En savoir plus](apache-spark-applications.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la surveillance des exécutions de pipeline, consultez l’article [Surveiller les exécutions de pipeline avec Azure Synapse Studio](how-to-monitor-pipeline-runs.md). 

Pour plus d’informations sur le débogage d’une application Apache Spark, consultez l’article [Superviser les applications Apache Spark sur Azure Synapse Studio](apache-spark-applications.md).