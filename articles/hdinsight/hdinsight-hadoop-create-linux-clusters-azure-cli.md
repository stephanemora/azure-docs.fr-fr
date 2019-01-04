---
title: Créer des clusters Apache Hadoop avec l’interface CLI Azure Classic – Azure HDInsight
description: Découvrez comment créer des clusters HDInsight à l’aide de l’interface multiplateforme Azure Classic CLI.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: c9b1f3570ae3f9b945c87c2ce316c3f72e32b4ad
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792048"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Créer des clusters HDInsight à l’aide de l’interface Azure Classic CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Les étapes de cette procédure décrivent comment créer un cluster HDInsight 3.5 à l’aide d’Azure Classic CLI.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure Classic CLI**. Les étapes décrites dans ce document ont été testées pour Azure Classic CLI version 0.10.14.

## <a name="log-in-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

Suivez les étapes décrites dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure](/cli/azure/authenticate-azure-cli) et connectez-vous à votre abonnement à l’aide de la méthode **login**.

## <a name="create-a-cluster"></a>Créer un cluster

Les étapes suivantes doivent être exécutées à partir d’une ligne de commande, telle que PowerShell ou Bash.

1. Utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure :

        azure login

    Vous êtes invité à fournir votre nom et mot de passe. Si vous avez plusieurs abonnements Azure, utilisez `azure account set <subscriptionname>` pour définir l’abonnement que les commandes Azure Classic CLI doivent utiliser.

2. Passez en mode Gestionnaire de ressources Azure en exécutant la commande suivante :

        azure config mode arm

3. Créez un groupe de ressources. Ce groupe de ressources contient le cluster HDInsight et le compte de stockage associé.

        azure group create groupname location

    * Remplacez `groupname` par un nom unique pour le groupe.

    * Remplacez `location` par la région géographique dans laquelle vous souhaitez créer le groupe.

       Pour obtenir la liste des emplacements valides, utilisez la commande `azure location list` , puis un des emplacements de la colonne `Name`.

4. Créez un compte de stockage. Ce compte de stockage est utilisé comme espace de stockage par défaut pour le cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Remplacez `groupname` par le nom du groupe créé à l’étape précédente.

    * Remplacez `location` par le même emplacement que celui utilisé à l’étape précédente.

    * Remplacez `storagename` par un nom de compte de stockage unique.

        > [!NOTE]  
        > Pour plus d’informations sur les paramètres utilisés dans cette commande, utilisez `azure storage account create -h` pour afficher l’aide relative à cette commande.

5. Récupérez la clé utilisée pour accéder au compte de stockage.

        azure storage account keys list -g groupname storagename

    * Remplacez `groupname` par le nom du groupe de ressources.
    * Remplacez `storagename` par le nom du compte de stockage.

      Dans les données renvoyées, enregistrez la valeur `key` par `key1`.

6. Créez un cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Remplacez `groupname` par le nom du groupe de ressources.

    * Remplacez `Hadoop` par le type de cluster que vous souhaitez créer. Par exemple, `Hadoop`, `HBase`, `Kafka`, `Spark` ou `Storm`.

      > [!IMPORTANT]  
      > Il existe différents types de clusters HDInsight correspondant à la charge de travail ou à la technologie pour laquelle ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster.

    * Remplacez `location` par le même emplacement que celui utilisé aux étapes précédentes.

    * Remplacez `storagename` par le nom du compte de stockage.

    * Remplacez `storagekey` par la clé obtenue à l’étape précédente.

    * Pour le paramètre `--defaultStorageContainer` , utilisez le même nom que celui utilisé pour le cluster.

    * Remplacez `admin` et `httppassword` par le nom et le mot de passe à utiliser lors de l’accès au cluster via HTTPS.

    * Remplacez `sshuser` et `sshuserpassword` par le nom et le mot de passe à utiliser lors de l’accès au cluster via SSH

      > [!IMPORTANT]  
      > Cet exemple crée un cluster avec deux nœuds de travail. Vous pouvez également modifier le nombre de nœuds worker après la création du cluster en effectuant des opérations de mise à l’échelle. Si vous envisagez d’utiliser plus de 32 nœuds worker, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM. Vous pouvez définir la taille du nœud principal à l’aide du paramètre `--headNodeSize` pendant la création du cluster.
      >
      > Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
      
      Le processus de création de cluster peut prendre plusieurs minutes. En règle générale, il dure environ 15 minutes.

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un cluster HDInsight à l’aide de l’interface Azure Classic CLI. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters Apache HBase

* [Bien démarrer avec Apache HBase sur HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Développer des applications Java pour Apache HBase sur HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm

* [Développer des topologies Java pour Apache Storm sur HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utiliser des composants Python dans Apache Storm sur HDInsight](storm/apache-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Apache Storm sur HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
