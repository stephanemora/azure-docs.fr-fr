---
title: 'Azure Toolkit for IntelliJ : Créer des applications Spark pour un cluster HDInsight '
description: Utilisez le kit de ressources Azure pour IntelliJ pour développer des applications Spark écrites en Scala et envoyez-les à un cluster HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 682b2266442f3aba6696385862844e1b4b93aa3d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702030"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Utiliser Azure Toolkit for IntelliJ afin de créer des applications Apache Spark pour un cluster HDInsight

Utilisez le plug-in Azure Toolkit for IntelliJ pour développer des applications [Apache Spark](https://spark.apache.org/) écrites en [Scala](https://www.scala-lang.org/), puis envoyez-les directement à un cluster Spark HDInsight à partir de l’environnement de développement intégré (IDE) IntelliJ. Vous pouvez utiliser le plug-in de différentes manières :

* Développer et soumettre une application Scala Spark sur un cluster HDInsight Spark.
* Accéder à vos ressources de cluster Azure HDInsight Spark.
* Développer et exécuter une application Scala Spark localement.

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Le [SDK Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Ce tutoriel utilise Java version 8.0.202.
* IntelliJ IDEA. Cet article utilise [IntelliJ IDEA Community  version 2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Consultez [Installation d’Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS.EXE.  Consultez [Problems running Hadoop on Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installer le plug-in Scala pour IntelliJ IDEA
Effectuez les étapes suivantes pour installer le plug-in Scala :

1. Ouvrez IntelliJ IDEA.

2. Dans l’écran d’accueil, accédez à **Configure** > **Plugins** (Configurer > Plug-ins) pour ouvrir la fenêtre **Plugins** (Plug-ins).
   
    ![Activer le plug-in Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Sélectionnez **Install** (Installer) pour le plug-in Scala proposé dans la nouvelle fenêtre.  

    ![Installer le plug-in Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Une fois que le plug-in est bien installé, vous devez redémarrer l’IDE.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Créer une application Scala Spark pour un cluster HDInsight Spark

1. Démarrez IntelliJ IDEA, puis sélectionnez **Create New Project** (Créer un projet) pour ouvrir la fenêtre **New Project** (Nouveau projet).

2. Sélectionnez **Azure Spark/HDInsight** dans le volet gauche.

3. Sélectionnez **Spark Project (Scala)** [Projet Spark (Scala)] dans la fenêtre principale.

4. Dans la liste déroulante **Build tool** (Outil de build), sélectionnez l’un des outils suivants :
   * **Maven** pour la prise en charge de l’Assistant de création de projets Scala.
   * **SBT** pour gérer les dépendances et la génération du projet Scala.

     ![Boîte de dialogue Nouveau projet](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Sélectionnez **Suivant**.

6. Dans la fenêtre **New Project** (Nouveau projet), entrez les informations suivantes :  

    |  Propriété   | Description   |  
    | ----- | ----- |  
    |Nom du projet| Entrez un nom.  Ce didacticiel utilise`myApp`.|  
    |Project&nbsp;location (Emplacement du projet)| Entrez l’emplacement où vous souhaitez enregistrer votre projet.|
    |Project SDK (SDK du projet)| Ce champ peut être vide si vous utilisez IDEA pour la première fois.  Sélectionnez **New...** (Nouveau) et accédez à votre JDK.|
    |Version de Spark|L’Assistant de création intègre la version correcte des SDK Spark et Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x**. Cet exemple utilise **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Sélection du kit de développement logiciel (SDK) Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Sélectionnez **Terminer**.  Vous devrez peut-être patienter quelques minutes avant que le projet soit disponible.

8. Le projet Spark crée automatiquement un artefact à votre intention. Pour afficher l’artefact, procédez comme suit :

   a. À partir de la barre de menus, accédez à **File** > **Project Structure**.

   b. Dans la fenêtre **Project Structure**, sélectionnez **Artifacts**.  

   c. Sélectionnez **Cancel** après avoir visualisé l’artefact.

      ![Informations sur l’artefact dans la boîte de dialogue](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Ajoutez le code source de votre application en procédant comme suit :

    a. À partir de Project, accédez à **myApp** > **src** > **main** > **scala**.  

    b. Cliquez avec le bouton droit sur **scala**, puis accédez à **New** > **Scala Class**.

   ![Commandes pour la création d’une classe Scala à partir du projet](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Dans la boîte de dialogue **Create New Scala Class**, indiquez un nom, sélectionnez **Object** dans la liste déroulante **Kind** (Genre), puis **OK**.

     ![Boîte de dialogue Créer une classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Le fichier **myApp.scala** s’ouvre dans la vue principale. Remplacez le code par défaut par le code ci-dessous :  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la septième colonne du fichier CSV, et écrit la sortie dans `/HVACOut` sous le conteneur de stockage par défaut du cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Se connecter au cluster HDInsight
L’utilisateur peut soit [se connecter à un abonnement Azure](#sign-in-to-your-azure-subscription), soit [lier un cluster HDInsight](#link-a-cluster) avec un identifiant joint à un domaine ou nom d’utilisateur/mot de passe Ambari pour se connecter à votre cluster HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure :

1. Dans la barre de menus, accédez à **Affichage** > **Fenêtres Outil** > **Azure Explorer**.
       
   ![Lien Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Dans Azure Explorer, cliquez avec le bouton droit sur le nœud **Azure**, puis sélectionnez **Se connecter**.

3. Dans la boîte de dialogue **Connexion à Azure**, sélectionnez **Se connecter** et entrez vos informations d’identification Azure.

    ![Boîte de dialogue Connexion à Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Une fois que vous êtes connecté, la boîte de dialogue **Sélectionner des abonnements** répertorie tous les abonnements Azure associés aux informations d’identification. Sélectionnez votre abonnement, puis cliquez sur le bouton **Sélectionner**.

    ![Boîte de dialogue Sélectionner des abonnements](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Dans **Azure Explorer**, développez **HDInsight** pour voir les clusters HDInsight Spark de votre abonnement.

    ![Clusters HDInsight Spark dans l’Explorateur Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.

    ![Nœud de nom de cluster développé](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Lier un cluster
Vous pouvez lier un cluster HDInsight avec le nom d’utilisateur managé Apache Ambari. De même, pour un cluster HDInsight joint à un domaine, vous pouvez effectuer une liaison à l’aide du domaine et du nom d’utilisateur, par exemple user1@contoso.com. Vous pouvez également lier un cluster Livy Service.

1. Dans la barre de menus, accédez à **Affichage** > **Fenêtres Outil** > **Azure Explorer**.

2. Dans Azure Explorer, cliquez avec le bouton droit sur le nœud **HDInsight**, puis sélectionnez **Lier un cluster**.

   ![menu contextuel de lien du cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. Les options disponibles dans la fenêtre **Lier un cluster** dépendent de la valeur que vous sélectionnez dans la liste déroulante **Lier un type de ressource**.  Entrez vos valeurs, puis sélectionnez **OK**.

    * **Cluster HDInsight**  
  
        |Propriété |Valeur |
        |----|----|
        |Lier un type de ressource|Sélectionnez **Cluster HDInsight** dans la liste déroulante.|
        |Nom du cluster/URL| Entrez un nom de cluster.|
        |Type d’authentification| Conservez **Authentification de base**.|
        |User Name| Entrez le nom d’utilisateur de cluster (la valeur par défaut est Admin).|
        |Mot de passe| Entrez le mot de passe du nom d’utilisateur.|
    
        ![Boîte de dialogue Lier un cluster HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Service Livy**  
  
        |Propriété |Valeur |
        |----|----|
        |Lier un type de ressource|Sélectionnez **Livy Service** dans la liste déroulante.|
        |Point de terminaison Livy| Entrez le point de terminaison Livy.|
        |Nom du cluster| Entrez un nom de cluster.|
        |Point de terminaison Yarn|facultatif.|
        |Type d’authentification| Conservez **Authentification de base**.|
        |User Name| Entrez le nom d’utilisateur de cluster (la valeur par défaut est Admin).|
        |Mot de passe| Entrez le mot de passe du nom d’utilisateur.|

        ![Boîte de dialogue Lier un cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Vous pouvez voir votre cluster lié à partir du nœud **HDInsight**.

   ![cluster lié](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Vous pouvez également dissocier un cluster à partir de **Azure Explorer**.

   ![cluster non lié](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Exécuter une application Scala Spark sur un cluster HDInsight Spark
Après avoir créé l’application Scala, vous pouvez l’envoyer au cluster.

1. À partir de Project, accédez à **myApp** > **src** > **main** > **scala** > **myApp**.  Cliquez avec le bouton droit sur **myApp**, puis sélectionnez **Submit Spark Application** (cette option sera probablement en bas de la liste).
    
      ![Commande d’envoi de l’application Spark à HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Dans la boîte de dialogue **Submit Spark Application**, sélectionnez **1. Spark on HDInsight**.

3. Dans la fenêtre **Edit configuration** (Modifier la configuration), entrez les valeurs suivantes, puis sélectionnez **OK** :

    |Propriété |Valeur |
    |----|----|
    |Clusters Spark (Linux uniquement)|Sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.|
    |Sélectionner un artefact à envoyer|Conservez le paramètre par défaut.|
    |Nom de la classe main|la valeur par défaut est la classe main du fichier sélectionné. Vous pouvez modifier la classe en sélectionnant les points de suspension (**...**) pour choisir une autre classe.|
    |Configurations du travail|Vous pouvez changer les clés et/ou les valeurs par défaut. Pour plus d’informations, consultez [API REST Apache Livy](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Arguments de ligne de commande|Vous pouvez entrer des arguments séparés par un espace pour la classe main, si nécessaire.|
    |JAR référencés et fichiers référencés|vous pouvez entrer les chemins des JAR et fichiers référencés, le cas échéant. Pour plus d'informations : [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Consultez également [Guide pratique pour charger des ressources sur un cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Stockage des chargements de travaux|Développez pour afficher des options supplémentaires.|
    |Type de stockage|Sélectionnez **Utiliser l’objet blob Azure pour charger** dans la liste déroulante.|
    |Compte de stockage|Entrez votre compte de stockage.|
    |Clé de stockage|Entrez votre clé de stockage.|
    |Conteneur de stockage|Sélectionnez votre conteneur de stockage dans la liste déroulante après avoir entré les valeurs **Compte de stockage** et **Clé de stockage**.|

    ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Sélectionnez **SparkJobRun** pour envoyer votre projet au cluster sélectionné. La progression de l’exécution de la tâche est affichée au bas de l’onglet **Remote Spark Job in Cluster** (Tâche Spark distante du cluster). Vous pouvez arrêter l’application en cliquant sur le bouton rouge. Pour savoir comment accéder à la sortie du travail, consultez la section « Accéder aux clusters HDInsight Spark et les gérer à l’aide du kit de ressources Azure pour IntelliJ » plus loin dans cet article.  
      
    ![Fenêtre Spark Submission (Envoi Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Déboguer des applications Apache Spark localement ou à distance sur un cluster HDInsight 
Nous recommandons également un autre mode de soumission de l’application Spark au cluster. Vous pouvez définir les paramètres dans l’IDE **Exécuter/déboguer des configurations**. Pour plus d’informations, voir [Déboguer des applications Apache Spark localement ou à distance sur un cluster HDInsight avec Azure Toolkit for IntelliJ par SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accéder aux clusters HDInsight Spark et les gérer à l’aide du kit de ressources Azure pour IntelliJ
Vous pouvez effectuer diverses opérations à l’aide du kit de ressources Azure pour IntelliJ.  La plupart des opérations sont lancées à partir d’**Azure Explorer**.  Dans la barre de menus, accédez à **Affichage** > **Fenêtres Outil** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Accéder à la vue des travaux

1. Dans Azure Explorer, accédez à **HDInsight** > \<votre_cluster> > **Travaux**.

    ![Nœud Vue de la tâche](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. Dans le volet droit, l’onglet **Spark Job View** (Affichage des travaux Spark) affiche toutes les applications qui ont été exécutées sur le cluster. Sélectionnez le nom de l’application pour laquelle vous souhaitez afficher plus de détails.

    ![Détails de l’application](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Pour afficher des informations de base sur les travaux en cours d’exécution, passez le curseur sur le graphique du travail. Pour afficher le graphique des étapes et les informations que chaque travail génère, sélectionnez un nœud sur le graphique du travail.

    ![Détails des étapes du travail](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Pour afficher les journaux d’activité fréquemment utilisés tels que les journaux d’activité *Driver Stderr*, *Driver Stdout* et *Directory Info*, sélectionnez l’onglet **Journal**.

    ![Détails des journaux](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Vous pouvez également afficher l’interface utilisateur de l’historique Spark et l’interface utilisateur YARN (au niveau de l’application) en sélectionnant un lien en haut de la fenêtre.

### <a name="access-the-spark-history-server"></a>Accéder au serveur d’historique Spark

1. Dans Azure Explorer, développez **HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark).  
2. Lorsque vous y êtes invité, entrez les informations d’identification administrateur du cluster que vous avez spécifiées lors de la configuration de ce dernier.

3. Dans le tableau de bord du serveur d’historique Spark, vous pouvez utiliser le nom de l’application pour rechercher l’application que vous venez d’exécuter. Dans le code précédent, vous définissez le nom de l’application en utilisant `val conf = new SparkConf().setAppName("myApp")`. Par conséquent, le nom de votre application Spark est **myApp**.

### <a name="start-the-ambari-portal"></a>Démarrer le portail Ambari

1. Dans Azure Explorer, développez **HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters (Ambari)).  

2. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous avez spécifié ces informations d’identification lors du processus de configuration des clusters.

### <a name="manage-azure-subscriptions"></a>Gérer les abonnements Azure
Par défaut, le kit de ressources Azure pour IntelliJ répertorie les clusters Spark à partir de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements auxquelles vous souhaitez accéder.  

1. Dans Azure Explorer, cliquez avec le bouton droit sur le nœud racine **Azure**, puis sélectionnez **Sélectionner des abonnements**.  

2. Dans la fenêtre **Sélectionner des abonnements**, décochez les cases à côté des abonnements auxquels vous ne souhaitez pas accéder, puis sélectionnez **Fermer**.

## <a name="spark-console"></a>Console Spark
Vous pouvez exécutez Spark Local Console(Scala) ou Spark Livy Interactive Session Console(Scala).

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
Vérifiez que le prérequis relatif à WINUTILS.EXE est respecté.

1. À partir de la barre de menus, accédez à **Run** > **Edit Configurations...** (Exécuter > Modifier les configurations).

2. Dans la fenêtre **Run/Debug Configurations** (Exécuter/déboguer des wonfigurations), dans le volet gauche, accédez à **Apache Spark on HDInsight** > **[Spark on HDInsight] myApp**.

3. Dans la fenêtre principale, sélectionnez l’onglet **Locally Run** (Exécuter localement).

4. Entrez les valeurs suivantes, puis sélectionnez **OK** :

    |Propriété |Valeur |
    |----|----|
    |Job main class (Classe main du travail)|la valeur par défaut est la classe main du fichier sélectionné. Vous pouvez modifier la classe en sélectionnant les points de suspension (**...**) pour choisir une autre classe.|
    |Variables d’environnement|Vérifiez que la valeur de HADOOP_HOME est correcte.|
    |WINUTILS.exe location (Emplacement de WINUTILS.exe)|Vérifiez que le chemin est correct.|

    ![Configuration de la console locale](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. À partir de Project, accédez à **myApp** > **src** > **main** > **scala** > **myApp**.  

6. À partir de la barre de menus, accédez à **Tools** > **Spark Console** > **Run Spark Local Console(Scala)** (Outils > Console Spark > Exécuter la console Spark locale (Scala)).

7. Ensuite, deux boîtes de dialogue peuvent s’afficher pour vous demander si vous souhaitez corriger automatiquement les dépendances. Dans ce cas, sélectionnez **Auto Fix** (Correction automatique).

    ![Spark Auto Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark Auto Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. La console doit ressembler à l’image ci-dessous. Dans la fenêtre de console, tapez `sc.appName`, puis appuyez sur Ctrl+Entrée.  Le résultat s’affichera. Vous pouvez clore la console locale en cliquant sur le bouton rouge.

    ![Résultat dans la console locale](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)
Elle est uniquement prise en charge sur IntelliJ 2018.2 et 2018.3.

1. À partir de la barre de menus, accédez à **Run** > **Edit Configurations...** (Exécuter > Modifier les configurations).

2. Dans la fenêtre **Run/Debug Configurations** (Exécuter/déboguer des wonfigurations), dans le volet gauche, accédez à **Apache Spark on HDInsight** > **[Spark on HDInsight] myApp**.

3. Dans la fenêtre principale, sélectionnez l’onglet **Remotely Run in Cluster** (Exécuter à distance dans le cluster).

4. Entrez les valeurs suivantes, puis sélectionnez **OK** :

    |Propriété |Valeur |
    |----|----|
    |Clusters Spark (Linux uniquement)|Sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.|
    |Nom de la classe main|la valeur par défaut est la classe main du fichier sélectionné. Vous pouvez modifier la classe en sélectionnant les points de suspension (**...**) pour choisir une autre classe.|

    ![Configuration de la console interactive](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. À partir de Project, accédez à **myApp** > **src** > **main** > **scala** > **myApp**.  

6. À partir de la barre de menus, accédez à **Tools** > **Spark Console** > **Run Spark Livy Interactive Session Console(Scala)** (Exécuter la console de session interactive Spark Livy (Scala)).

7. La console doit ressembler à l’image ci-dessous. Dans la fenêtre de console, tapez `sc.appName`, puis appuyez sur Ctrl+Entrée.  Le résultat s’affichera. Vous pouvez clore la console locale en cliquant sur le bouton rouge.

    ![Résultat dans la console interactive](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Envoyer la sélection à la console Spark

Il est pratique pour vous de prévoir le résultat du script en envoyant du code à la console locale ou à la console de session interactive Livy (Scala). Vous pouvez mettre en évidence du code dans le fichier Scala, puis cliquer avec le bouton droit sur **Send Selection To Spark Console** (Envoyer la sélection à la console Spark). Le code sélectionné est envoyé à la console afin d’être traité. Le résultat s’affiche après le code dans la console. La console vérifiera les erreurs s’il y en a.  

   ![Envoyer la sélection à la console Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Convertir des applications IntelliJ IDEA existantes pour qu’elles utilisent le kit de ressources Azure pour IntelliJ

Vous pouvez convertir les applications Spark Scala existantes que vous avez créées dans IntelliJ IDEA pour qu’elles soient compatibles avec le kit de ressources Azure pour IntelliJ. Vous pouvez ensuite utiliser le plug-in pour envoyer les applications à un cluster HDInsight Spark.

1. Pour une application Spark Scala existante créée à l’aide d’IntelliJ IDEA, ouvrez le fichier .iml associé.

1. Au niveau de la racine se trouve un élément **module** comme celui-ci :
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Ajoutez `UniqueKey="HDInsightTool"` à la fin de l’élément **module** , qui doit alors ressembler à ceci :
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Enregistrez les modifications. Votre application doit maintenant être compatible avec le kit de ressources Azure pour IntelliJ. Vous pouvez effectuer un test en cliquant avec le bouton droit sur le nom du projet dans Project. Le menu contextuel doit maintenant comporter l’option **Submit Spark Application to HDInsight**(Envoyer l’application Spark à HDInsight).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Erreur dans l’exécution locale : *Use a larger heap size* (Utilisez un tas de plus grande taille)
Dans Spark 1.6, si vous utilisez un kit de développement logiciel (SDK) Java 32 bits au cours de l’exécution locale, vous pouvez rencontrer les erreurs suivantes :

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Ces erreurs se produisent parce que le segment de mémoire n’est pas assez grand pour l’exécution de Spark. Spark nécessite au moins 471 Mo. (Pour plus d’informations, voir [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Une solution simple consiste à utiliser un kit de développement logiciel (SDK) Java 64 bits. Vous pouvez également modifier les paramètres de la JVM dans IntelliJ en ajoutant les options suivantes :

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Ajout d’options à la zone « Options de la machine virtuelle » dans IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Forum Aux Questions
Si le cluster est occupé, l’erreur ci-dessous risque de se produire.

![Intellij get error when cluster busy](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij get error when cluster busy](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Commentaires et problèmes connus
Pour l’instant, la visualisation directe des sorties Spark n’est pas prise en charge.

Si vous avez des suggestions ou des commentaires, ou que vous rencontrez des problèmes lors de l’utilisation de ce plug-in, envoyez-nous un e-mail à l’adresse hdivstool@microsoft.com.

## <a name="seealso"></a>Étapes suivantes
* [Présentation : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créer un projet Scala (vidéo) : [Créer des applications Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Apache Spark avec BI : Effectuer une analyse interactive des données à l’aide de Spark dans HDInsight avec les outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour analyser la température de bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans Azure Toolkit for Eclipse pour créer des applications Apache Spark](apache-spark-eclipse-tool-plugin.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
