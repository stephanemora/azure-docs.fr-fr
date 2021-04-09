---
title: 'Azure Toolkit for Eclipse : Créer des applications Scala pour HDInsight Spark'
description: Utilisez HDInsight Tools dans le kit de ressources Azure pour Eclipse pour développer des applications Spark écrites en Scala et envoyez-les à un cluster HDInsight Spark, directement à partir de l’IDE Eclipse.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866571"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Utiliser Azure Toolkit for Eclipse afin de créer des applications Apache Spark pour un cluster HDInsight

Utilisez HDInsight Tools dans Azure Toolkit for [Eclipse](https://www.eclipse.org/) afin de développer des applications [Apache Spark](https://spark.apache.org/) écrites en [Scala](https://www.scala-lang.org/) et envoyez-les à un cluster Azure HDInsight Spark, directement à partir de l’environnement de développement intégré (IDE) Eclipse. Vous pouvez utiliser le plug-in HDInsight Tools de différentes manières :

* Pour développer une application Scala Spark et l’envoyer sur un cluster HDInsight Spark.
* Pour accéder à vos ressources de cluster Azure HDInsight Spark.
* Pour développer et exécuter une application Scala Spark localement.

## <a name="prerequisites"></a>Prérequis

* Cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de développeur Java (JDK), version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [IDE Eclipse](https://www.eclipse.org/downloads/). Cet article utilise l’environnement de développement intégré (IDE) Eclipse pour les développeurs Java.

## <a name="install-required-plug-ins"></a>Installer les plug-ins nécessaires

### <a name="install-azure-toolkit-for-eclipse"></a>Installer le Kit de ressources Azure pour Eclipse

Pour plus d’informations sur l’installation, voir [Installation du kit de ressources Azure pour Eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Installer le plug-in Scala

Quand vous ouvrez Eclipse, HDInsight Tools détecte automatiquement si vous avez installé le plug-in Scala. Sélectionnez **OK** pour continuer, puis suivez les instructions pour installer le plug-in à partir de la Place de marché Eclipse. Redémarrez l’IDE une fois l’installation terminée.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Installation automatique du plug-in Scala" border="true":::

### <a name="confirm-plug-ins"></a>Confirmer les plug-ins

1. Accédez à **Aide** > **Place de marché Eclipse**.

1. Sélectionnez l’onglet **Installé**.

1. Vous devez voir au moins :
    * Azure Toolkit for Eclipse \<version>.
    * IDE Scala \<version>.

## <a name="sign-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure :

1. Démarrez l’IDE Eclipse.

1. Accédez à **Fenêtre** >  **Afficher la vue** > **Autre** > **Se connecter**.

1. Dans la boîte de dialogue **Afficher la vue**, accédez à **Azure** > **Azure Explorer**, puis sélectionnez **Ouvrir**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Apache Spark Eclipse – Afficher la vue" border="true":::

1. Dans **Azure Explorer**, cliquez avec le bouton droit sur le nœud **Azure**, puis sélectionnez **Se connecter**.

1. Dans la boîte de dialogue **Connexion à Azure**, choisissez la méthode d’authentification, sélectionnez **Se connecter** et effectuez le processus de connexion.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark Eclipse – Connexion à Azure" border="true":::

1. Une fois que vous êtes connecté, la boîte de dialogue **Vos abonnements** liste tous les abonnements Azure associés aux informations d’identification. Appuyez sur **Sélectionner** pour fermer la boîte de dialogue.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Boîte de dialogue Sélectionner des abonnements" border="true":::

1. Dans **Azure Explorer**, accédez à **Azure** >  **HDInsight** pour afficher les clusters HDInsight Spark figurant dans votre abonnement.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure3" border="true":::

1. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="Développement d’un nom de cluster pour voir les ressources" border="true":::

## <a name="link-a-cluster"></a>Lier un cluster

Vous pouvez lier un cluster normal en utilisant le nom d’utilisateur Ambari managé. De même, pour un cluster HDInsight joint à un domaine, vous pouvez effectuer une liaison à l’aide du domaine et du nom d’utilisateur, par exemple `user1@contoso.com`.

1. Dans **Azure Explorer**, cliquez avec le bouton droit sur **HDInsight** et sélectionnez **Lier un cluster**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Explorateur Azure - Menu de la liaison de cluster" border="true":::

1. Entrez le **nom du cluster**, le **nom d’utilisateur** et le **mot de passe**, puis sélectionnez **OK**. Si vous le souhaitez, entrez un compte de stockage, une clé de stockage, puis sélectionnez un conteneur de stockage pour que l’explorateur de stockage fonctionne avec l’arborescence de gauche.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Boîte de dialogue Lier un cluster HDInsight" border="true":::

   > [!NOTE]  
   > Nous utilisons la clé de stockage liée, le nom d’utilisateur et le mot de passe si le cluster est à la fois connecté sur un abonnement Azure et lié à un cluster.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Comptes de stockage Azure Explorer" border="true":::
   >
   > Pour l’utilisateur clavier uniquement, lorsque le focus actuel se trouve sur **Clé de stockage**, vous devez utiliser **Ctrl+TAB** pour vous concentrer sur le champ suivant de la boîte de dialogue.

1. Vous pouvez voir le cluster lié sous **HDInsight**. Vous pouvez désormais soumettre une application à ce cluster lié.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Explorateur Azure - Cluster lié à HDI" border="true":::

1. Vous pouvez également dissocier un cluster à partir de **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer - Cluster dissocié" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configuration d’un projet Spark Scala pour un cluster HDInsight Spark

1. Dans l’espace de travail IDE Eclipse, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans l’Assistant **Nouveau projet**, sélectionnez **Projet HDInsight** > **Spark sur HDInsight (Scala)** . Sélectionnez ensuite **Suivant**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="Sélection du projet Spark sur HDInsight (Scala)" border="true":::

1. Dans la boîte de dialogue **New HDInsight Scala Project** (Nouveau projet HDInsight Scala), indiquez les valeurs suivantes, puis sélectionnez **Next** (Suivant) :
   * Entrez un nom pour le projet.
   * Dans la zone **JRE**, vérifiez que l’option **Use an execution environment JRE** (Utiliser un environnement d’exécution JRE) est définie sur **JavaSE-1.7** ou version ultérieure.
   * Dans la zone **Bibliothèque Spark**, vous pouvez choisir l’option **Utiliser Maven pour configurer le kit de développement logiciel (SDK) Spark**.  Notre outil intègre la version correcte pour les kits de développement logiciel (SDK) Spark et Scala. Vous pouvez aussi choisir l’option **Ajouter le kit SDK Spark manuellement**. Téléchargez et ajoutez le kit SDK Spark manuellement.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Boîte de dialogue New HDInsight Scala Project (Nouveau projet HDInsight Scala)" border="true":::

1. Dans la boîte de dialogue suivante, passez en revue les détails, puis sélectionnez **Terminer**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Créer une application Scala pour un cluster HDInsight Spark

1. Dans l’**Explorateur de package**, développez le projet que vous avez créé précédemment. Cliquez avec le bouton droit sur **src**, sélectionnez **Nouveau** > **Autre**.

1. Dans la boîte de dialogue **Sélectionner un Assistant**, sélectionnez **Assistants Scala** > **Objet Scala**. Sélectionnez ensuite **Suivant**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Sélection d’un assistant pour créer un objet Scala" border="true":::

1. Dans la boîte de dialogue **Create New File** (Créer un fichier), entrez un nom pour l’objet, puis sélectionnez **Finish** (Terminer). Un éditeur de texte s’ouvre.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Assistant Nouveau fichier - Créer un fichier" border="true":::

1. Dans l’éditeur de texte, remplacez le contenu actuel par le code ci-dessous :

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Exécutez l’application sur un cluster HDInsight Spark :

   a. Dans Package Explorer (Explorateur de packages), cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).

   b. Dans la boîte de dialogue **Spark Submission** (Envoi Spark), entrez les valeurs suivantes, puis sélectionnez **Submit** (Envoyer) :

   * Pour **Cluster Name**(Nom du cluster), sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.
   * Sélectionnez un artefact à partir du projet Eclipse ou choisissez-en un sur un disque dur. La valeur par défaut dépend de l’élément sur lequel vous cliquez avec le bouton droit dans l’Explorateur de package.
   * Dans la liste déroulante **Main class name** (Nom de la classe principale), l’Assistant Envoi affiche tous les noms des objets de votre projet. Sélectionnez-en un ou entrez-en un que vous souhaitez exécuter. Si vous avez sélectionné un artefact à partir d’un disque dur, vous devez entrer le nom de la classe principale manuellement. 
   * Comme le code d’application dans cet exemple n’exige aucun argument de ligne de commande et qu’il ne référence pas de fichiers JAR ou d’autres fichiers, vous pouvez laisser les autres zones de texte vides.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Boîte de dialogue Apache Spark Submission (Envoi Spark)" border="true":::

1. L’onglet **Spark Submission** (Envoi Spark) doit commencer à afficher la progression. Vous pouvez arrêter l’application en sélectionnant le bouton rouge dans la fenêtre **Spark Submission** (Envoi Spark). Vous pouvez également afficher les journaux d’activité pour cette exécution d’application spécifique en sélectionnant l’icône en forme de globe (indiquée par la zone bleue dans l’image).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="Fenêtre Apache Spark Submission (Envoi Spark)" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Accéder aux clusters HDInsight Spark et les gérer à l’aide de HDInsight Tools dans le kit de ressources Azure pour Eclipse

Vous pouvez effectuer diverses opérations à l’aide de HDInsight Tools, y compris en accédant à la sortie du travail.

### <a name="access-the-job-view"></a>Accéder à la vue des travaux

1. Dans **Azure Explorer**, développez **HDInsight** puis le nom du cluster Spark, puis sélectionnez **Travaux**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Nœud de la vue des travaux Eclipse dans Azure Explorer" border="true":::

1. Sélectionnez le nœud **Travaux**. Si une version Java est antérieure à la version **1.8**, HDInsight Tools vous rappelle automatiquement d’installer le plug-in **E(fx)clipse**. Sélectionnez **OK** pour continuer, puis suivez l’assistant d’installation pour l’installer depuis la Place de marché Eclipse et redémarrer Eclipse.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="Installation du plug-in E(fx)clipse manquant" border="true":::

1. Ouvrez la vue des travaux à partir du nœud **Tâches**. Dans le volet droit, l’onglet **Spark Job View** (Affichage des travaux Spark) affiche toutes les applications qui ont été exécutées sur le cluster. Sélectionnez le nom de l’application pour laquelle vous souhaitez afficher plus de détails.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Détails des journaux des travaux dans la vue Apache Eclipse" border="true":::

   Vous pouvez alors effectuer une des actions suivantes :

   * Placez le curseur sur le graphe du travail. Il affiche des informations de base sur le travail en cours d’exécution. Sélectionnez le graphe de travail ; vous pouvez voir alors les étapes et les informations générées par chaque travail.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Informations sur les étapes du graphe de travail Apache Spark" border="true":::

   * Sélectionnez l’onglet **Journal** pour afficher les journaux d’activité fréquemment utilisés, notamment les journaux d’activité **Driver Stderr**, **Driver Stdout** et **Directory Info**.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Informations du journal des travaux Apache Spark Eclipse" border="true":::

   * Ouvrez l’interface utilisateur de l’historique Spark et l’interface utilisateur Apache Hadoop YARN (au niveau de l’application) en sélectionnant les liens hypertexte en haut de la fenêtre.

### <a name="access-the-storage-container-for-the-cluster"></a>Accéder au conteneur de stockage du cluster

1. Dans l’Explorateur Azure, développez le nœud racine **HDInsight** pour afficher la liste des clusters HDInsight Spark disponibles.

1. Développez le nom de cluster pour voir le compte de stockage et le conteneur de stockage par défaut du cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Compte de stockage et conteneur de stockage par défaut" border="true":::

1. Sélectionnez le nom du conteneur de stockage associé au cluster. Dans le volet droit, double-cliquez sur le dossier **HVACOut**. Ouvrez l’un des fichiers **part-** pour afficher la sortie de l’application.

### <a name="access-the-spark-history-server"></a>Accéder au serveur d’historique Spark

1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nom de votre cluster Spark, puis sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous les avez spécifiées au moment de l’approvisionnement du cluster.

1. Dans le tableau de bord du serveur d’historique Spark, utilisez le nom de l’application pour rechercher l’application que vous venez d’exécuter. Dans le code précédent, vous définissez le nom de l’application en utilisant `val conf = new SparkConf().setAppName("MyClusterApp")`. Le nom de votre application Spark était donc **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Démarrer le portail Apache Ambari

1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nom de votre cluster Spark, puis sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters [Ambari]).

1. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous les avez spécifiées au moment de l’approvisionnement du cluster.

### <a name="manage-azure-subscriptions"></a>Gérer les abonnements Azure

Par défaut, HDInsight Tools du kit de ressources Azure pour Eclipse répertorie les clusters Spark de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements pour lesquels vous souhaitez accéder au cluster.

1. Dans Azure Explorer, cliquez avec le bouton droit sur le nœud racine **Azure**, puis sélectionnez **Gérer les abonnements**.

1. Dans la boîte de dialogue, décochez les cases concernant l’abonnement auquel vous ne souhaitez pas accéder, puis sélectionnez **Fermer**. Vous pouvez également sélectionner **Se déconnecter** si vous souhaitez vous déconnecter de votre abonnement Azure.

## <a name="run-a-spark-scala-application-locally"></a>Exécuter une application Spark Scala localement

Vous pouvez utiliser HDInsight Tools du kit de ressources Azure pour Eclipse pour exécuter des applications Spark Scala localement sur votre poste de travail. En règle générale, ces applications n’ont pas besoin d’accéder aux ressources de cluster telles que le conteneur de stockage, et elles peuvent être exécutées et testées localement.

### <a name="prerequisite"></a>Configuration requise

Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Cette exception est liée à l’absence du fichier **WinUtils.exe** dans Windows.

Pour résoudre cette erreur, vous devez disposer de [Winutils.exe](https://github.com/steveloughran/winutils) dans un emplacement tel que **C:\WinUtils\bin**, puis ajouter la variable d’environnement **HADOOP_HOME** et définir sa valeur sur **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Exécuter une application Spark Scala locale

1. Démarrez Eclipse et créez un projet. Dans la boîte de dialogue **New Project** (Nouveau projet), choisissez les options suivantes, puis sélectionnez **Next** (Suivant).

1. Dans l’Assistant **Nouveau projet**, sélectionnez **Projet HDInsight** > **Spark on HDInsight Local Run Sample (Scala)** (Exemple d’exécution locale de Spark sur HDInsight [Scala]). Sélectionnez ensuite **Suivant**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Le nouveau projet sélectionne une boîte de dialogue d’assistant" border="true":::

1. Pour fournir les détails du projet, suivez les étapes 3 à 6 indiquées dans la section précédente [Configuration d’un projet Spark Scala pour un cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Le modèle ajoute un exemple de code (**LogQuery**) sous le dossier **src** que vous pouvez exécuter localement sur votre ordinateur.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="Emplacement de l’application Scala locale LogQuery" border="true":::

1. Cliquez avec le bouton droit sur **LogQuery.scala** et sélectionnez **Exécuter en tant que** > **1 Application Scala**. Un résultat ressemblant à ce qui suit apparaît sous l’onglet **Console** :

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Résultat de l’exécution locale de l’application Spark" border="true":::

## <a name="reader-only-role"></a>Rôle Lecteur seul

Lorsque les utilisateurs envoient du travail à un cluster avec l’autorisation de rôle Lecteur seul, les informations d’identification Ambari sont obligatoires.

### <a name="link-cluster-from-context-menu"></a>Lier le cluster à partir du menu contextuel

1. Connectez-vous avec un compte membre du rôle Lecteur seul.

2. Dans **Azure Explorer**, développez **HDInsight** pour voir les clusters HDInsight de votre abonnement. Les clusters signalés par **"Role:Reader"** ont uniquement l’autorisation du rôle Lecteur seul.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - lecteur de rôle" border="true":::

3. Cliquez avec le bouton droit de la souris sur le cluster avec l’autorisation de rôle Lecteur seul. Sélectionnez **Link this cluster** dans le menu contextuel pour lier le cluster. Entrez le nom d’utilisateur et le mot de passe Ambari.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - lien" border="true":::

4. Si le cluster est correctement lié, HDInsight est actualisé.
   L’étape du cluster sera reliée.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - lié" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Lier le cluster en développant le nœud Jobs (Travaux)

1. Cliquez sur le nœud **Jobs** (Travaux). La fenêtre contextuelle **Cluster Job Access Denied** (Accès refusé au travail du cluster) s’ouvre alors.

2. Cliquez sur **Link this cluster** pour lier le cluster.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Lier un cluster à partir de la fenêtre d’envoi de Spark

1. Créez un projet HDInsight.

2. Cliquez avec le bouton droit sur le package. Sélectionnez ensuite **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - envoi" border="true":::

3. Sélectionnez un cluster doté de l’autorisation de rôle Lecteur seul pour **Nom du cluster**. Un message d’avertissement s’affiche. Cliquez sur **Link this cluster** pour lier le cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - lier cet élément" border="true":::

### <a name="view-storage-accounts"></a>Afficher les comptes de stockage

* Pour les clusters possédant l’autorisation de rôle Lecteur seul, cliquez sur le nœud **Storage Accounts** (Comptes de stockage). La fenêtre contextuelle **Storage Access Denied** (Accès au stockage refusé) s’ouvre alors.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - stockage" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - refusé" border="true":::

* Pour les clusters liés, cliquez sur le nœud **Storage Accounts**. La fenêtre contextuelle **Storage Access Denied** s’ouvre alors.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="Clusters HDInsight Spark dans l’Explorateur Azure - refusé2" border="true":::

## <a name="known-problems"></a>Problèmes connus

Lorsque vous utilisez **Lier un cluster**, je vous conseille de fournir des informations d’identification de stockage.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="Lier un cluster avec des informations d’identification de stockage Eclipse" border="true":::

Il existe deux modes pour soumettre les travaux. Si les informations d’identification de stockage sont fournies, le mode batch sera utilisé pour soumettre le travail. Sinon, le mode interactif sera utilisé. Si le cluster est occupé, l’erreur ci-dessous risque de se produire.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="eclipse get error when cluster busy" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="eclipse get error when cluster busy yarn" border="true":::

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Apache Spark avec BI : effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : Utiliser Spark dans HDInsight pour analyser la température d’un bâtiment à l’aide de données issues des systèmes de chauffage, de ventilation et de climatisation](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection d’aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide d’Apache Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Création et exécution d’applications

* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Utiliser le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par VPN](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser Azure Toolkit for IntelliJ pour déboguer des applications Apache Spark à distance par SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour bloc-notes Jupyter dans un cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec des blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](apache-spark-job-debugging.md)