---
title: Gérer les clusters Apache Hadoop avec PowerShell – Azure HDInsight
description: Découvrez comment effectuer des tâches d’administration pour les clusters Apache Hadoop dans HDInsight au moyen d’Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435622"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestion des clusters Apache Hadoop dans HDInsight au moyen d’Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Dans cet article, vous allez découvrir comment gérer des clusters [Apache Hadoop](https://hadoop.apache.org/) dans Azure HDInsight à l’aide de module Az de Azure PowerShell. Pour obtenir la liste des cmdlets PowerShell HDInsight, consultez la rubrique [Référence Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Le [module Az](https://docs.microsoft.com/powershell/azure/overview) PowerShell installé.

## <a name="create-clusters"></a>Créer des clusters

Consultez la page [Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lister les clusters

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

La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster. Pour modifier la taille du cluster Hadoop à l’aide d’Azure PowerShell, exécutez la commande suivante depuis un ordinateur client :

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Pour plus d’informations sur la mise à l’échelle des clusters, consultez [Mettre à l’échelle les clusters HDInsight](./hdinsight-scaling-best-practices.md).

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
$hadoopUserPassword = '<Enter the Password>'
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

Il s’agit de la même procédure que pour l’octroi et la révocation de l’accès HTTP. Si l’accès HTTP a été octroyé au cluster, vous devez d’abord le révoquer.  Octroyez ensuite l’accès avec les informations d’identification de l’utilisateur HTTP.

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

* [Cmdlets Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Gérer des clusters Apache Hadoop dans HDInsight avec le portail Azure](hdinsight-administer-use-portal-linux.md)
* [Administrer HDInsight à l’aide d’une interface de ligne de commande](hdinsight-administer-use-command-line.md)
* [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Envoi de tâches Apache Hadoop par programmation](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
