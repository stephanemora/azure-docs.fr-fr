---
title: Charger des données pour des travaux Apache Hadoop dans HDInsight
description: Découvrez comment charger des données pour des travaux Apache Hadoop et y accéder dans HDInsight avec Azure Classic CLI, Explorateur Stockage Azure, Azure PowerShell, la ligne de commande Hadoop ou Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100146"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Charger des données pour des travaux Apache Hadoop dans HDInsight

Azure HDInsight fournit un système HDFS (Hadoop Distributed File System) complet pour Stockage Azure et Azure Data Lake Storage (Gen1 et Gen2). Stockage Azure et Azure Data Lake Storage (Gen1 et Gen2) sont conçus en tant qu’extensions HDFS pour offrir aux clients une expérience fluide. Ils permettent à l’ensemble des composants de l’écosystème Hadoop de fonctionner directement sur les données qu’il gère. Stockage Azure et Azure Data Lake Storage (Gen1 et Gen2) sont des systèmes de fichiers distincts, optimisés pour le stockage de données et pour les calculs réalisés à partir de ces données. Pour connaître les avantages associés à l’utilisation de Stockage Azure, voir [Utiliser Stockage Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md), [Utiliser Data Lake Storage Gen1 avec HDInsight](hdinsight-hadoop-use-data-lake-store.md) et [Utiliser Data Lake Storage Gen2 avec HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Prérequis

Notez les prérequis suivants avant de démarrer :

* Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) ou la rubrique [Créer des clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Connaissance des articles suivants :
    * [Utiliser Stockage Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Utiliser Data Lake Storage Gen1 avec HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Utiliser Data Lake Storage Gen2 avec HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Charger des données sur le Stockage Azure

## <a name="utilities"></a>Services

Microsoft fournit les utilitaires suivants pour utiliser le Stockage Azure :

| Outil | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Commande Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> La commande Hadoop est uniquement disponible sur le cluster HDInsight. La commande autorise uniquement le chargement de données du système de fichiers local sur le Stockage Azure.  

## <a name="hadoop-command-line"></a><a id="commandline"></a>Ligne de commande Hadoop

La ligne de commande Hadoop est utile uniquement pour stocker les données dans le blob du stockage Azure quand celles-ci sont déjà présentes sur le nœud principal du cluster.

Pour utiliser la commande Hadoop, vous devez d’abord vous connecter au nœud principal à l’aide de [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté, vous pouvez utiliser la syntaxe suivante pour télécharger un fichier dans le stockage.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Par exemple : `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Comme le système de fichiers par défaut pour HDInsight se trouve dans le Stockage Azure, /example/data.txt s’y trouve également. Vous pouvez également faire référence au fichier comme ceci :

    wasbs:///example/data/data.txt

or

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Pour la liste des autres commandes Hadoop qui fonctionnent avec des fichiers, voir [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Sur les clusters Apache HBase, la taille de bloc par défaut durant l’écriture de données est de 256 Ko. Bien que cela fonctionne bien quand vous utilisez les API HBase ou les API REST, l’utilisation des commandes `hadoop` ou `hdfs dfs` pour écrire des données supérieures à ~12 Go génère une erreur. Pour plus d’informations, consultez la section [Exception de stockage pour l’écriture sur un objet blob](#storageexception) dans cet article.

## <a name="graphical-clients"></a>Clients graphiques

Plusieurs applications fournissent également une interface graphique pour utiliser Azure Storage. Le tableau suivant répertorie quelques-unes de ces applications :

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Outils Microsoft Visual Studio pour HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer for Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Monter le stockage Azure comme un lecteur Local

Consultez [Monter le stockage Azure comme un lecteur Local](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Effectuer un chargement en utilisant des services

### <a name="azure-data-factory"></a>Azure Data Factory

Le service Azure Data Factory est un service entièrement géré pour composer des services de stockage de données, de traitement de données et de déplacement de données dans des pipelines de production rationalisés, évolutifs et fiables.

|Type de stockage|Documentation|
|----|----|
|Stockage Blob Azure|[Copier des données vers ou depuis le stockage Blob Azure à l’aide d’Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l'aide d'Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Charger des données dans Azure Data Lake Storage Gen2 avec Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a><a id="sqoop"></a>Apache Sqoop

Sqoop est un outil conçu pour transférer des données entre Hadoop et des bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQ Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR.

Pour plus d’informations, consultez [Utilisation de Sqoop avec HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Kits de développement logiciel (SDK) de développement

Le Stockage Azure est également accessible à l’aide d’un SDK Azure dans les langages de programmation suivants :

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Pour plus d'informations sur l'installation des kits de développement logiciel (SDK) Azure, consultez [Téléchargements Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Dépannage

### <a name="storage-exception-for-write-on-blob"></a><a id="storageexception"></a>Exception de stockage pour l’écriture sur un objet blob

**Symptômes** : Quand vous utilisez la commande `hadoop` ou `hdfs dfs` pour écrire des fichiers supérieurs à ~12 Go sur un cluster HBase, vous pouvez rencontrer l’erreur suivante :

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Cause** : Les clusters HBase sur HDInsight ont par défaut une taille de bloc de 256 Ko lors de l’écriture dans le stockage Azure. Bien que cela fonctionne pour les API HBase ou les API REST, cela génère une erreur quand vous utilisez les utilitaires en ligne de commande `hadoop` ou `hdfs dfs`.

**Résolution** : Utilisez `fs.azure.write.request.size` pour spécifier une plus grande taille de bloc. Vous pouvez le faire sur une base par utilisation à l’aide du paramètre `-D`. La commande suivante illustre l’utilisation de ce paramètre avec la commande `hadoop` :

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Vous pouvez également augmenter la valeur de `fs.azure.write.request.size` globalement à l’aide d’Apache Ambari. Vous pouvez utiliser les étapes suivantes pour modifier la valeur dans l’interface utilisateur Web d’Ambari :

1. Dans votre navigateur, accédez à l’interface utilisateur Web d’Ambari pour votre cluster. Il s’agit de `https://CLUSTERNAME.azurehdinsight.net`, où `CLUSTERNAME` est le nom de votre cluster.

    Lorsque vous y êtes invité, entrez le nom et le mot de passe administrateur correspondant au cluster HDInsight.
2. Sur le côté gauche de l’écran, sélectionnez **HDFS**, puis l’onglet **Configurations**.
3. Dans le champ **Filtrer...** , entrez `fs.azure.write.request.size`. Le champ et la valeur actuelle s’affichent au milieu de la page.
4. Modifiez la valeur 262144 (256 Ko) sur la nouvelle valeur. Par exemple, 4194304 (4 Mo).

    ![Image de la modification de la valeur via l’interface utilisateur Web d’Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Pour plus d’informations sur l’utilisation d’Ambari, voir [Gestion des clusters HDInsight à l’aide de l’interface utilisateur Web d’Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment obtenir des données avec HDInsight, consultez les articles suivants pour apprendre à effectuer des analyses :

* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Envoi de tâches Apache Hadoop par programmation](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
