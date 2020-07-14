---
title: Surveiller les applications Apache Spark
description: Utilisez Azure Synapse Studio pour surveiller vos applications Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 20c4216e7af0cb4100204ebae1b484a53e3c50a6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971217"
---
# <a name="use-the-azure-synapse-studio-to-monitor-your-apache-spark-applications"></a>Utiliser Azure Synapse Studio pour surveiller vos applications Apache Spark

Azure Synapse Analytics vous permet d’utiliser Apache Spark pour exécuter des blocs-notes, des travaux et d’autres types d’applications sur vos pools Apache Spark dans votre espace de travail.

Cet article explique comment surveiller vos applications Apache Spark, et ainsi garder un œil sur leur état, leurs problèmes et leur progression.

Ce tutoriel décrit les tâches suivantes :

* Superviser l’exécution des applications Apache Spark
* Afficher les applications Apache Spark terminées
* Afficher les applications Apache Spark annulées
* Déboguer les applications Apache Spark ayant échoué

## <a name="prerequisites"></a>Prérequis

Avant de commencer le didacticiel, veillez à disposer des éléments suivants :

- Un espace de travail Azure Synapse Studio. Pour obtenir des instructions, consultez [Créer un espace de travail Azure Synapse Studio](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Un pool Apache Spark.

## <a name="monitor-running-apache-spark-application"></a>Superviser l’exécution des applications Apache Spark

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark en cours d’exécution, sélectionnez l’application Apache Spark concernée. Si l’application Apache Spark est toujours en cours d’exécution, vous pouvez surveiller la progression.

  ![sélectionner une tâche en cours d’exécution](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. Annulez l’exécution de l’application Apache Spark.

3. Actualisez la requête de journal.

4. Affichez le graphique.

5. Vérifiez les informations dans **Résumé**.

6. Consultez les **Journaux**. Les informations du journal sont vides au cours de l’exécution.

    ![afficher la tâche en cours d’exécution](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Afficher les applications Apache Spark terminées

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark terminées, sélectionnez l’application Apache Spark concernée.

  ![sélectionner la tâche terminée](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. Actualisez la requête de journal.

3. Ouvrez le lien vers le serveur d’historique Apache Spark en cliquant sur **Serveur d’historique Spark**.

4. Consultez les informations dans **Résumé** en cliquant sur l’icône du graphique.

5. Consultez les **Journaux**. Vous pouvez sélectionner différents types de journaux dans la liste déroulante. vous pouvez télécharger les informations du journal en cliquant sur **Télécharger les journaux**.

6. Vous pouvez voir une vue d’ensemble de votre travail dans le graphique du travail ainsi généré. Par défaut, le graphique affiche tous les travaux. Vous pouvez filtrer cet affichage par **ID de travail**.

7. Par défaut, l’affichage **Progress** (Progression) est sélectionné. Vous pouvez vérifier le flux de données en sélectionnant **Read** (Lues) ou **Written** (Écrites) dans le menu déroulant **Display** (Affichage).

8. Pour lire la tâche, sélectionnez **Playback** (Lecture). Vous pouvez sélectionner **Stop** (Arrêter) à tout moment.

9. Utilisez la molette de la souris pour effectuer un zoom avant/arrière du graphe de travail, ou sélectionnez **Zoom to fit** (Zoom d’ajustement) pour l’adapter à la taille de l’écran.

10. Le nœud de graphique du travail affiche les informations suivantes de chaque phase :

    * ID.

    * Nom ou description.

    * Nombre total de tâches.

    * lecture de données : somme de la taille d’entrée et de la taille de lecture aléatoire ;

    * Écriture de données : somme de la taille de sortie et de la taille des écritures aléatoires.

    * Durée d’exécution : durée entre l’heure de début de la première tentative et l’heure de fin de la dernière tentative.

    * Nombre de lignes : somme des enregistrements d’entrée, des enregistrements de sortie, des enregistrements de lecture aléatoire et des enregistrements d’écriture aléatoire.

    * Avancement.

     ![afficher la tâche terminée](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Cliquez sur le graphique pour que les détails de l’index s’affichent.

   ![détails de l’index](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Afficher les applications Apache Spark annulées

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark annulées, sélectionnez l’application Apache Spark concernée.

 ![sélectionner une tâche annulée](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. Actualisez la requête de journal.

3. Ouvrez le lien vers le serveur d’historique Apache en cliquant sur **Serveur d’historique Spark**.

4. Affichez le graphique.

5. Vérifiez les informations dans **Résumé**.

6. Consultez les **Journaux**. Vous pouvez sélectionner différents types de journaux dans la liste déroulante. vous pouvez télécharger les informations du journal en cliquant sur **Télécharger les journaux**.

   ![afficher une tâche annulée](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Déboguer les applications Apache Spark ayant échoué

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark ayant échoué, sélectionnez l’application Apache Spark concernée.

![sélectionner une tâche ayant échoué](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. Actualisez la requête de journal.

3. Ouvrez le lien vers le serveur d’historique Apache Spark en cliquant sur **Serveur d’historique Spark**.

4. Affichez le graphique.

5. Vérifiez les informations dans **Résumé**.

6. Consultez les informations sur l’erreur.

   ![informations sur la tâche ayant échoué](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la surveillance des exécutions de pipeline, consultez l’article [Surveiller les exécutions de pipeline avec Azure Synapse Studio](how-to-monitor-pipeline-runs.md).  
