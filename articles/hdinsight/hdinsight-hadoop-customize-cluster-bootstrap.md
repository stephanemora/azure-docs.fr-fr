---
title: Personnaliser les configurations de cluster Azure HDInsight à l’aide de Bootstrap
description: Découvrez comment personnaliser la configuration du cluster HDInsight par programmation à l’aide de .Net, PowerShell et des modèles Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 7f9100686eaab8c4c75e3d862026b18b6c46ed09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65203704"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personnalisation de clusters HDInsight à l’aide de Bootstrap

Les scripts Bootstrap permettent d’installer et de configurer des composants dans Azure HDInsight par programmation. 

Il existe trois approches pour définir les paramètres de fichier config à mesure que votre cluster HDInsight est créé :

* Utilisation d'Azure PowerShell
* Utilisation du Kit de développement logiciel (SDK) .NET
* Utilisation d’un modèle Azure Resource Manager

Par exemple, à l’aide de ces méthodes de programmation, vous pouvez configurer des options dans ces fichiers :

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* Server.Properties (configuration de répartiteurs kafka)

Pour plus d’informations sur l’installation de composants supplémentaires sur un cluster HDInsight pendant le processus de création, consultez [Personnaliser des clusters Azure HDInsight à l’aide des actions de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Prérequis

* Si vous utilisez PowerShell, vous avez besoin du [module Az](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Le code PowerShell suivant permet de personnaliser une configuration [Apache Hive](https://hive.apache.org/) :

> [!IMPORTANT]  
> Le paramètre `Spark2Defaults` peut être utilisé avec [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Vous pouvez transmettre les valeurs vides pour le paramètre, comme indiqué dans l’exemple de code ci-dessous.


```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

[L’annexe](#appendix-powershell-sample) fournit un script PowerShell opérationnel complet.

**Pour vérifier la modification :**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Clusters HDInsight**. Si vous ne voyez pas cette option, cliquez d’abord sur **Tous les services**.
3. Cliquez sur le cluster que vous venez de créer en utilisant le script PowerShell.
4. Cliquez sur **Tableau de bord** en haut du panneau pour ouvrir l’IU Ambari.
5. Cliquez sur **Hive** dans le menu de gauche.
6. Sous **Résumé**, cliquez sur **HiveServer2**.
7. Sélectionnez l’onglet **Configurations** .
8. Cliquez sur **Hive** dans le menu de gauche.
9. Cliquez sur l’onglet **Avancé** .
10. Faites défiler vers le bas, puis développez **Site hive avancé**.
11. Recherchez **hive.metastore.client.socket.timeout** dans la section.

Et d’autres exemples sur la personnalisation d’autres fichiers de configuration :

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Utilisation du Kit de développement logiciel (SDK) .NET
Consultez [Créer des clusters Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Utilisation d’un modèle Resource Manager
Vous pouvez utiliser Bootstrap dans un modèle Resource Manager :

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop personnalise le modèle Azure Resource Manager de Bootstrap de cluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Voir aussi
* [Créer des clusters Apache Hadoop dans HDInsight][hdinsight-provision-cluster] pour obtenir des instructions sur la création d’un cluster HDInsight à l’aide d’autres options personnalisées.
* [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script]
* [Installer et utiliser Apache Spark sur les clusters HDInsight][hdinsight-install-spark]
* [Installer et utiliser Apache Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Procédure de création d’un cluster"

## <a name="appendix-powershell-sample"></a>Annexe : Exemple de code PowerShell

Ce script PowerShell permet de créer un cluster HDInsight et de personnaliser un paramètre Hive. Veillez à entrer des valeurs pour `$nameToken`, `$httpPassword` et `$sshPassword`.

> [!IMPORTANT]  
> Les valeurs de `DefaultStorageAccount` et `DefaultStorageContainer` ne sont pas renvoyées à partir de [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) lorsque l’option de [transfert sécurisé](../storage/common/storage-require-secure-transfer.md) est activée sur le compte de stockage.

> [!WARNING]  
> Le type de compte de stockage `BlobStorage` ne peut pas être utilisé pour les clusters HDInsight.


```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>' 

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>' 
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
