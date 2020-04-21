---
title: Surveiller les applications Apache Spark
description: Utilisez Azure Synapse Studio pour surveiller vos applications Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427297"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Utiliser Azure Synapse Studio (préversion) pour surveiller vos applications Apache Spark

Azure Synapse Analytics vous permet d’utiliser Spark pour exécuter des bloc-notes, des travaux et d’autres types d’applications sur vos pools Spark dans votre espace de travail.

Cet article explique comment surveiller vos applications Spark, et ainsi garder un œil sur leur état, leurs problèmes et leur progression.

## <a name="accessing-the-list-of-spark-applications"></a>Accès à la liste des applications Spark

Pour afficher la liste des applications Spark dans votre espace de travail, commencez par [ouvrir Azure Synapse Studio](https://web.azuresynapse.net/) et sélectionner votre espace de travail.

  > [!div class="mx-imgBorder"]
  > ![Se connecter à l’espace de travail](./media/common/login-workspace.png)

Une fois que vous avez ouvert votre espace de travail, sélectionnez la section **Monitor** sur la gauche.

  > [!div class="mx-imgBorder"]
  > ![Sélectionner un hub Monitor](./media/common/left-nav.png)

Sélectionnez **Applications Spark** pour afficher la liste de ces applications.

  > [!div class="mx-imgBorder"]
  > ![Sélectionner des applications Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrage de vos applications Spark

Vous pouvez filtrer la liste des applications Spark en la limitant à celles qui vous intéressent. Les filtres en haut de l’écran vous permettent de spécifier un champ sur lequel filtrer.

Par exemple, vous pouvez filtrer la vue pour afficher uniquement les applications Spark contenant le nom « sales » :

  > [!div class="mx-imgBorder"]
  > ![Bouton Filtrer](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Exemple de filtre](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Affichage des détails d’une application Spark spécifique

Pour afficher les détails d’une de vos applications Spark, sélectionnez l’application. Si l’application Spark est toujours en cours d’exécution, vous pouvez surveiller la progression.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la surveillance des exécutions de pipeline, consultez l’article [Surveiller les exécutions de pipeline avec Azure Synapse Studio](how-to-monitor-pipeline-runs.md).  
