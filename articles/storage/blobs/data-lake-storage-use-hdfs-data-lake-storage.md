---
title: Utilisation de l’interface CLI HDFS avec Azure Data Lake Storage Gen2 Preview
description: Présentation de l’interface CLI HDFS pour Data Lake Storage Gen2 Preview
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: b4485e234e19e93a852895c80775b8aadc7a15ce
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976492"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Utilisation de l’interface CLI HDFS avec Data Lake Storage Gen2

Azure Data Lake Storage Gen2 Preview vous permet de gérer les données et d’y accéder comme avec un [système de fichiers DFS Hadoop (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Si vous avez un cluster HDInsight attaché ou que vous exécutez un travail Apache Spark à l’aide d’Azure Databricks pour analyser les données stockées dans un compte Stockage Azure, vous pouvez utiliser l’interface de ligne de commande (CLI) pour récupérer et manipuler les données chargées.

## <a name="hdfs-cli-with-hdinsight"></a>Interface CLI HDFS avec HDInsight

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Ce système de fichiers est accessible à l’aide de l’interpréteur de commandes qui interagit directement avec le système de fichiers HDFS et d’autres systèmes de fichiers pris en charge par Hadoop. Voici les commandes couramment utilisées et les liens vers des ressources utiles.

>[!IMPORTANT]
>La facturation du cluster HDInsight démarre après la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Pour découvrir comment supprimer un cluster, consultez notre [article à ce sujet](../../hdinsight/hdinsight-delete-cluster.md). Toutefois, les données stockées dans un compte de stockage avec Azure Data Lake Storage Gen2 persistent même après la suppression du cluster HDInsight.

### <a name="get-a-list-of-files-or-directories"></a>Obtenir la liste des fichiers ou répertoires

    hdfs dfs -ls <args>

### <a name="create-a-directory"></a>Créer un répertoire

    hdfs dfs -mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Supprimer un fichier ou un répertoire

    hdfs dfs -rm [-skipTrash] URI [URI ...]

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Utiliser l’interface CLI HDFS avec un cluster HDInsight Hadoop sur Linux

Tout d’abord, établissez l’[accès à distance aux services](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Si vous choisissez [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix), l’exemple de code PowerShell se présente comme suit :

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
La chaîne de connexion se trouve dans la section « Connexion SSH + Cluster » du panneau du cluster HDInsight dans le portail Azure. Les informations d’identification SSH ont été spécifiées au moment de la création du cluster.

Pour plus d’informations sur l’interface CLI HDFS, consultez la [documentation officielle](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) et le [Guide des autorisations HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Pour en savoir plus sur les listes ACL dans Databricks, consultez [Secrets CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli). 

## <a name="hdfs-cli-with-azure-databricks"></a>Interface CLI HDFS avec Azure Databricks

Databricks fournit une interface CLI facile à utiliser qui repose sur l’API REST de Databricks. Le projet open source est hébergé sur [GitHub](https://github.com/databricks/databricks-cli). Voici les commandes couramment utilisées.

### <a name="get-a-list-of-files-or-directories"></a>Obtenir la liste des fichiers ou répertoires

    dbfs ls [-l]

### <a name="create-a-directory"></a>Créer un répertoire

    dbfs mkdirs

### <a name="delete-a-file"></a>Supprimer un fichier

    dbfs rm [-r]

Une autre façon d’interagir avec Databricks est d’utiliser des bloc-notes. Un bloc-notes a un langage principal, vous pouvez mélanger les langages en spécifiant la commande magique de langage %language au début de la cellule. En particulier, %sh vous permet d’exécuter du code d’interpréteur de commandes dans votre bloc-notes qui ressemble beaucoup à l’exemple HDInsight plus haut dans cet article.

### <a name="get-a-list-of-files-or-directories"></a>Obtenir la liste des fichiers ou répertoires

    %sh ls <args>

### <a name="create-a-directory"></a>Créer un répertoire

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Supprimer un fichier ou un répertoire

    %sh rm [-skipTrash] URI [URI ...]

Après avoir démarré le cluster Spark dans Azure Databricks, vous créez un bloc-notes. L’exemple de script de bloc-notes se présente comme suit :

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Pour plus d’informations sur l’interface CLI Databricks, consultez la [documentation officielle](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Pour plus d’informations sur les bloc-notes, consultez la section [blocs-notes](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) de la documentation.

## <a name="set-file-and-directory-level-permissions"></a>Définir des autorisations au niveau des répertoires et des fichiers

Vous définissez et obtenez des autorisations d’accès au niveau des fichiers et des répertoires. Voici quelques commandes pour vous aider à démarrer. 

Pour en savoir plus sur les autorisations au niveau des répertoires et des fichiers pour le système de fichiers Azure Data Lake Gen2, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Afficher les listes de contrôle d’accès (ACL) des fichiers et répertoires

    hdfs dfs -getfacl [-R] <path>

Exemple :

`hdfs dfs -getfacl -R /dir`

Voir [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Définir les listes ACL des fichiers et répertoires

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exemple :

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Voir [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Changer le propriétaire des fichiers

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Voir [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Changer l’association de groupe des fichiers

    hdfs dfs -chgrp [-R] <group> <URI>

Voir [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Changer les autorisations des fichiers

    hdfs dfs -chmod [-R] <mode> <URI>

Voir [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Vous pouvez voir la liste complète des commandes en accédant au site web [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="next-steps"></a>Étapes suivantes

[Utiliser un compte Azure Data Lake Storage Gen2 dans Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) 