---
title: Prise en main de ML Services sur HDInsight - Azure
description: Apprenez à créer un Apache Spark sur un cluster HDInsight incluant ML Services, puis à envoyer un script R sur le cluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 474048bc9f31e4630110ea099f3e0b375ad74e51
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087582"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Démarrer avec ML Services sur Azure HDInsight

Azure HDInsight vous permet de créer un cluster ML Services. Cette option permet aux scripts R d’utiliser [Apache Spark](https://spark.apache.org/) et [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) afin d’exécuter des calculs distribués. Dans cet article, vous allez apprendre à créer un cluster ML Service sur HDInsight, puis à exécuter un script R illustrant l’utilisation de Spark pour effectuer des calculs R distribués.

## <a name="prerequisites"></a>Conditions préalables

* **Un abonnement Azure** : Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations, consultez l’article [Obtenir une version d’évaluation gratuite de Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Créer le cluster à l’aide du portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Accédez à **+ Créer une ressource** > **Analytique** > **HDInsight**.

3. À partir du panneau **Informations de base**, entrez les informations suivantes :

    * **Nom du cluster** : nom du cluster HDInsight.
    * **Abonnement**: Sélectionnez l’abonnement à utiliser.
    * **Nom d’utilisateur de connexion au cluster** et **Mot de passe de connexion au cluster** : les informations de connexion permettant d’accéder au cluster via HTTPS. Vous utilisez ces informations d’identification pour accéder aux services tels que l’interface utilisateur web Apache Ambari ou l’API REST.
    * **Nom d’utilisateur Secure Shell (SSH)** : Information de connexion permettant d’accéder au cluster suivant le protocole SSH. Par défaut, le mot de passe est le même que le mot de passe de connexion de cluster.
    * **Groupe de ressources** : Groupe de ressources dans lequel le cluster sera créé.
    * **Emplacement** : Région Azure dans laquelle le cluster sera créé.

        ![Détails de base du cluster](./media/r-server-get-started/clustername.png)

4. Sélectionnez le **Type de cluster**, puis définissez les valeurs suivantes dans la section **Configuration du cluster** :

    * **Type du cluster** : ML Services

    * **Système d’exploitation** : Linux

    * **Version** : ML Server 9.3 (HDI 3.6). Les notes de publication de ML Server9.3 sont disponibles sur [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **R Studio Community Edition pour ML Server** : cet IDE basé sur navigateur est installé par défaut sur le nœud de périphérie. Si vous préférez ne pas l’installer, décochez la case. Si vous choisissez de l’installer, l’URL d’accès à la connexion à RStudio Server se trouve sur le panneau d’une application du portail de votre cluster une fois qu’il a été créé.

        ![Détails de base du cluster](./media/r-server-get-started/clustertypeconfig.png)

4. Après avoir sélectionné le type de cluster, utilisez le bouton __Sélectionner__ pour définir le type de cluster. Ensuite, utilisez le bouton __Suivant__ bouton pour terminer la configuration de base.

5. À partir de la section **Stockage**, sélectionnez ou créez un compte de stockage. Pour les étapes de ce document, laissez les autres champs de cette section sur leurs valeurs par défaut. Utilisez le bouton __Suivant__ pour enregistrer la configuration de stockage.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Dans la section **Résumé**, passez en revue la configuration du cluster. Utilisez les liens __Modifier__ pour modifier les éventuels paramètres incorrects. Pour finir, cliquez sur le bouton __Créer__ pour créer le cluster.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > La création du cluster peut prendre jusqu’à 20 minutes.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Se connecter à RStudio Server

Si vous avez choisi d’installer RStudio Server Community Edition pour votre cluster HDInsight, vous pouvez accéder à la connexion RStudio à l’aide d’une des deux méthodes suivantes :

* **Option 1** : accédez à l’URL suivante (où **CLUSTERNAME** est le nom du cluster ML Services que vous avez créé) :

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Option 2** - Utiliser le portail Azure.
  À partir du portail :
  1. Dans le menu de gauche, sélectionnez **Tous les services**.
  2. Sous **ANALYTICS**, sélectionnez **Clusters HDInsight**.
  3. Sélectionnez le nom de votre cluster dans la page **Clusters HDInsight**.
  4. À partir de **Tableaux de bord ML Services**, sélectionnez **Serveur R Studio**. 

     ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

     > [!IMPORTANT]  
     > Quelle que soit la méthode que vous utilisez, vous devez vous authentifier deux fois lorsque vous ouvrez une session pour la première fois.  Pour la première authentification, fournissez *l’ID d’utilisateur administrateur du cluster* et le *mot de passe*. Pour la deuxième authentification, fournissez *l’ID d’utilisateur SSH* et le *mot de passe*. Ouvertures de session suivantes nécessitent uniquement les informations d’identification SSH.

Une fois que vous êtes connecté, votre écran doit ressembler à la capture d’écran suivante :

![Se connecter à RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Exécuter un exemple de travail

Vous pouvez soumettre un travail à l’aide des fonctions ScaleR. Voici un exemple des commandes utilisées pour exécuter un travail :

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Se connecter au nœud de périphérie du cluster

Dans cette section, vous allez apprendre à vous connecter au nœud de périphérie d’un cluster HDInsight ML Services à l’aide de SSH. Pour plus d’informations sur l’utilisation de SSH, consultez [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

La commande SSH pour se connecter au nœud de périphérie du cluster ML Services est :

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Pour trouver la commande SSH pour votre cluster, dans le portail Azure, cliquez sur le nom du cluster, puis sur **SSH + Connexion du cluster**, et pour **Nom d’hôte**, sélectionnez le nœud de périphérie. Cela a pour effet d’afficher les informations relatives au point de terminaison SSH pour le nœud de périmètre.

![Image du point de terminaison SSH pour le nœud de périmètre](./media/r-server-get-started/sshendpoint.png)

Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple : 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Une fois connecté, une invite semblable à la suivante s’affiche :

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Utiliser la console R

1. Dans la session SSH, utilisez la commande suivante pour démarrer la console R :  

        R

2. Vous devez voir une sortie avec la version de ML Server, en plus d’autres informations.
    
3. Vous pouvez entrer un code R dans l’invite `>` . ML Services sur HDInsight inclut des packages qui vous permettent d’interagir facilement avec Hadoop et d’exécuter des calculs distribués. Par exemple, utilisez la commande suivante pour afficher la racine du système de fichiers par défaut du cluster HDInsight :

        rxHadoopListFiles("/")

4. Vous pouvez également utiliser l’adressage de style WASB.

        rxHadoopListFiles("wasb:///")

5. Pour quitter la console R, utilisez la commande suivante :

        quit()

## <a name="automated-cluster-creation"></a>Création de cluster automatisée

Vous pouvez automatiser la création d’un cluster ML Services pour HDInsight à l’aide du Kit de développement logiciel (SDK) et de PowerShell.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Pour créer un cluster ML Services à l’aide du Kit de développement logiciel (SDK) .NET, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Pour créer un cluster ML Services à l’aide de PowerShell, consultez l’article [Créer des clusters HDInsight à l’aide d’Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Supprimer le cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un cluster ML Services dans Azure HDInsight, et découvert les principes fondamentaux de l’utilisation de la console R à partir d’une session SSH. Les articles suivants expliquent les autres méthodes de gestion et d’utilisation de ML Services sur HDInsight :

* [Envoyer des travaux depuis Outils R pour Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Gérer un cluster ML Services sur HDInsight](r-server-hdinsight-manage.md)
* [Rendre opérationnel un cluster ML Services sur HDInsight](r-server-operationalize.md)
* [Options de contexte de calcul pour un cluster ML Services sur HDInsight](r-server-compute-contexts.md)
* [Options de stockage Azure pour un cluster ML Services sur HDInsight](r-server-storage.md)
