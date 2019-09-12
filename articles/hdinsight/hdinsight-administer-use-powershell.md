---
title: Gérer les clusters Apache Hadoop dans HDInsight avec PowerShell - Azure
description: Découvrez comment effectuer des tâches d’administration pour les clusters Apache Hadoop dans HDInsight au moyen d’Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 3f4ccd8de1f26ea898b0e7ec4bb57aa20b1be209
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885344"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestion des clusters Apache Hadoop dans HDInsight au moyen d’Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Dans cet article, vous allez découvrir comment gérer des clusters [Apache Hadoop](https://hadoop.apache.org/) dans Azure HDInsight à l’aide de module Az de Azure PowerShell. Pour obtenir la liste des cmdlets PowerShell HDInsight, consultez la rubrique [Référence Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Le [module Az](https://docs.microsoft.com/powershell/azure/overview) PowerShell installé.

## <a name="create-clusters"></a>Créer des clusters
Consultez la page [Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Énumérer les clusters
Utilisez la commande suivante pour afficher la liste de tous les clusters de l’abonnement actif :

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Afficher le cluster
Utilisez la commande suivante pour afficher les détails d’un cluster spécifique dans l’abonnement actif :

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Suppression des clusters
Utilisez la commande suivante pour supprimer un cluster :

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui le contient. Supprimer un groupe de ressources supprime toutes les ressources dans le groupe, notamment le compte de stockage par défaut.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

Impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

* Apache Hadoop

    Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

    Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Le redémarrage des services entraîne l’échec de toutes les tâches en cours ou en attente durant la réalisation de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.
* Apache HBase

    Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster, puis exécuter les commandes suivantes à partir d’une fenêtre d’invite de commandes :

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l’opération de mise à l’échelle terminée avec succès, vous devrez rééquilibrer la topologie.

    Cela peut se faire de deux façons à l’aide de :

  * l'interface utilisateur Web de Storm
  * l’outil d’interface de ligne de commande (CLI)

    Pour plus d’informations, consultez la documentation [Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

    ![HDInsight storm mise à l’échelle rééquilibrage](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Pour modifier la taille du cluster Hadoop à l’aide d’Azure PowerShell, exécutez la commande suivante depuis un ordinateur client :

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Octroyer/Révoquer l’accès
Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès. Pour révoquer :

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Pour octroyer :

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.

L’octroi et la révocation de l’accès sont également possibles par le biais du portail. Consultez [Gérer des clusters Apache Hadoop dans HDInsight avec le Portail Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Mettre à jour les informations d’identification de l’utilisateur HTTP
Il s'agit de la même procédure que pour l'octroi et la révocation de l'accès HTTP. Si l’accès HTTP a été octroyé au cluster, vous devez d’abord le révoquer.  Octroyez ensuite l’accès avec les informations d’identification de l’utilisateur HTTP.

## <a name="find-the-default-storage-account"></a>Trouvez le compte de stockage par défaut
Le script PowerShell suivant montre comment obtenir le nom de compte de stockage par défaut et les informations connexes :

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Trouvez le groupe de ressources
En mode Resource Manager, chaque cluster HDInsight appartient à un groupe de ressources Azure.  Pour rechercher le groupe de ressources :

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Soumettre les travaux
**Pour envoyer des tâches MapReduce**

Consultez [Exécuter les exemples MapReduce inclus dans HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Pour envoyer des travaux Apache Hive**

Consultez [Exécuter des requêtes Apache Hive avec PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Pour envoyer des travaux Apache Sqoop**

Consultez [Utiliser Apache Sqoop avec HDInsight](hadoop/hdinsight-use-sqoop.md).

**Pour envoyer des travaux Apache Oozie**

Consultez [Utilisation d’Apache Oozie avec Apache Hadoop pour définir et exécuter un workflow Azure HDInsight basé sur Linux](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Téléchargement de données vers le stockage d'objets blob Azure

Consultez [Charger les données vers HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Voir aussi

* [Documentation de référence des cmdlets HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Gérer des clusters Apache Hadoop dans HDInsight avec le portail Azure](hdinsight-administer-use-portal-linux.md)
* [Administrer HDInsight à l’aide d’une interface de ligne de commande](hdinsight-administer-use-command-line.md)
* [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Envoi de tâches Apache Hadoop par programmation](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
