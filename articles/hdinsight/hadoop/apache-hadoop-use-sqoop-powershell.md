---
title: Exécuter des tâches Apache Sqoop avec PowerShell et Azure HDInsight
description: Découvrez comment utiliser Azure PowerShell à partir d’un poste de travail pour exécuter des commandes Apache Sqoop import et export entre un cluster Apache Hadoop et une base de données Azure SQL.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 49375b00607f1f23a99855f8b80f5f5c3c4dac22
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686668"
---
# <a name="run-apache-sqoop-jobs-by-using-azure-powershell-for-apache-hadoop-in-hdinsight"></a>Exécuter des tâches Apache Sqoop avec Azure PowerShell pour Apache Hadoop dans HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Azure PowerShell pour exécuter des tâches Apache Sqoop dans HDInsight Azure pour importer et exporter des données entre un cluster HDInsight et une base de données SQL Azure ou la base de données SQL Server. Cet exemple exporte des données à partir de `/tutorials/usesqoop/data/sample.log` dans le compte de stockage par défaut, puis les importations pour une table appelée `log4jlogs` dans une base de données SQL Server. Cet article est la suite de [Use Apache Sqoop avec Hadoop dans HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* Une station de travail avec Azure PowerShell [AZ Module](https://docs.microsoft.com/powershell/azure/overview) installé.

* Saisie semi-automatique de [configurer d’environnement de test](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [Use Apache Sqoop avec Hadoop dans HDInsight](./hdinsight-use-sqoop.md).


## <a name="run-apache-sqoop-by-using-powershell"></a>Exécuter Apache Sqoop avec PowerShell
Le script PowerShell suivant prétraite le fichier source, puis l’exporte vers une base de données SQL Azure à la table `log4jlogs`. Remplacez `CLUSTERNAME`, `CLUSTERPASSWORD`, et `SQLPASSWORD` avec les valeurs que vous avez utilisé par les conditions préalables.

```powershell 
<#------ BEGIN USER INPUT ------#>
$hdinsightClusterName = "CLUSTERNAME"
$httpUserName = "admin"  #default is admin, update as needed
$httpPassword = 'CLUSTERPASSWORD'
$sqlDatabasePassword = 'SQLPASSWORD'
<#------- END USER INPUT -------#>

# Other fixed variable that should be used as is
$sqlServerName = $hdinsightClusterName + "dbserver"
$sqlDatabaseName = $hdinsightClusterName + "db"
$tableName_log4j = "log4jlogs"
$exportDir_log4j = "/tutorials/usesqoop/data"
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"
$sqljdbcdriver = "/user/oozie/share/lib/sqoop/mssql-jdbc-7.0.0.jre8.jar"

$cluster = Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
$defaultStorageAccountName = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageContainer = $cluster.DefaultStorageContainer
$resourceGroup = $cluster.ResourceGroup

$sqlServer = Get-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $sqlServerName
$sqlServerLogin = $sqlServer.SqlAdministratorLogin
$sqlServerFQDN = $sqlServer.FullyQualifiedDomainName

#Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}

#pre-process the source file
Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
# Define the connection string
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroup `
                                -Name $defaultStorageAccountName)[0].Value

# Create block blob objects referencing the source and destination blob.
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $defaultStorageAccountName

$storageContainer = ($storageAccount |Get-AzStorageContainer -Name $defaultStorageContainer).CloudBlobContainer

$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#export the log file from the cluster to the SQL database
Write-Host "Exporting the log file ..." -ForegroundColor Green

$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

# Connection string
$connectionString = "jdbc:sqlserver://$sqlServerFQDN;user=$sqlServerLogin@$sqlServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
    -Files $sqljdbcdriver

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroup `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultStorageContainer `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput
```

## <a name="limitations"></a>Limites
HDInsight basé sur Linux présente les limitations suivantes :

* Exportation en bloc : le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou Azure SQL Database ne prend pas en charge actuellement les insertions en bloc.

* Traitement par lot : lorsque vous utilisez le commutateur `-batch` pour procéder à des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lot des opérations d’insertion. 

## <a name="next-steps"></a>Étapes suivantes
Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utiliser Apache Oozie avec HDInsight](../hdinsight-use-oozie-linux-mac.md) : Utilisez l’action Sqoop dans un workflow Oozie.
* [Chargez des données dans HDInsight](../hdinsight-upload-data.md) : découvrez d’autres méthodes pour charger des données dans HDInsight ou le stockage Blob Azure.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
