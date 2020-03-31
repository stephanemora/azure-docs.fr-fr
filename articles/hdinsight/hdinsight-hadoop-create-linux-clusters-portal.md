---
title: Créer des clusters Apache Hadoop à l’aide d’un navigateur web, Azure HDInsight
description: Découvrez comment créer des clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark sur Linux pour HDInsight à l’aide d’un navigateur web et du portail Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623263"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Créer des clusters Linux dans HDInsight à l’aide du portail Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Le portail Azure est un outil web qui permet de gérer les services et les ressources hébergés dans le cloud Microsoft Azure. Cet article vous montre comment créer des clusters Azure HDInsight Linux par le biais du portail. Des informations supplémentaires sont disponibles dans [Créer des clusters HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le portail Azure expose la plupart des propriétés du cluster. Avec les modèles Azure Resource Manager, vous pouvez masquer de nombreux détails. Pour plus d’informations, consultez [Créer des clusters Apache Hadoop dans HDInsight avec des modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-clusters"></a>Créer des clusters

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du haut, sélectionnez **+ Créer une ressource**.

    ![Créer un cluster dans le portail Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Création d’un cluster dans le portail Azure")

1. Sélectionnez **Analyse** > **Azure HDInsight** pour accéder à la page **Créer un cluster HDInsight**.

## <a name="basics"></a>Concepts de base

![HDInsight - Créer un cluster - Informations de base](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Création d’un cluster dans le portail Azure")

Sous l’onglet **De base**, fournissez les informations suivantes :

|Propriété |Description |
|---|---|
|Abonnement|Dans la liste déroulante, sélectionnez l’abonnement Azure utilisé pour le cluster.|
|Resource group|Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
|Nom du cluster|Entrez un nom globalement unique.|
|Région|Dans la liste déroulante, sélectionnez une région dans laquelle le cluster est créé.|
|Type de cluster|Choisissez **Sélectionner un type de cluster** pour ouvrir une liste. Dans la liste, sélectionnez le type de cluster souhaité. Il existe différents types de clusters HDInsight, correspondant à la charge de travail ou à la technologie pour laquelle ils sont conçus. Il n’y a pas de méthode prise en charge pour créer un cluster combinant plusieurs types,|
|Version|Dans la liste déroulante, sélectionnez une **version**. Utilisez la version par défaut si vous ne savez pas laquelle choisir. Pour plus d’informations, consultez [Versions de clusters HDInsight](hdinsight-component-versioning.md).|
|Nom d’utilisateur de connexion au cluster|Indiquez le nom d’utilisateur, la valeur par défaut est **Administrateur**.|
|Mot de passe de connexion au cluster|Indiquez le mot de passe.|
|Confirmer le mot de passe de connexion du cluster|Entrez de nouveau le mot de passe|
|Nom d’utilisateur SSH (Secure Shell)|Indiquez le nom d’utilisateur. La valeur par défaut est **sshuser**|
|Utiliser le mot de passe de connexion au cluster pour SSH|Si vous souhaitez utiliser comme mot de passe SSH le mot de passe administrateur que vous avez spécifié précédemment, cochez la case **Utiliser le mot de passe de connexion au cluster pour SSH**. Sinon, fournissez un **MOT DE PASSE** ou une **CLÉ PUBLIQUE** pour l’authentification de l’utilisateur SSH. Nous recommandons d’utiliser une clé publique. Choisissez **Sélectionner** en bas de l’écran pour enregistrer la configuration des informations d’identification.  Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Sélectionnez **Suivant : Stockage >>** pour passer à l’onglet suivant.

## <a name="storage"></a>Stockage

![HDInsight - Créer un cluster - Stockage](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Création d’un cluster dans le portail Azure - Stockage")

### <a name="primary-storage"></a>Stockage principal

Dans la liste déroulante pour **Type de stockage principal**, sélectionnez votre type de stockage par défaut. Les champs suivants à renseigner varient en fonction de votre sélection. Pour **Stockage Azure** :

1. Pour **Méthode de sélection**, choisissez **Sélectionner dans la liste**ou **Utiliser une clé d’accès**.
    * Pour **Sélectionner dans la liste**, sélectionnez **Compte de stockage principal** dans la liste déroulante ou sélectionnez **Créer**.
    * Pour **Utiliser une clé d’accès**, entrez votre **Nom du compte de stockage**. Fournissez ensuite la **Clé d’accès**.

1. Pour **Conteneur**, acceptez la valeur par défaut ou entrez-en une nouvelle.

### <a name="additional-azure-storage"></a>Stockage Azure supplémentaire

Facultatif : Sélectionnez **Ajouter un stockage Azure** pour le stockage du cluster supplémentaire. L’utilisation d’un compte de stockage supplémentaire dans une région différente de celle du cluster HDInsight n’est pas prise en charge.

### <a name="metastore-settings"></a>Paramètres du metastore

Facultatif : Spécifiez une base de données SQL existante pour enregistrer les métadonnées Apache Hive, Apache Oozie et/ou Apache Ambari hors du cluster. La base de données Azure SQL utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, dont Azure HDInsight. Quand vous créez un metastore, choisissez un nom de base de données sans tiret ni trait d’union, car la présence de ces caractères peut faire échouer le processus de création du cluster.

Sélectionnez **Suivant : Sécurité et réseau >>** pour passer à l’onglet suivant.

## <a name="security--networking"></a>Sécurité + mise en réseau

![HDInsight - Créer un cluster - Sécurité et réseau](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight - Créer un cluster - Sécurité et réseau")

Sous l’onglet **Sécurité et réseau**, indiquez les informations suivantes :

|Propriété |Description |
|---|---|
|Package de sécurité d’entreprise|Facultatif : Cochez la case pour utiliser le **Pack Sécurité Entreprise**. Pour plus d’informations, consultez [Configurer un cluster HDInsight avec le Pack Sécurité Entreprise en utilisant Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Facultatif : Sélectionnez une version TLS dans la liste déroulante. Pour plus d’informations, consultez [TLS](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Réseau virtuel|Facultatif : Sélectionnez un réseau virtuel et un sous-réseau existants dans la liste déroulante. Pour plus d’informations, consultez [Planifier un déploiement de réseau virtuel pour des clusters Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). L’article indique la configuration spécifique requise pour le réseau virtuel.|
|Paramètres de chiffrement de disque|Facultatif : Cochez la case pour utiliser le chiffrement. Pour plus d’informations, consultez [Chiffrement de disque avec clé gérée par le client](./disk-encryption.md).|
|Proxy REST Kafka|Ce paramètre est disponible uniquement pour le type de cluster Kafka. Pour plus d’informations, consultez [Utilisation d’un proxy REST](./kafka/rest-proxy.md).|
|Identité|Facultatif : Sélectionnez une identité de service affectée par l’utilisateur existante dans la liste déroulante. Pour plus d’informations, consultez [Identités managées dans Azure HDInsight](./hdinsight-managed-identities.md).|

Sélectionnez **Suivant : Configuration + prix >>** pour passer à l’onglet suivant.

## <a name="configuration--pricing"></a>Configuration + prix

![HDInsight - Créer un cluster - Configuration](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Onglet Configuration et prix")

Sous l’onglet **Configuration + prix**, indiquez les informations suivantes :

|Propriété |Description |
|---|---|
|+ Ajouter une application|Facultatif : Sélectionnez toutes les applications souhaitées. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Pour plus d’informations, consultez [Installer des applications lors de la création du cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Taille du nœud|Facultatif : Sélectionnez un nœud de taille différente.|
|Nombre de nœuds|Facultatif : Entrez le nombre de nœuds pour le type de nœud spécifié. Si vous envisagez d’utiliser plus de 32 nœuds Worker, sélectionnez une taille de nœud principal avec au moins huit cœurs et 14 Go de RAM. Planifiez la taille des nœuds lors de la phase de création du cluster ou adaptez leur taille après avoir créé le cluster.|
|Activer la mise à l’échelle automatique|Facultatif : Cochez la case pour activer cette fonctionnalité. Pour plus d’informations, consultez [Mettre à l’échelle automatiquement les clusters Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Ajouter une action de script|Facultatif : Cette option est utile si vous voulez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).|

Sélectionnez **Vérifier + créer >>** pour valider la configuration du cluster et passer à l’onglet final.

## <a name="review--create"></a>Vérifier + créer

![HDInsight - Créer un cluster - Résumé](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Spécifier le nombre de nœuds de cluster")

Passez en revue les paramètres. Sélectionnez **Créer** pour créer le cluster.

La création du cluster prend un certain temps (en règle générale, environ 20 minutes). Surveillez les **Notifications** pour contrôler le processus de provisionnement.

## <a name="post-creation"></a>Après la création

Quand vous avez terminé le processus de création, sélectionnez **Accéder à la ressource** dans la notification **Le déploiement a été effectué**. La fenêtre de cluster fournit les informations suivantes.

![HDI - Vue d’ensemble des clusters dans le portail Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriétés du cluster")

Voici une description de quelques icônes présentes dans la fenêtre :

|Propriété | Description |
|---|---|
|Vue d’ensemble|Fournit toutes les informations essentielles sur le cluster. son nom, le groupe de ressources auquel il appartient, son emplacement, le système d’exploitation utilisé, l’URL d’accès au tableau de bord du cluster, etc.|
|Tableaux de bord des clusters|Vous redirige vers le portail Ambari associé au cluster.|
|SSH + Connexion du cluster|Fournit les informations nécessaires pour accéder au cluster à l’aide de SSH.|
|DELETE|permet de supprimer le cluster HDInsight.|

## <a name="customize-clusters"></a>Personnaliser des clusters

* [Personnaliser des clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Supprimer le cluster

Consultez [Suppression d’un cluster HDInsight avec votre navigateur, PowerShell ou l’interface de ligne de commande Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Dépanner

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

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
