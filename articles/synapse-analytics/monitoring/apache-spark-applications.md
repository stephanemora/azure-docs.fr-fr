---
title: Surveiller des applications Apache Spark à l’aide de Synapse Studio
description: Utilisez Synapse Studio pour surveiller vos applications Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: d86e8a6e16fdaa48678ae1af89eb236f6e00b01b
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005138"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Utiliser Synapse Studio pour surveiller vos applications Apache Spark

Azure Synapse Analytics vous permet d’utiliser Apache Spark pour exécuter des blocs-notes, des travaux et d’autres types d’applications sur vos pools Apache Spark dans votre espace de travail.

Cet article explique comment surveiller vos applications Apache Spark, et ainsi garder un œil sur leur état, leurs problèmes et leur progression.

Ce tutoriel décrit les tâches suivantes :

* Superviser l’exécution des applications Apache Spark
* Afficher les applications Apache Spark terminées
* Afficher les applications Apache Spark annulées
* Déboguer les applications Apache Spark ayant échoué
* Afficher les données d’entrée et de sortie pour les applications Apache Spark
* Comparer des applications Apache Spark

## <a name="prerequisites"></a>Prérequis

Avant de commencer le didacticiel, veillez à disposer des éléments suivants :

- Un espace de travail Synapse Studio. Pour obtenir des instructions, consultez [Créer un espace de travail Synapse Studio](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Un pool Apache Spark.

## <a name="view-apache-spark-applications"></a>Afficher les applications Apache Spark 
Vous pouvez afficher toutes les applications Apache Spark à partir de **Superviser** -> **Applications Apache Spark**.
   ![applications Apache Spark](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Afficher une application Apache Spark terminée

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark terminées, sélectionnez l’application Apache Spark concernée.

  ![sélectionner la tâche terminée](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. **Actualisez** le travail.

3. Cliquez sur **Comparer les applications** pour utiliser la fonctionnalité de comparaison. Pour plus d’informations sur cette fonctionnalité, consultez [**Comparer des applications Apache Spark**](#compare-apache-spark-applications).

4. Cliquez sur **Serveur d’historique Spark** pour ouvrir la page Serveur d’historique.

5. Vérifiez les informations dans **Résumé**.

6. Vérifiez les diagnostics dans l’onglet **Diagnostic**.

7. Consultez les **Journaux**. Vous pouvez afficher l’enregistrement complet des journaux **Livy**, **Prelaunch** et **Driver** en sélectionnant différentes options dans la liste déroulante. Vous pouvez également récupérer directement les informations de journal requises en recherchant des mots clés. Cliquez sur **Télécharger le journal** pour télécharger les informations du journal sur l’emplacement local, puis cochez la case **Filtrer les erreurs et les avertissements** pour filtrer les erreurs et les avertissements dont vous avez besoin.

8. Vous pouvez voir une vue d’ensemble de votre travail dans le graphique du travail ainsi généré. Par défaut, le graphique affiche tous les travaux. Vous pouvez filtrer cet affichage par **ID de travail**.

9. Par défaut, l’affichage **Progress** (Progression) est sélectionné. Vous pouvez vérifier le flux de données en sélectionnant **Progression**/**Lecture**/**Écriture**/**Durée** dans la liste déroulante **Affichage**.

10. Pour lire le travail, cliquez sur le bouton **Lire**. Vous pouvez cliquer sur le bouton **Arrêter** à tout moment pour arrêter.

11. Utilisez la barre de défilement pour effectuer un zoom avant et un zoom arrière sur le graphique du travail. Vous pouvez également sélectionner **Zoom ajusté** pour l’ajuster à l’écran.

    [![afficher la tâche terminée](./media/how-to-monitor-spark-applications/view-completed-job.png)](./media/how-to-monitor-spark-applications/view-completed-job.png#lightbox)


12. Le nœud de graphique du travail affiche les informations suivantes de chaque phase :

    - ID de travail
    - Nombre de tâches
    - Durée
    - Nombre de lignes
    - Lecture de données : somme de la taille d’entrée et de la taille de lecture aléatoire
    - Écriture de données : somme de la taille de sortie et de la taille des écritures aléatoires
    - Nombre d’étapes

       ![nœud du graphique du travail](./media/how-to-monitor-spark-applications/job-graph-node.png)

13. Pointez le curseur de la souris sur un travail, et les détails du travail s’affichent dans l’info-bulle :
    
    - Icône de l’état du travail : Si l’état du travail est réussi, il s’affiche sous la forme d’un « √ » vert ; si le travail détecte un problème, il affiche un « ! » jaune.
    - ID de travail.
    - Partie générale :
      - Avancement
      - Durée
      - Nombre total de tâches
    - Partie sur les données :
      - Nombre total de lignes
      - Taille de lecture
      - Taille d’écriture
    - Partie sur l’asymétrie :
      - Asymétrie des données
      - Asymétrie temporelle
    - Nombre d’étapes

      ![Pointer sur un travail](./media/how-to-monitor-spark-applications/hover-a-job.png)

14. Cliquez sur **Nombre d’étapes** pour développer toutes les étapes contenues dans le travail. Cliquez sur **Réduire** à côté de l’ID de travail pour réduire toutes les étapes du travail.

15. Cliquez sur **Afficher les détails** dans le graphique d’une étape pour que les détails de l’étape s’affichent.

    [![développer toutes les étapes](./media/how-to-monitor-spark-applications/expand-all-the-stages.png)](./media/how-to-monitor-spark-applications/expand-all-the-stages.png#lightbox)
    
## <a name="monitor-running-apache-spark-application"></a>Superviser l’exécution d’applications Apache Spark

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark en cours d’exécution, sélectionnez l’application Apache Spark concernée et affichez les détails. Si l’application Apache Spark est toujours en cours d’exécution, vous pouvez surveiller la progression.

   ![sélectionner une tâche en cours d’exécution](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. **Annulez** l’exécution de l’application Apache Spark.

3. **Actualisez** le travail.

4. Cliquez sur le bouton **Interface utilisateur Spark** pour accéder à la page Travail Spark.

5. Pour **Graphique du travail**, **Résumé**, **Diagnostics** et **Journaux**, Vous pouvez voir une vue d’ensemble de votre travail dans le graphique du travail ainsi généré. Reportez-vous aux étapes 5 à 15 de la section [Afficher une application Apache Spark terminée](#view-completed-apache-spark-application). 

    [![afficher la tâche en cours d’exécution](./media/how-to-monitor-spark-applications/view-running-job.png)](./media/how-to-monitor-spark-applications/view-running-job.png#lightbox)

## <a name="view-canceled-apache-spark-application"></a>Afficher une application Apache Spark annulée

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark annulées, sélectionnez l’application Apache Spark concernée.

 ![sélectionner une tâche annulée](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. **Actualisez** le travail.

3. Cliquez sur **Comparer les applications** pour utiliser la fonctionnalité de comparaison. Pour plus d’informations sur cette fonctionnalité, consultez [**Comparer des applications Apache Spark**](#compare-apache-spark-applications).

4. Ouvrez le lien vers le serveur d’historique Apache en cliquant sur **Serveur d’historique Spark**.

5. Affichez le graphique. Vous pouvez voir une vue d’ensemble de votre travail dans le graphique du travail ainsi généré. Reportez-vous aux étapes 5 à 15 de la section [Afficher une application Apache Spark terminée](#view-completed-apache-spark-application).

  [![afficher une tâche annulée](./media/how-to-monitor-spark-applications/view-cancelled-job.png)](./media/how-to-monitor-spark-applications/view-cancelled-job.png#lightbox)

## <a name="debug-failed-apache-spark-application"></a>Déboguer une application Apache Spark défaillante

Ouvrez **Superviser**, puis sélectionnez **Applications Apache Spark**. Pour afficher les détails des applications Apache Spark ayant échoué, sélectionnez l’application Apache Spark concernée.

![sélectionner une tâche ayant échoué](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Consultez **Tâches terminées**, **Statut** et **Durée totale**.

2. **Actualisez** le travail.

3. Cliquez sur **Comparer les applications** pour utiliser la fonctionnalité de comparaison. Pour plus d’informations sur cette fonctionnalité, consultez [**Comparer des applications Apache Spark**](#compare-apache-spark-applications).

4. Ouvrez le lien vers le serveur d’historique Apache en cliquant sur **Serveur d’historique Spark**.

5. Affichez le graphique. Vous pouvez voir une vue d’ensemble de votre travail dans le graphique du travail ainsi généré. Reportez-vous aux étapes 5 à 15 de la section [Afficher une application Apache Spark terminée](#view-completed-apache-spark-application).

   [![informations sur la tâche ayant échoué](./media/how-to-monitor-spark-applications/failed-job-info.png)](./media/how-to-monitor-spark-applications/failed-job-info.png#lightbox)


## <a name="view-input-dataoutput-data-for-apache-spark-application"></a>Afficher les données d’entrée/de sortie pour les applications Apache Spark

Sélectionnez une application Apache Spark, puis cliquez sur l’onglet **Données d’entrée/de sortie** pour afficher les dates de l’entrée et de la sortie des applications Apache Spark. Cette fonction peut mieux vous aider à déboguer le travail Spark. De plus, la source de données prend en charge trois méthodes de stockage : gen1, gen2 et blob.
    
**Onglet Données d’entrée**
     
1. Cliquez sur le bouton **Copier l’entrée** pour coller le fichier d’entrée dans l’emplacement local.

2. Cliquez sur le bouton **Exporter au format CSV** pour exporter le fichier d’entrée au format CSV.

3. Vous pouvez rechercher des fichiers en entrant des mots clés dans **Zone de recherche** (les mots clés incluent le nom du fichier, le format de lecture et le chemin d’accès).

4. Vous pouvez trier les fichiers d’entrée en cliquant sur **Nom**, **Format de lecture** et **Chemin d’accès**.

5. Si vous pointez le curseur de la souris sur un fichier d’entrée, l’icône des boutons **Télécharger/Copier le chemin d’accès/Plus** s’affiche.

   ![onglet entrée](./media/how-to-monitor-spark-applications/input-tab.png)

6. Si vous cliquez sur le bouton **Plus**, le menu contextuel **Copier le chemin d’accès/Afficher dans Explorer/Propriétés** apparaît.
      
    ![entrée plus](./media/how-to-monitor-spark-applications/input-more.png)

   * Copier le chemin d’accès : peut copier le **chemin d’accès complet** et le **chemin d’accès relatif**.
   * Afficher dans Explorer : permet d’accéder au compte de stockage lié (Données->Lié).
   * Propriétés : affiche les propriétés de base du fichier (Nom de fichier, Chemin d’accès du fichier, Format de lecture, Taille, Modifié).

     ![image des propriétés](./media/how-to-monitor-spark-applications/properties.png)

**Onglet Données de sortie**

   Présente les mêmes fonctionnalités que l’entrée.

   ![image sortie](./media/how-to-monitor-spark-applications/output.png)

## <a name="compare-apache-spark-applications"></a>Comparer des applications Apache Spark

Il existe deux façons de comparer des applications. Vous pouvez comparer en choisissant de **comparer une application** ou en cliquant sur le bouton **Comparer dans le bloc-notes** pour afficher la comparaison dans le bloc-notes.

### <a name="compare-by-choose-an-application"></a>Comparer par choix d’une application

Cliquez sur le bouton **Comparer des applications** et choisissez une application dont comparer les performances. Vous pouveze voir intuitivement la différence entre les deux applications.

![comparer des applications](./media/how-to-monitor-spark-applications/compare-applications.png)

![détails de comparaison d’applications](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. Utilisez la souris pour survoler une application et l’icône **Comparer des applications** s’affiche.

2. Cliquez sur l’icône **Comparer des applications** et la page Comparer des applications s’affiche.

3. Cliquez sur le bouton **Choisir une application** pour ouvrir la page **Choisir une application de comparaison**.

4. Lorsque vous choisissez l’application pour la comparaison, vous devez entrer l’URL de l’application ou choisir dans la liste périodique. Cliquez ensuite sur le bouton **OK**. 

   ![choisir une application de comparaison](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Le résultat de la comparaison s’affiche sur la page comparer des applications.

   ![résultat de la comparaison](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Comparer avec Comparer dans le bloc-notes

Cliquez sur le bouton **Comparer dans le bloc-notes** sur la page **Comparer les applications** pour ouvrir le bloc-notes. Le nom par défaut du fichier  *.ipynb* est **Analyse de l’application actualisée**.

![Comparer dans le bloc-notes](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

Dans le bloc-notes : fichier d’analyse de l’application actualisée, vous pouvez exécuter l’application directement après avoir défini le pool Spark et la langue.

![analyse de l’application actualisée](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la surveillance des exécutions de pipeline, consultez l’article [Surveiller les exécutions de pipeline à l’aide de Synapse Studio](how-to-monitor-pipeline-runs.md).
