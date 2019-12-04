---
title: Créer des clusters Apache Hadoop à l’aide d’un navigateur web, Azure HDInsight
description: Découvrez comment créer des clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark sur Linux pour HDInsight à l’aide d’un navigateur web et du portail Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215908"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Créer des clusters Linux dans HDInsight à l’aide du portail Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Le portail Azure est un outil web qui permet de gérer les services et les ressources hébergés dans le cloud Microsoft Azure. Cet article vous montre comment créer des clusters Azure HDInsight Linux par le biais du portail.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Prérequis

* **Un abonnement Azure**. Regardez la vidéo qui explique [comment obtenir un essai gratuit d’Azure pour tester Hadoop dans HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un navigateur Web moderne**. Le portail Azure utilise les langages HTML5 et JavaScript. Il est possible qu’il ne fonctionne pas correctement sur les anciens navigateurs web.

## <a name="create-clusters"></a>Créer des clusters

Le portail Azure expose la plupart des propriétés du cluster. Avec les modèles Azure Resource Manager, vous pouvez masquer de nombreux détails. Pour plus d’informations, consultez [Créer des clusters Apache Hadoop dans HDInsight avec des modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. À partir du menu gauche, accédez à **+ Créer une ressource** >  **Analytique** > **Azure HDInsight**.

    ![Créer un cluster dans le portail Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Création d’un cluster dans le portail Azure")

1. À partir de la page **Créer un cluster HDInsight**, sélectionnez **Accéder à l’expérience de création classique**.

    ![Accéder à l’expérience de création classique](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Dans la page **HDInsight**, sélectionnez **Personnalisé (taille, paramètres, applications)** .

1. Sélectionnez **1 De base**. Entrez ensuite les informations suivantes :

    |Propriété |Description |
    |---|---|
    |Nom du cluster|Ce nom doit être globalement unique.|
    |Subscription|Dans la liste déroulante, sélectionnez l’abonnement Azure utilisé pour le cluster.|
    |Type de cluster|Sélectionnez le type de cluster que vous souhaitez créer. (par exemple, Hadoop ou Apache Spark). Le **Système d’exploitation** sera **Linux**. Ensuite, sélectionnez une version de type de cluster. Utilisez la version par défaut si vous ne savez pas laquelle choisir. Pour plus d’informations, consultez [Versions de clusters HDInsight](hdinsight-component-versioning.md).|
    |Nom d’utilisateur de connexion au cluster|Indiquez le nom d’utilisateur, la valeur par défaut est **Administrateur**.|
    |Mot de passe de connexion au cluster|Indiquez le mot de passe.|
    |Nom d’utilisateur SSH (Secure Shell)|La valeur par défaut est **sshuser**. Si vous souhaitez utiliser comme mot de passe SSH le mot de passe administrateur que vous avez spécifié précédemment, cochez la case **Utiliser le mot de passe de connexion au cluster pour SSH**. Sinon, fournissez un **MOT DE PASSE** ou une **CLÉ PUBLIQUE** pour l’authentification de l’utilisateur SSH. Nous recommandons d’utiliser une clé publique. Choisissez **Sélectionner** en bas de l’écran pour enregistrer la configuration des informations d’identification.  Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Resource group|Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant.|
    |Location|Spécifiez un centre de données où sera créé le cluster.|

    ![HDInsight - Créer un cluster - Informations de base](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Création d’un cluster dans le portail Azure")

    > [!IMPORTANT]  
    > Il existe différents types de clusters HDInsight, correspondant à la charge de travail ou à la technologie pour laquelle ils sont conçus. Il n’y a pas de méthode prise en charge pour créer un cluster combinant plusieurs types, comme Storm et HBase sur un même cluster.

    Cliquez sur **Suivant** pour passer à la page suivante.

1. Dans **2 Sécurité + réseau**, vous pouvez connecter votre cluster à un réseau virtuel à partir du menu déroulant affiché. Sélectionnez un réseau virtuel Azure et le sous-réseau si vous souhaitez placer le cluster dans un réseau virtuel. Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, consultez [Planifier un déploiement de réseau virtuel pour des clusters Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). L’article indique la configuration spécifique requise pour le réseau virtuel.

    Si vous souhaitez utiliser le **Pack Sécurité Entreprise**, suivez les instructions fournies dans [Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Cliquez sur **Suivant** pour passer à la page suivante.

1. Dans **3 Stockage**, pour **Paramètres du compte de stockage**, spécifiez si vous voulez utiliser Stockage Azure ou Azure Data Lake Storage comme stockage par défaut. Pour plus d’informations, consultez le tableau suivant.

    | Type de stockage principal | Description |
    |------------------|-------------|
    | Stockage Azure   |  * Pour **Méthode de sélection**, choisissez **Mes abonnements** si vous voulez utiliser un compte de stockage qui fait partie de votre abonnement Azure. Sélectionnez ensuite le compte de stockage. Sinon, sélectionnez **Clé d’accès**. Entrez les informations du compte de stockage que vous souhaitez choisir en dehors de votre abonnement Azure.</br></br> * Pour **Conteneur par défaut**, choisissez le nom de conteneur par défaut suggéré par le portail ou spécifiez celui de votre choix.</br></br> * Si votre stockage par défaut est Stockage Blob Azure, vous pouvez aussi sélectionner **Comptes de stockage supplémentaires** pour spécifier des comptes de stockage supplémentaires à associer au cluster. Pour **Clés de stockage Azure**, sélectionnez **Ajouter une clé de stockage**. Vous pouvez ensuite spécifier un compte de stockage de vos abonnements Azure ou d’autres abonnements. Indiquez la clé d’accès au compte de stockage.</br></br> * Si votre stockage par défaut est Stockage Blob Azure, vous pouvez aussi sélectionner **Accès à Data Lake Storage** pour spécifier Azure Data Lake Storage comme stockage supplémentaire. Pour plus d’informations, consultez [Démarrage rapide : Configurer des clusters dans HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Azure Data Lake Storage | Sélectionnez **Azure Data Lake Storage Gen1** ou **Azure Data Lake Storage Gen2**. Consultez ensuite l’article [Démarrage rapide : Configurer des clusters dans HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) pour obtenir des instructions. |

    **Paramètres du métastore (facultatif)**

    Spécifiez éventuellement une base de données SQL dans laquelle stocker les métadonnées Apache Hive et Apache Oozie associées au cluster. Pour **Sélectionner une base de données SQL pour Hive**, sélectionnez une base de données SQL. Indiquez ensuite le nom d’utilisateur et le mot de passe pour la base de données. Répétez ces étapes pour les métadonnées d’Oozie.

    Voici quelques points à prendre en compte sur l’utilisation d’une base de données Azure SQL Database pour les metastores :
    * La base de données Azure SQL Database utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, dont Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL Database, sélectionnez le nom du serveur. Ce serveur est celui sur lequel s’exécute l’instance SQL Database. Quand vous êtes revenu à l’écran du serveur, sélectionnez **Configurer**. Pour **Services Azure**, sélectionnez **Oui**. Ensuite, sélectionnez **Enregistrer**.
    * Quand vous créez un metastore, choisissez un nom de base de données sans tiret ni trait d’union, car la présence de ces caractères peut faire échouer le processus de création du cluster.

    ![HDInsight - Créer un cluster - Stockage](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Création d’un cluster dans le portail Azure")

    > [!WARNING]  
    > L’utilisation d’un compte de stockage supplémentaire à un autre emplacement que le cluster HDInsight n’est pas prise en charge.

    Cliquez sur **Suivant** pour passer à la page suivante.

1. Dans **4 Applications (facultatif)** , sélectionnez les applications souhaitées. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Pour plus d’informations, consultez [Installer des applications lors de la création du cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Cliquez sur **Suivant** pour passer à la page suivante.

1. **5 Taille de cluster** affiche des informations sur les nœuds qui sont utilisés pour ce cluster. Définissez le nombre de nœuds de travail dont vous avez besoin pour le cluster. Le coût estimé de l’exécution du cluster est également affiché.

    ![HDInsight - Créer un cluster - Nœuds](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Spécifier le nombre de nœuds de cluster")

   > [!IMPORTANT]  
   > Si vous envisagez d’utiliser plus de 32 nœuds worker, sélectionnez une taille de nœud principal avec au moins huit cœurs et 14 Go de RAM. Planifiez la taille des nœuds lors de la phase de création du cluster ou adaptez leur taille après avoir créé le cluster.
   >
   > Pour plus d’informations sur les tailles de nœuds et les coûts associés, consultez [Tarification d’Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Cliquez sur **Suivant** pour passer à la page suivante.

1. Dans **6 Actions de Script**, vous pouvez personnaliser un cluster de manière à installer des composants personnalisés. Cette option est utile si vous voulez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).

   Cliquez sur **Suivant** pour passer à la page suivante.

1. Dans **7 Résumé**, vérifiez toutes les informations que vous avez entrées précédemment. Sélectionnez ensuite **Créer**.

    ![HDInsight - Créer un cluster - Résumé](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Spécifier le nombre de nœuds de cluster")

    > [!NOTE]  
    > La création du cluster prend un certain temps (en règle générale, environ 20 minutes). Surveillez les **Notifications** pour contrôler le processus de provisionnement.

1. Quand vous avez terminé le processus de création, sélectionnez **Accéder à la ressource** dans la notification **Le déploiement a été effectué**. La fenêtre de cluster fournit les informations suivantes.

    ![HDI - Vue d’ensemble des clusters dans le portail Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriétés du cluster")

    Voici une description de quelques icônes présentes dans la fenêtre :

    |Propriété | Description |
    |---|---|
    |Vue d'ensemble|Fournit toutes les informations essentielles sur le cluster. son nom, le groupe de ressources auquel il appartient, son emplacement, le système d’exploitation utilisé, l’URL d’accès au tableau de bord du cluster, etc.|
    |Tableaux de bord des clusters|Vous redirige vers le portail Ambari associé au cluster.|
    |SSH + Connexion du cluster|Fournit les informations nécessaires pour accéder au cluster à l’aide de SSH.|
    |Supprimer|permet de supprimer le cluster HDInsight.|

## <a name="customize-clusters"></a>Personnaliser des clusters

* [Personnaliser des clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Supprimer le cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un cluster HDInsight. Apprenez maintenant à l’utiliser.

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation de MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters Apache HBase

* [Bien démarrer avec Apache HBase sur HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Développer des applications Java pour Apache HBase sur HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm

* [Développer des topologies Java pour Apache Storm sur HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utiliser des composants Python dans Apache Storm sur HDInsight](storm/apache-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Apache Storm sur HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters Apache Spark

* [Créer une application autonome à l’aide de Scala](spark/apache-spark-create-standalone-application.md)
* [Exécuter des travaux à distance sur un cluster Apache Spark à l’aide d’Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark avec BI : Effectuer une analyse interactive des données à l’aide de Spark dans HDInsight avec les outils décisionnels](spark/apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection d’aliments](spark/apache-spark-machine-learning-mllib-ipython.md)
