---
title: Créer des clusters Apache Hadoop à l’aide d’un navigateur web - Azure HDInsight
description: Découvrez comment créer des clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark sur Linux pour HDInsight avec un navigateur web et le portail Azure en préversion.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 7ff69d3b79662cff1fc28b0b59777225d0cc439f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189028"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Créer des clusters Linux dans HDInsight à l’aide du portail Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Le portail Azure est un outil web qui permet de gérer les services et les ressources hébergés dans le cloud Microsoft Azure. Dans cet article, vous allez apprendre à créer des clusters HDInsight Linux avec le portail.

## <a name="prerequisites"></a>Prérequis
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un navigateur Web moderne**. Le portail Azure utilise les langages HTML5 et Javascript. Il est possible qu’il ne fonctionne pas correctement sur les anciens navigateurs web.

## <a name="create-clusters"></a>Créer des clusters
Le portail Azure expose la plupart des propriétés du cluster. Avec le modèle Azure Resource Manager, vous pouvez masquer de nombreux détails. Pour plus d’informations, consultez [Création de clusters Hadoop Apache basés sur Linux dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **+ Créer une ressource**, **Analytique**, puis **HDInsight**.
   
    ![Création d’un nouveau cluster dans le portail Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "Création d’un nouveau cluster dans le portail Azure")

3. Dans le panneau **HDInsight**, cliquez sur **Personnalisé (taille, paramètres, applications)**, sur **Paramètres de base**, puis entrez les informations suivantes.

    ![Création d’un nouveau cluster dans le portail Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "Création d’un nouveau cluster dans le portail Azure")

    * Entrez un **nom de cluster** : Ce nom doit être globalement unique.

    * Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement Azure utilisé pour le cluster.

    * Cliquez sur **Type de cluster**, puis sélectionnez le type de cluster (Hadoop, Spark, etc.) à créer. Pour **Système d’exploitation**, cliquez sur **Linux**, puis sélectionnez une version. Utilisez la version par défaut si vous ne savez pas laquelle choisir. Pour plus d’informations, consultez [Versions de clusters HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]
        > Il existe différents types de clusters HDInsight, correspondant aux différentes charges de travail ou technologies pour lesquelles ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster. 
        > 
        > 
        
    * Pour **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster**, saisissez le nom d’utilisateur et le mot de passe (respectivement) de l’utilisateur administrateur.

    * Entrez un **Nom d’utilisateur SSH** et, si vous souhaitez que le mot de passe SSH soit le même que le mot de passe administrateur que vous avez spécifié précédemment, cochez la case **Utiliser le même mot de passe que pour la connexion au cluster**. Sinon, fournissez un **MOT DE PASSE** ou une **CLÉ PUBLIQUE**, qui seront utilisés pour authentifier l’utilisateur SSH. L’utilisation d’une clé publique est l’approche recommandée. Cliquez sur **Sélectionner** en bas de l’écran pour enregistrer la configuration des informations d’identification.
   
        Pour plus d’informations, consultez [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Pour **Groupe de ressources**, indiquez si vous souhaitez créer un groupe de ressources ou utiliser un groupe existant.

    * Spécifiez un **emplacement** de centre de données dans lequel le cluster doit être créé.

    * Cliquez sur **Suivant**.

4. Dans **Sécurité et mise en réseau**, vous pouvez connecter votre cluster à un réseau virtuel à l’aide de la liste déroulante fournie. Sélectionnez un réseau virtuel Azure et le sous-réseau si vous souhaitez placer le cluster dans un réseau virtuel. Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md). Si vous souhaitez utiliser le **Pack Sécurité Entreprise**, vous pouvez également suivre les instructions fournies ici : [Configurer un cluster HDInsight avec le pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

5. Pour **Stockage**, spécifiez si vous souhaitez utiliser le stockage Azure (WASB) ou Data Lake Storage comme stockage par défaut. Consultez le tableau ci-dessous pour plus d’informations.

     ![Création d’un nouveau cluster dans le portail Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "Création d’un nouveau cluster dans le portail Azure")

     | Stockage                                      | Description |
     |----------------------------------------------|-------------|
     | **Objets blob de stockage Azure comme stockage par défaut**   | <ul><li>Pour **Type de stockage principal**, sélectionnez **Stockage Azure**. Ensuite, pour **Méthode de sélection**, vous pouvez choisir **Mes abonnements** si vous souhaitez spécifier un compte de stockage qui fait partie de votre abonnement Azure, puis sélectionner le compte de stockage. Sinon, cliquez sur **Clé d’accès** et fournissez les informations du compte de stockage que vous souhaitez choisir en dehors de votre abonnement Azure.</li><li>Pour **Conteneur par défaut**, vous pouvez choisir d’utiliser le nom de conteneur par défaut suggéré par le portail ou spécifier le vôtre.</li><li>Si vous utilisez WASB comme stockage par défaut, vous pouvez éventuellement cliquer sur **Comptes de stockage supplémentaires** pour spécifier les comptes de stockage supplémentaires à associer au cluster. Pour **Clés de stockage Azure**, cliquez sur **Ajouter une clé de stockage**, puis fournissez un compte de stockage à partir de vos abonnements Azure ou d’autres abonnements (en fournissant la clé d’accès du compte de stockage).</li><li>Si vous utilisez WASB comme stockage par défaut, vous pouvez éventuellement cliquer sur **Accès à Data Lake Store** pour spécifier Azure Data Lake Storage comme stockage supplémentaire. Pour plus d’informations, consultez [Démarrage rapide : Configurer des clusters dans HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage en tant que stockage par défaut** | Pour **Type de stockage principal**, sélectionnez **Azure Data Lake Storage Gen1** ou **Azure Data Lake Storage Gen2 (préversion)**, puis consultez l’article [Démarrage rapide : Configurer des clusters dans HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) pour obtenir des instructions. |
     | **Metastores externes**                      | Spécifiez éventuellement une base de données SQL pour stocker les métadonnées Hive et Oozie associées au cluster. Pour **Sélectionner une base de données SQL pour Hive**, sélectionnez une base de données SQL, puis indiquez le nom d’utilisateur/mot de passe pour la base de données. Répétez ces étapes pour les métadonnées d’Oozie.<br><br>Des points à prendre en compte lors de l’utilisation d’une base de données Azure SQL pour les metastores. <ul><li>La base de données Azure SQL Database utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, dont Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.</li><li>Lors de la création d’un metastore, n’utilisez pas de nom de base de données contenant des traits d’union ou des tirets, car cela risque d’entraîner l’échec du processus de création du cluster.</li></ul> |

     Cliquez sur **Suivant**. 

     > [!WARNING]
     > L’utilisation d’un compte de stockage supplémentaire dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

6. Cliquez éventuellement sur **Applications** pour installer les applications qui fonctionnent avec les clusters HDInsight. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Pour plus d’informations, consultez [Installer des applications HDInsight](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Cliquez sur **Taille de cluster** pour afficher des informations sur les nœuds qui sont utilisés pour ce cluster. Définissez le nombre de nœuds de travail dont vous avez besoin pour le cluster. Le coût estimé de l’exécution du cluster est également affiché.
   
    ![Niveaux tarifaires du nœud](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "Spécifier le nombre de nœuds de cluster")
   
   > [!IMPORTANT]
   > Si vous envisagez d’utiliser plus de 32 nœuds de travail pendant la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
   > 
   > Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Cliquez sur **Suivant** pour enregistrer la configuration de tarification du nœud.

8. Dans **Actions de Script**, vous pouvez personnaliser un cluster de manière à installer des composants personnalisés.  Utilisez cette option si vous voulez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).
Cliquez sur **Suivant**.

9. Pour **Résumé**, vérifiez les informations que vous avez entrées précédemment, puis cliquez sur **Créer**.

     ![Niveaux tarifaires du nœud](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "Spécifier le nombre de nœuds de cluster")
    
    > [!NOTE]
    > La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus de provisionnement.
    > 
    > 
10. Une fois la création terminée, cliquez sur la vignette du cluster dans le tableau d’accueil. La fenêtre de cluster fournit les informations suivantes.
    
    ![Interface du cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "Propriétés du cluster")
    
    Utilisez ce qui suit pour comprendre les icônes affichées en haut.
    
    * L’onglet **Vue d’ensemble** fournit toutes les informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d’exploitation, l’URL vers le tableau de bord de cluster, etc.
    * Le **Tableau de bord** vous redirige vers le portail Ambari associé au cluster.
    * **Secure Shell** : informations nécessaires à l’accès au cluster à l’aide de SSH.
    * **Mise à l’échelle de cluster** vous permet d’augmenter et diminuer le nombre de nœuds de travail associés au cluster.
      * **Supprimer** : permet de supprimer le cluster HDInsight.
    

## <a name="customize-clusters"></a>Personnaliser des clusters
* Consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Supprimer le cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Étapes suivantes
Vous avez créé un cluster HDInsight. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop
* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utiliser Apache Hadoop MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters Apache HBase
* [Bien démarrer avec Apache HBase sur HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Développer des applications Java pour Apache HBase sur HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm
* [Développer des topologies Java pour Apache Storm sur HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utiliser des composants Python dans Apache Storm sur HDInsight](storm/apache-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Apache Storm sur HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters Apache Spark
* [Créer une application autonome avec Scala](spark/apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Apache Livy sur un cluster Apache Spark](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark avec BI : effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](spark/apache-spark-use-bi-tools.md)
* [Apache Spark avec Machine Learning : utiliser Spark dans HDInsight pour prédire les résultats de l’inspection d’aliments](spark/apache-spark-machine-learning-mllib-ipython.md)

