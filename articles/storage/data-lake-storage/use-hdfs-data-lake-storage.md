---
title: Utilisation de l’interface CLI HDFS avec Azure Data Lake Storage Gen2 Preview
description: Présentation de l’interface CLI HDFS pour Data Lake Storage Gen2 Preview
services: storage
documentationcenter: ''
author: artemuwka
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75fb07120c78c45d422ee5017eac0afcf0e80859
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060816"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Utilisation de l’interface CLI HDFS avec Data Lake Storage Gen2

Azure Data Lake Storage Gen2 Preview vous permet de gérer les données et d’y accéder comme avec un [système de fichiers DFS Hadoop (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Si vous avez un cluster HDInsight attaché ou que vous exécutez un travail Apache Spark à l’aide d’Azure Databricks pour analyser les données stockées dans Azure Data Lake Storage Gen2, vous pouvez utiliser l’interface de ligne de commande (CLI) pour récupérer et manipuler les données chargées. Le reste de l’article décrit les options dont vous disposez en attendant que [l’équipe du Stockage Azure ajoute la prise en charge de l’Explorateur Stockage Azure et du portail Azure](https://azure.microsoft.com/roadmap/) - Amusez-vous bien !

## <a name="hdfs-cli-with-hdinsight"></a>Interface CLI HDFS avec HDInsight

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Ce système de fichiers est accessible à l’aide de l’interpréteur de commandes qui interagit directement avec le système de fichiers HDFS et d’autres systèmes de fichiers pris en charge par Hadoop. Voici les commandes couramment utilisées et les liens vers des ressources utiles.

>[!IMPORTANT]
>La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster quand il n’est plus utilisé (découvrez comment [supprimer un cluster](../../hdinsight/hdinsight-delete-cluster.md)). Toutefois, les données stockées dans Azure Data Lake Storage Gen2 persistent même après la suppression d’un cluster HDInsight.

Pour obtenir la liste des fichiers ou répertoires :

    hdfs dfs -ls <args>
Pour créer un répertoire :

    hdfs dfs -mkdir [-p] <paths>
Pour supprimer un fichier ou un répertoire :

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Prenons maintenant l’exemple d’un cluster Hadoop HDInsight sur Linux. Pour utiliser l’interface CLI HDFS, vous devez d’abord établir un [accès à distance aux services](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Si vous choisissez [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), l’exemple de code PowerShell se présente comme suit :
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

La chaîne de connexion se trouve dans la section « Connexion SSH + Cluster » du panneau du cluster HDInsight dans le portail Azure. Les informations d’identification SSH ont été spécifiées au moment de la création du cluster.

Pour plus d’informations sur l’interface CLI HDFS, consultez la [documentation officielle](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) et le [Guide des autorisations HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>Interface CLI HDFS avec Azure Databricks

Databricks fournit une interface CLI facile à utiliser qui repose sur l’API REST de Databricks. Le projet open source est hébergé sur [GitHub](https://github.com/databricks/databricks-cli). Voici les commandes couramment utilisées.

Pour obtenir la liste des fichiers ou répertoires :

    dbfs ls [-l]
Pour créer un répertoire :

    dbfs mkdirs
Pour supprimer un fichier :

    dbfs rm [-r]

Une autre façon d’interagir avec Databricks est d’utiliser des bloc-notes. Un bloc-notes a un langage principal, vous pouvez mélanger les langages en spécifiant la commande magique de langage %language au début de la cellule. En particulier, %sh vous permet d’exécuter du code d’interpréteur de commandes dans votre bloc-notes qui ressemble beaucoup à l’exemple HDInsight plus haut dans cet article.

Pour obtenir la liste des fichiers ou répertoires :

    %sh ls <args>
Pour créer un répertoire :

    %sh mkdir [-p] <paths>
Pour supprimer un fichier ou un répertoire :

    %sh rm [-skipTrash] URI [URI ...]

Après avoir démarré le cluster Spark dans Azure Databricks, vous créez un bloc-notes. L’exemple de script de bloc-notes se présente comme suit :

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Pour plus d’informations sur l’interface CLI Databricks, consultez la [documentation officielle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Pour plus d’informations sur les bloc-notes, consultez la section [blocs-notes](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) de la documentation.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster HDInsight avec Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Utiliser un compte Azure Data Lake Storage Gen2 dans Azure Databricks](./quickstart-create-databricks-account.md) 