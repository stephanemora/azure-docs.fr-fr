---
title: 'Azure Toolkit for IntelliJ : Déboguer les applications Spark à distance via SSH '
description: Obtenez des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur des clusters HDInsight via SSH
keywords: déboguer à distance intellij, débogage à distance intellij, ssh, intellij, hdinsight, déboguer intellij, débogage
services: hdinsight
ms.service: hdinsight
author: hrasheed
ms.author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 87a774fad6b7e45f233102b7fb026aa9663dceb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005924"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Déboguez les applications Apache Spark localement ou à distance, sur un cluster HDInsight, avec Azure Toolkit for IntelliJ via SSH

Cet article propose des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le [kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) afin de déboguer des applications à distance sur un cluster HDInsight. Pour déboguer votre projet, vous pouvez également regarder la vidéo [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Déboguer des applications HDInsight Spark avec le kit de ressources Azure pour IntelliJ).

**Composants requis**
* **Outils HDInsight dans le kit de ressources Azure pour IntelliJ**. Cet outil fait partie du kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Et le **kit de ressources Azure pour IntelliJ**. Utilisez ce kit de ressources pour créer des applications Apache Spark pour un cluster HDInsight. Pour plus d’informations, veuillez suivre les instructions de la rubrique [Utiliser le kit de ressources Azure pour IntelliJ pour créer des applications Apache Spark pour un cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Service HDInsight SSH avec gestion de nom d’utilisateur et de mot de passe**. Pour plus d’informations, consultez [Connexion à HDInsight (Apache Hadoop) à l’aide de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) et [Utiliser le tunneling SSH pour accéder à l’IU web Ambari, JobHistory, NameNode, Apache Oozie et à d’autres IU web](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Découvrez comment effectuer une exécution et un débogage locaux
### <a name="scenario-1-create-a-spark-scala-application"></a>Scénario 1 : Créer une application Spark Scala 

1. Démarrez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue **Nouveau projet** , procédez comme suit :

   a. Sélectionnez **Azure Spark/HDInsight**. 

   b. Sélectionnez un modèle Java ou Scala en fonction de vos préférences. Sélectionnez entre les options suivantes :

   - **Projet Spark (Java)**

   - **Projet Spark (Scala)**

   - **Projet Spark avec exemples (Scala)**

   - **Projet Spark avec la tâche d’échec (version préliminaire) (Scala) d’exemples de débogage**

     Cet exemple utilise un **projet Spark avec exemples (Scala)** modèle.

   c. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

   - **Maven**, pour la prise en charge de l’Assistant de création de projets Scala

   - **SBT**, pour gérer les dépendances et la génération du projet Scala 

     ![Créer un projet de débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Sélectionnez **Suivant**.     
 
1. Dans la fenêtre **Nouveau projet** qui s’ouvre, effectuez les opérations suivantes :

   ![Sélectionner le SDK Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Entrez un nom de projet et un emplacement de projet.

   b. Dans la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Java 1.8** pour le cluster **Spark 2.x** ou **Java 1.7** pour le cluster **Spark 1.x**.

   c. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le SDK Spark et le SDK Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x.** La version utilisée dans cet exemple est **Spark 2.0.2 (Scala 2.11.8)**.

   d. Sélectionnez **Terminer**.

1. Sélectionnez **src** > **principal** > **scala** pour ouvrir votre code dans le projet. Cet exemple utilise le script **SparkCore_wasbloTest**.

### <a name="prerequisite-for-windows"></a>Configuration requise pour Windows
Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Cette exception est liée à l’absence du fichier WinUtils.exe sur Windows. 

Pour résoudre cette erreur, [téléchargez le fichier exécutable](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**. Ajoutez ensuite la variable d’environnement **HADOOP_HOME** et définissez la valeur de la variable sur **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scénario 2 : Effectuer une exécution locale
1. Ouvrez le script **SparkCore_wasbloTest**, cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez l’option **Exécuter '[travail Spark] XXX'** pour effectuer l’exécution locale.
1. Une fois l’exécution locale terminée, vous pouvez voir le fichier de sortie enregistré dans l’Explorateur de votre projet actuel **data** > **__default__**.

    ![Résultat de l’exécution locale](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Nos outils définissent la configuration de l’exécution locale par défaut automatiquement lorsque vous effectuez l’exécution et le débogage locaux. Ouvrez la configuration **[Spark sur HDInsight] XXX** dans le coin supérieur droit, vous pouvez voir le **[Spark sur HDInsight] XXX** déjà créée sous **Apache Spark sur HDInsight**. Basculez vers l’onglet **Locally Run (Exécuter localement)**.

    ![Configuration de l’exécution locale](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Variables d’environnement](#prerequisite-for-windows) : si vous avez déjà défini la variable d’environnement système **HADOOP_HOME** sur **C:\WinUtils**, elle peut détecter automatiquement qu’aucun ajout manuel n’est nécessaire.
    - [Emplacement de WinUtils.exe](#prerequisite-for-windows) : si vous n’avez pas défini la variable d’environnement système, vous pouvez trouver l’emplacement en cliquant sur son bouton.
    - Choisissez simplement l’une des deux options ; elles ne sont pas requises sur MacOS et Linux.
1. Vous pouvez également définir la configuration manuellement avant d’effectuer l’exécution et le débogage locaux. Dans la capture d’écran précédente, sélectionnez le signe plus (**+**). Puis sélectionnez le **Apache Spark sur HDInsight** option. Entrez les informations de **Nom** et **Nom de la classe principale** à enregistrer, puis cliquez sur le bouton d’exécution locale.

### <a name="scenario-3-perform-local-debugging"></a>Scénario 3 : Effectuer un débogage local
1. Ouvrez le script **SparkCore_wasbloTest**, et définissez les points d’arrêt.
1. Avec le bouton droit de l’éditeur de script, puis sélectionnez l’option **déboguer ' [Spark sur HDInsight] XXX'** pour effectuer un débogage local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Découvrez comment effectuer une exécution et un débogage à distance
### <a name="scenario-1-perform-remote-run"></a>Scénario 1 : Effectuer une exécution à distance

1. Pour accéder au menu **Modifier les configurations**, sélectionnez l’icône dans le coin supérieur droit. Dans ce menu, vous pouvez créer ou modifier les configurations pour le débogage à distance.

   ![Modifier les configurations](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Dans la boîte de dialogue **Run/Debug Configurations** (Exécuter/Déboguer les configurations) sélectionnez le signe plus (**+**). Puis sélectionnez le **Apache Spark sur HDInsight** option.

   ![Ajouter une nouvelle configuration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Basculez vers l’onglet **Remotely Run in Cluster (Exécuter à distance dans le cluster)**. Entrez les informations sur le **Nom**, le **Cluster Spark** et le **Nom principal de la classe**. Puis cliquez sur **configuration avancée (débogage à distance)**. Nos outils prennent en charge le débogage avec **Exécuteurs**. Pour **numExectors**, la valeur par défaut est 5. Il est déconseillé de définir une valeur supérieure à 3.

   ![Exécuter le débogage des configurations](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Dans le **Configuration avancée (débogage à distance)** partie, sélectionnez **débogage à distance Spark activer**. Entrez le nom d’utilisateur SSH, puis entrez un mot de passe ou utilisez un fichier de clé privée. Si vous souhaitez effectuer le débogage à distance, vous devez définir cette option. Par contre, cette action n’est pas nécessaire si vous souhaitez simplement utiliser l’exécution à distance.

   ![Activer le débogage à distance Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. La configuration est maintenant enregistrée avec le nom fourni. Pour afficher les détails de configuration, sélectionnez le nom de configuration. Pour apporter des modifications, sélectionnez **Modifier les configurations**. 

1. Une fois le paramétrage des configurations terminé, vous pouvez exécuter le projet sur le cluster à distance ou effectuer un débogage à distance.
   
   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Cliquez sur le bouton **Déconnecter** si les journaux d’activité d’envoi n’apparaissent pas dans le panneau gauche. Toutefois, une exécution a toujours lieu sur le serveur principal.

   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scénario 2 : Effectuer un débogage à distance
1. Configurez des points de rupture, puis sélectionnez l’icône **Débogage distant**. La différence avec la soumission à distance est que le nom d’utilisateur/mot de passe SSH doit être configuré.

   ![Sélectionner l’icône de débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quand l’exécution du programme atteint le point de rupture, un onglet **Pilote** et deux onglets **Exécuteur** s’affichent dans le volet **Débogueur**. Sélectionnez l’icône **Reprendre le programme** pour continuer à exécuter le code ; le point de rupture suivant est alors atteint. Vous devez basculer vers l’onglet **Exécuteur** correct pour trouver l’exécuteur cible à déboguer. Vous pouvez consulter les journaux d’activité d’exécution sous l’onglet **Console** correspondant.

   ![Onglet Débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scénario 3 : Effectuer un débogage et une résolution des bogues à distance
1. Configurez deux points de rupture, puis sélectionnez l’icône **Déboguer** pour démarrer le processus de débogage à distance.

1. Le code s’arrête au premier point de rupture et les informations sur les paramètres et les variables s’affichent dans le volet **Variable**. 

1. Sélectionnez l’icône **Reprendre le programme** pour continuer. Le code s’arrête au deuxième point. L’exception est interceptée comme prévu.

   ![Lever l’erreur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Resélectionnez l’icône **Reprendre le programme**. La fenêtre **Soumission HDInsight Spark** affiche une erreur d’exécution de tâche.

   ![Soumission de l’erreur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Pour mettre à jour dynamiquement la valeur de la variable à l’aide de la fonctionnalité de débogage IntelliJ, resélectionnez **Déboguer**. Le volet **Variables** réapparaît. 

1. Cliquez avec le bouton droit de la cible de l’onglet **Déboguer**, puis sélectionnez **Définir la valeur**. Ensuite, entrez une nouvelle valeur pour la variable. Puis sélectionnez **Entrer** pour enregistrer la valeur. 

   ![Définir la valeur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Sélectionnez l’icône **Reprendre le programme** pour continuer à exécuter le programme. Cette fois-ci, aucune exception n’est interceptée. Vous pouvez voir que le projet s’exécute correctement sans aucune exception.

   ![Déboguer sans exception](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Étapes suivantes
* [Présentation : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créer un projet Scala (vidéo) : [Créer des applications Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Apache Spark avec BI : Effectuer une analyse interactive des données à l’aide de Spark dans HDInsight avec les outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour analyser la température de bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser Azure Toolkit for IntelliJ afin de créer des applications Apache Spark pour un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
