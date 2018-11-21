---
title: 'Azure Toolkit for IntelliJ : créer des applications Spark pour un cluster HDInsight '
description: Utilisez le kit de ressources Azure pour IntelliJ pour développer des applications Spark écrites en Scala et envoyez-les à un cluster HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: ff7cfcd56158bd38d031a29a21247fb9eb6b91f9
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289068"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Utiliser le kit de ressources Azure pour IntelliJ pour créer des applications Spark pour un cluster HDInsight

Utilisez le plug-in du kit de ressources Azure pour IntelliJ pour développer des applications Spark écrites en Scala et envoyez-les à un cluster HDInsight Spark, directement à partir de l’environnement de développement intégré (IDE) IntelliJ. Vous pouvez utiliser le plug-in de différentes manières :

* Développer et soumettre une application Scala Spark sur un cluster HDInsight Spark.
* Accéder à vos ressources de cluster Azure HDInsight Spark.
* Développer et exécuter une application Scala Spark localement.

Pour créer votre projet, consultez la vidéo [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)(Créer des applications Spark à l’aide du Kit de ressources Azure pour IntelliJ).

> [!IMPORTANT]
> Vous pouvez utiliser ce plug-in pour créer des applications et les envoyer à un cluster HDInsight Spark sur Linux uniquement.
> 

## <a name="prerequisites"></a>Prérequis

- Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir du [site web Oracle](https://aka.ms/azure-jdks).
- IntelliJ IDEA. Cet article utilise la version 2017.1. Vous pouvez l’installer à partir du [site web JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Installer le kit de ressources Azure pour IntelliJ
Pour plus d’informations, consultez [Installation du kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Prise en main
Pour commencer, l’utilisateur peut soit [se connecter à un abonnement Azure](#sign-in-to-your-azure-subscription), soit [lier un cluster HDInsight](#link-a-cluster) avec un identifiant joint à un domaine ou nom d’utilisateur/mot de passe Ambari.


## <a name="sign-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure :

1. Démarrez l’IDE IntelliJ et ouvrez Azure Explorer. Dans le menu **Affichage**, sélectionnez **Fenêtres d’outil**, puis **Azure Explorer**.
       
   ![Lien Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Cliquez avec le bouton droit sur le nœud **Azure**, puis sélectionnez **Se connecter**.

1. Dans la boîte de dialogue **Connexion à Azure**, sélectionnez **Se connecter** et entrez vos informations d’identification Azure.

    ![Boîte de dialogue Connexion à Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Une fois que vous êtes connecté, la boîte de dialogue **Sélectionner des abonnements** répertorie tous les abonnements Azure associés aux informations d’identification. Cliquez sur le bouton **Sélectionner**.

    ![Boîte de dialogue Sélectionner des abonnements](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. Dans l’onglet **Azure Explorer**, développez **HDInsight** pour afficher les clusters HDInsight Spark de votre abonnement.
   
    ![Clusters HDInsight Spark dans l’Explorateur Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.
   
    ![Nœud de nom de cluster développé](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Lier un cluster
Vous pouvez lier un cluster HDInsight normal en utilisant le nom d’utilisateur Ambari managé. De même, pour un cluster HDInsight joint à un domaine, vous pouvez effectuer une liaison à l’aide du domaine et du nom d’utilisateur, par exemple user1@contoso.com. Vous pouvez également lier un cluster Livy Service.

1. Sélectionnez **Link a cluster (Lier un cluster)** dans l’**explorateur Azure**.

   ![menu contextuel de lien du cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Vous disposez de deux options pour lier des clusters. 

   * Pour lier un cluster HDInsight, choisissez **Cluster HDInsight** dans le champ **Cluster Info** (Informations sur le cluster), puis entrez **l’URL ou le nom du cluster**, le **nom de l’utilisateur** et le **mot de passe**.

      ![Boîte de dialogue Lier un cluster HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Pour lier un cluster de service Livy, choisissez **Service Livy** dans le champ **Cluster Info** (Informations sur le cluster), puis entrez un **point de terminaison Livy** et le **nom du cluster**. Le **point de terminaison Yarn** est facultatif. Dans le champ **Authentification**, deux options sont fournies. Il s’agit des options **Authentification de base** et **Aucune authentification**. Lorsque vous sélectionnez **Authentification de base**, vous devez fournir un **nom d’utilisateur** et un **mot de passe**. Si l’authentification a échoué, vous devez vérifier le nom d’utilisateur et le mot de passe.
      
      ![Boîte de dialogue Lier un cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. Si les informations d’entrée sont correctes, vous pouvez voir un cluster lié dans le nœud **HDInsight**. Vous pouvez désormais soumettre une application à ce cluster lié.

   ![cluster lié](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Vous pouvez également dissocier un cluster à partir de **Azure Explorer**.
   
   ![cluster non lié](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Créer une application Scala Spark sur un cluster HDInsight Spark

1. Démarrez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue **Nouveau projet**, effectuez les étapes suivantes : 

   a. Sélectionnez **HDInsight** > **Spark sur HDInsight (Scala)**.

   b. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

      * **Maven**, pour la prise en charge de l’Assistant de création de projets Scala
      * **SBT**, pour gérer les dépendances et la génération du projet Scala

    ![Boîte de dialogue Nouveau projet](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Sélectionnez **Suivant**.

1. L’assistant de création de projets Scala détecte automatiquement si vous avez installé le plug-in Scala. Sélectionnez **Installer**.

   ![Vérification de l’installation du plug-in Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Sélectionnez **OK** pour télécharger le plug-in Scala. Suivez les instructions pour redémarrer IntelliJ. 

   ![Boîte de dialogue d’installation du plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. Dans la fenêtre **Nouveau projet**, procédez comme suit :  

    ![Sélection du kit de développement logiciel (SDK) Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Entrez un nom de projet et un emplacement.

   b. Dans la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Java 1.8** pour le cluster Spark 2.x ou **Java 1.7** pour le cluster Spark 1.x.

   c. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le kit de développement logiciel Spark et le kit de développement logiciel Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x**. La version utilisée dans cet exemple est **Spark 2.0.2 (Scala 2.11.8)**.

1. Sélectionnez **Terminer**.

1. Le projet Spark crée automatiquement un artefact à votre intention. Pour afficher l’artefact, procédez comme suit :

   a. Dans le menu **Fichier**, sélectionnez **Structure de projet**.

   b. Dans la boîte de dialogue **Structure de projet**, sélectionnez **Artefacts** pour afficher l’artefact créé par défaut. Vous pouvez également créer votre propre artefact en sélectionnant le signe plus (**+**).

      ![Informations sur l’artefact dans la boîte de dialogue](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Ajoutez le code source de votre application en procédant comme suit :

   a. Dans l’Explorateur de projets, cliquez avec le bouton droit sur **src**, pointez sur **Nouveau**, puis sélectionnez **Scala class** (Classe Scala).
      
      ![Commandes pour la création d’une classe Scala à partir de l’Explorateur de projets](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. Dans la boîte de dialogue **Create New Scala Class** (Créer une classe Scala), indiquez un nom, dans la zone **Kind** (Genre), sélectionnez **Objet**, puis **OK**.
      
      ![Boîte de dialogue Créer une classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. Dans le fichier **MyClusterApp.scala** , collez le code suivant. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la septième colonne du fichier CSV et écrit la sortie dans **/HVACOut** sous le conteneur de stockage par défaut du cluster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Exécuter une application Scala Spark sur un cluster HDInsight Spark
Après avoir créé l’application Scala, vous pouvez l’envoyer au cluster.

1. Dans l’Explorateur de projets, localisez un fichier Java ou Scala, puis sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight) dans le menu contextuel.
    
      ![Commande d’envoi de l’application Spark à HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Dans la boîte de dialogue de configuration, entrez les valeurs suivantes, puis cliquez sur **SparkJobRun**.

      ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * Dans la zone **Spark clusters (Linux only)**(Clusters Spark (Linux uniquement)), sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.

    * Sélectionnez un artefact à partir du projet IntelliJ, ou choisissez-en un sur le disque dur.

    * Champ **Main class name** (Nom de la classe main) : la valeur par défaut est la classe main du fichier sélectionné. Vous pouvez modifier la classe en sélectionnant les points de suspension (**...**) pour choisir une autre classe.   

    * Champ **Job Configurations** (Configurations de tâche) : les valeurs par défaut sont définies comme dans l’image ci-dessus. Vous pouvez modifier la valeur ou ajouter une clé ou une valeur à votre envoi de tâche. Pour plus d’informations, consultez [API REST Apache Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![Signification de la configuration du travail dans la boîte de dialogue d’envoi de Spark](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * Champ **Command line arguments** (Arguments de ligne de commande) : pour la classe main, vous pouvez entrer des valeurs d’arguments séparées pour chaque espace, si nécessaire.

    * Champs **Referenced Jars** (JAR référencés) et **Referenced Files** (Fichiers référencés) : vous pouvez entrer les chemins des JAR et des fichiers référencés, le cas échéant. Pour plus d’informations, consultez [Configuration Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment). 

      ![Signification des fichiers JAR dans la boîte de dialogue d’envoi de Spark](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > Pour charger vos JAR et vos fichiers référencés, reportez-vous au [Guide pratique pour charger des ressources vers un cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).
                         
    * **Upload Path** (Chemin de chargement) : vous pouvez indiquer l’emplacement de stockage pour l’envoi des ressources de projet Jar ou Scala. Vous pouvez choisir parmi trois options de stockage : **Azure Blob** (Stockage Blob Azure), **Use Spark interactive session to upload artifacts** (Utiliser la session interactive Spark pour charger les artefacts) et **Use cluster default storage account** (Utiliser le compte de stockage par défaut du cluster) et **ADLS Gen1**. La capture d’écran ci-dessous est un exemple de Stockage Blob Azure.

        ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Cliquez sur **SparkJobRun** pour envoyer votre projet au cluster sélectionné. La progression de l’exécution de la tâche est affichée au bas de l’onglet **Remote Spark Job in Cluster** (Tâche Spark distante du cluster). Vous pouvez arrêter l’application en cliquant sur le bouton rouge. Pour savoir comment accéder à la sortie du travail, consultez la section « Accéder aux clusters HDInsight Spark et les gérer à l’aide du kit de ressources Azure pour IntelliJ » plus loin dans cet article.
      
     ![Fenêtre Spark Submission (Envoi Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Déboguer les applications Spark localement ou à distance sur un cluster HDInsight 
Nous recommandons également un autre mode de soumission de l’application Spark au cluster. Vous pouvez définir les paramètres dans l’IDE **Exécuter/déboguer des configurations**. Pour plus d’informations, reportez-vous à la rubrique [Déboguer des applications Spark localement ou à distance sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Accéder aux clusters HDInsight Spark et les gérer à l’aide du kit de ressources Azure pour IntelliJ
Vous pouvez effectuer diverses opérations à l’aide du kit de ressources Azure pour IntelliJ.

### <a name="access-the-job-view"></a>Accéder à la vue des travaux
1. Dans Azure Explorer, développez **HDInsight**, développez le nom du cluster Spark, puis sélectionnez **Travaux**.  

    ![Nœud Vue de la tâche](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. Dans le volet droit, l’onglet **Spark Job View** (Affichage des travaux Spark) affiche toutes les applications qui ont été exécutées sur le cluster. Sélectionnez le nom de l’application pour laquelle vous souhaitez afficher plus de détails.

    ![Détails de l’application](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Remarque
    >

1. Pour afficher des informations de base sur les travaux en cours d’exécution, passez le curseur sur le graphique du travail. Pour afficher le graphique des étapes et les informations que chaque travail génère, sélectionnez un nœud sur le graphique du travail.

    ![Détails des étapes du travail](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Pour afficher les journaux fréquemment utilisés tels que les journaux *Driver Stderr*, *Driver Stdout* et *Directory Info*, sélectionnez l’onglet **Journal**.

    ![Détails des journaux](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. Vous pouvez également afficher l’interface utilisateur de l’historique Spark et l’interface utilisateur YARN (au niveau de l’application) en sélectionnant un lien en haut de la fenêtre.

### <a name="access-the-spark-history-server"></a>Accéder au serveur d’historique Spark
1. Dans Azure Explorer, développez **HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark). 

1. Lorsque vous y êtes invité, entrez les informations d’identification administrateur du cluster que vous avez spécifiées lors de la configuration de ce dernier.

1. Dans le tableau de bord du serveur d’historique Spark, vous pouvez utiliser le nom de l’application pour rechercher l’application que vous venez d’exécuter. Dans le code précédent, vous définissez le nom de l’application en utilisant `val conf = new SparkConf().setAppName("MyClusterApp")`. Par conséquent, le nom de votre application Spark est **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Démarrer le portail Ambari
1. Dans Azure Explorer, développez **HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters (Ambari)). 

1. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous avez spécifié ces informations d’identification lors du processus de configuration des clusters.

### <a name="manage-azure-subscriptions"></a>Gérer les abonnements Azure
Par défaut, le kit de ressources Azure pour IntelliJ répertorie les clusters Spark à partir de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements auxquelles vous souhaitez accéder. 

1. Dans Azure Explorer, cliquez avec le bouton droit sur le nœud racine **Azure**, puis sélectionnez **Gérer les abonnements**. 

1. Dans la boîte de dialogue, décochez les cases en regard des abonnements auquel vous ne souhaitez pas accéder, puis sélectionnez **Fermer**. Vous pouvez également sélectionner **Se déconnecter** si vous souhaitez vous déconnecter de votre abonnement Azure.

## <a name="spark-console"></a>Console Spark
Vous pouvez exécutez Spark Local Console(Scala) ou Spark Livy Interactive Session Console(Scala).

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
1. Procédez à la configuration, si ce n’est déjà fait. Dans la fenêtre **Run/Debug Configurations** (Exécuter/Déboguer des configurations), cliquez sur **+**->**Azure HDInsight Spark**, sélectionnez l’onglet **Locally Run** (Exécuter localement) et **Remotely Run in Cluster** (Exécuter à distance dans le cluster), choisissez la classe main, puis cliquez sur **OK**.

    ![Configuration de la console locale](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. Ouvrez le fichier de classe main correspondant, cliquez avec le bouton droit sur **Spark Console**, puis cliquez sur **Run Spark Local Console(Scala)** (Exécuter Spark Local Console(Scala)). Pour lancer la console, vous pouvez également accéder au menu **Tools (Outils)**->**Spark Console**->**Run Spark Local Console(Scala) (Exécuter Spark Local Console(Scala))**. Ensuite, deux boîtes de dialogue s’affichent pour vous demander si vous souhaitez corriger automatiquement les dépendances. Il suffit de cliquer sur le bouton **Auto Fix** (Corriger automatiquement).

    ![Spark Auto Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark Auto Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Point d’entrée de Spark Local](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. Une fois la console locale lancée, vous devez voir l’écran ci-dessous. Vous pouvez y effectuer l’action de votre choix. Par exemple, vous pouvez entrer **sc.appName**, puis appuyer sur Ctrl + Entrée pour afficher le résultat. Vous pouvez clore la console locale en cliquant sur le bouton rouge.

    ![Résultat dans la console locale](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)
Elle est uniquement prise en charge sur IntelliJ 2018.2 et 2018.3.

1. Procédez à la configuration, si ce n’est déjà fait. Dans la fenêtre **Run/Debug Configurations** (Exécuter/Déboguer des configurations), cliquez sur **+**->**Azure HDInsight Spark**, sélectionnez l’onglet **Remotely Run in Cluster** (Exécuter à distance dans le cluster), choisissez le nom du cluster et la classe main, puis cliquez sur **OK**.

    ![Ajout d’entrée de configuration dans la console interactive](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Configuration de la console interactive](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Ouvrez le fichier correspondant à votre fichier de classe main, cliquez avec le bouton droit sur **Spark Console**, puis cliquez sur **Run Spark Livy Interactive Session Console(Scala)** (Exécuter Spark Livy Interactive Session Console(Scala)). Vous pouvez également accéder au menu **Tools** (Outils), puis cliquer sur **Spark Console**, puis **Run Spark Livy Interactive Session Console(Scala)** (Exécuter Spark Livy Interactive Session Console(Scala)) pour lancer la console.

3. Une fois que vous avez lancé la console, vous pouvez y effectuer l’action de votre choix. Par exemple, vous pouvez entrer **sc.appName**, puis appuyer sur Ctrl + Entrée pour afficher le résultat.

    ![Résultat dans la console interactive](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Envoyer la sélection à la console Spark
Il est pratique pour vous de prévoir le résultat du script en envoyant certains codes à la console locale ou à la console de session interactive Livy (Scala). Vous pouvez mettre en évidence certains codes dans le fichier Scala, puis cliquez avec le bouton droit sur **Send Selection To Spark Console** (Envoyer la sélection à la console Spark). Les codes sélectionnés sont envoyés à la console afin d’être traités. Le résultat s’affiche après les codes dans la console. La console vérifiera les erreurs s’il y en a. 

   ![Envoyer la sélection à la console Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Convertir des applications IntelliJ IDEA existantes pour qu’elles utilisent le kit de ressources Azure pour IntelliJ
Vous pouvez convertir les applications Spark Scala existantes que vous avez créées dans IntelliJ IDEA pour qu’elles soient compatibles avec le kit de ressources Azure pour IntelliJ. Vous pouvez ensuite utiliser le plug-in pour envoyer les applications à un cluster HDInsight Spark.

1. Pour une application Spark Scala existante créée à l’aide d’IntelliJ IDEA, ouvrez le fichier .iml associé.

1. Au niveau de la racine se trouve un élément **module** comme celui-ci :
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Ajoutez `UniqueKey="HDInsightTool"` à la fin de l’élément **module** , qui doit alors ressembler à ceci :
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Enregistrez les modifications. Votre application doit maintenant être compatible avec le kit de ressources Azure pour IntelliJ. Vous pouvez effectuer un test en cliquant avec le bouton droit sur le nom du projet dans l’Explorateur de projets. Le menu contextuel doit maintenant comporter l’option **Submit Spark Application to HDInsight**(Envoyer l’application Spark à HDInsight).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Erreur dans l’exécution locale : *Please use a larger heap size* (Veuillez utiliser un tas de plus grande taille)
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
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créez un projet Scala (vidéo) : [Créer des applications Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
