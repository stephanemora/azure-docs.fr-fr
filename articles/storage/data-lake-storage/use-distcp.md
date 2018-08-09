---
title: Copier des données dans Azure Data Lake Storage Gen2 (préversion) à l’aide de Distcp | Microsoft Docs
description: Utiliser l’outil Distcp pour copier des données vers et à partir de Data Lake Storage Gen2 (préversion)
services: storage
author: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 065c4c4315bda209484cc1b2449980e55d4ac798
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522694"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Utiliser Distcp pour copier des données entre des objets blob de Stockage Azure et Data Lake Storage Gen2 (préversion)

Si vous avez un cluster HDInsight avec un accès à Azure Data Lake Storage Gen2 Preview, vous pouvez utiliser des outils de l’écosystème Hadoop comme [Distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) pour copier des données **vers et à partir d’un** stockage de cluster HDInsight (WASB) dans un compte Data Lake Storage Gen2. Cet article fournit des instructions sur l’utilisation de l’outil Distcp.

## <a name="prerequisites"></a>Prérequis

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte de stockage Azure avec la fonctionnalité Azure Data Lake Storage (préversion) activée**. Pour savoir comment en créer un, consultez [Démarrage rapide : créer un compte de stockage Azure Data Lake Storage Gen2 Preview](quickstart-create-account.md).
* **Cluster Azure HDInsight** avec un accès à un compte Data Lake Storage. Consultez [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](use-hdi-cluster.md). Veillez à activer le Bureau à distance pour le cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utiliser Distcp à partir d’un cluster HDInsight Linux

Un cluster HDInsight est fourni avec l’utilitaire Distcp, que vous pouvez utiliser pour copier dans un cluster des données provenant de différentes sources. Si vous avez configuré le cluster HDInsight pour utiliser à la fois le Stockage Blob Azure et Azure Data Lake Storage, l’utilitaire Distcp peut également être utilisé tel quel pour copier des données de l’un à l’autre. Dans cette section, nous allons voir comment utiliser l’utilitaire Distcp.

1. À partir de votre Bureau, utilisez SSH pour la connexion au cluster. Consultez [Connexion à un cluster HDInsight sous Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Exécutez les commandes de l’invite SSH.

2. Vérifiez si vous pouvez accéder aux objets blob d’Azure Storage (WASB). Exécutez la commande suivante :

        hdfs dfs –ls wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Vous devriez obtenir la liste du contenu de l’objet blob de stockage.

3. Là encore, vérifiez si vous pouvez accéder au compte Data Lake Storage à partir du cluster. Exécutez la commande suivante :

        hdfs dfs -ls abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    La sortie doit vous donner la liste des fichiers/dossiers du compte Data Lake Storage.

4. Utilisez Distcp pour copier des données de WASB vers un compte Data Lake Storage.

        hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Cette commande copie le contenu du dossier **/example/data/gutenberg/** du stockage Blob vers **/myfolder** dans le compte Data Lake Storage.

5. De la même manière, utilisez Distcp pour copier des données d’un compte Data Lake Storage vers le stockage Blob (WASB).

        hadoop distcp abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Cette commande copie le contenu du répertoire **/myfolder** du compte Data Lake Store vers le dossier **/example/data/gutenberg/** de WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considérations de performances lors de l’utilisation de DistCp

La granularité la plus basse de DistCp étant un fichier unique, la définition du nombre maximal de copies simultanées est le paramètre le plus important à optimiser par rapport à Data Lake Storage. Vous contrôlez le nombre de copies simultanées en définissant le paramètre de nombre de mappeurs (**m**) en ligne de commande. Ce paramètre spécifie le nombre maximal de mappeurs utilisés pour copier les données. La valeur par défaut est 20.

**Exemple**

    hadoop distcp wasb://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfs://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Comment déterminer le nombre de mappeurs à utiliser ?

Voici quelques conseils à suivre.

* **Étape 1 : Déterminer la quantité totale de mémoire YARN** : la première étape consiste à déterminer la mémoire YARN disponible pour le cluster sur lequel vous exécutez la tâche DistCp. Ces informations sont disponibles dans le portail de Ambari associé au cluster. Accédez à YARN et affichez l’onglet Configurations pour voir la mémoire YARN. Pour obtenir la mémoire YARN totale, multipliez la mémoire YARN par nœud par le nombre de nœuds dans votre cluster.

* **Étape 2 : Calculer le nombre de mappeurs** : la valeur de **m** est égale au quotient de la mémoire de YARN totale divisée par la taille du conteneur YARN. Les informations sur la taille du conteneur YARN sont également disponibles dans le portail Ambari. Accédez à YARN et affichez l’onglet Configurations. La taille du conteneur YARN s’affiche dans cette fenêtre. L’équation pour obtenir le nombre de mappeurs (**m**) est

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemple**

Supposons que vous avez 4 nœuds D14v2s dans le cluster et que vous essayez de transférer 10 To de données à partir de 10 dossiers différents. Chaque dossier contient différentes quantités de données, et la taille des fichiers dans chaque dossier est différente.

* **Total de mémoire YARN** : à partir du portail Ambari, vous déterminez que la mémoire YARN est de 96 Go pour un nœud D14. Ainsi, la mémoire YARN totale pour un cluster à quatre nœuds est : 

        YARN memory = 4 * 96GB = 384GB

* **Nombre de mappeurs** : à partir du portail Ambari, vous déterminez que la taille du conteneur YARN est de 3072 pour un nœud de cluster D14. Par conséquent, le nombre de mappeurs est :

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Si d’autres applications utilisent de la mémoire, vous pouvez choisir d’utiliser uniquement une partie de la mémoire de YARN de votre cluster pour DistCp.

### <a name="copying-large-datasets"></a>Copie de jeux de données volumineux

Lorsque la taille du jeu de données à déplacer est volumineuse (par exemple > 1 To) ou si vous avez de nombreux dossiers, vous devez envisager d’utiliser plusieurs tâches DistCp. Il n’y a probablement aucun gain de performance, mais cela répartit les tâches. Ainsi, si une tâche échoue, il vous suffit de redémarrer la tâche en question plutôt que l’ensemble complet.

### <a name="limitations"></a>Limites

* DistCp tente de créer des mappeurs similaires en taille pour optimiser les performances. L’augmentation du nombre de mappeurs n’améliore pas nécessairement les performances.

* DistCp est limité à un seul mappeur par fichier. Par conséquent, vous ne devez pas avoir plus de mappeurs que de fichiers. Étant donné que DistCp peut affecter un seul mappeur par fichier, cela limite la quantité d’exécutions simultanées utilisables pour copier des fichiers volumineux.

* Si vous avez un petit nombre de fichiers volumineux, vous devez les fractionner en plusieurs segments de fichier de 256 Mo pour avoir un meilleur potentiel de simultanéité. 
