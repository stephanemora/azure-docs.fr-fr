---
title: Copier des données vers et depuis WASB dans Azure Data Lake Storage Gen1 à l’aide de Distcp| Microsoft Docs
description: Utilisez l’outil Distcp pour copier des données entre des objets blob Azure Storage et Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fbefe233ce0d2477982faf0a9f38a73062e0c7a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878798"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Utiliser Distcp pour copier des données entre des objets blob Stockage Azure et Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Utilisation de DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilisation d’AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Si vous avez un cluster HDInsight avec un accès à Azure Data Lake Storage Gen1, vous pouvez utiliser des outils de l’écosystème Hadoop comme Distcp pour copier des données **vers et à partir d’un** stockage de cluster HDInsight (WASB) dans un compte Data Lake Storage Gen1. Cet article fournit des instructions sur l’utilisation de l’outil Distcp.

## <a name="prerequisites"></a>Conditions préalables

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Storage Gen1**. Pour savoir comment en créer un, consultez [Prise en main d’Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** avec accès à un compte Data Lake Storage Gen1. Voir [Créer un cluster HDInsight avec Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à activer le Bureau à distance pour le cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utiliser Distcp à partir d’un cluster HDInsight Linux

Un cluster HDInsight est fourni avec l’utilitaire Distcp, que vous pouvez utiliser pour copier dans un cluster des données provenant de différentes sources. Si vous avez configuré le cluster HDInsight pour utiliser Data Lake Storage Gen1 comme espace de stockage supplémentaire, l’utilitaire Distcp peut également être utilisé tel quel pour copier des données vers et depuis un compte Data Lake Storage Gen1. Dans cette section, nous allons voir comment utiliser l’utilitaire Distcp.

1. À partir de votre Bureau, utilisez SSH pour la connexion au cluster. Consultez [Connexion à un cluster HDInsight sous Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Exécutez les commandes de l’invite SSH.

2. Vérifiez si vous pouvez accéder aux objets blob d’Azure Storage (WASB). Exécutez la commande suivante :

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Vous devriez obtenir la liste du contenu de l’objet blob de stockage.

3. Là encore, vérifiez si vous pouvez accéder au compte Data Lake Storage Gen1 à partir du cluster. Exécutez la commande suivante :

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    La sortie doit vous donner la liste des fichiers/dossiers du compte Data Lake Storage Gen1.

4. Utilisez Distcp pour copier des données de WASB vers un compte Data Lake Storage Gen1.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Cette commande copie le contenu du dossier **/example/data/gutenberg/** de WASB vers le répertoire **/myfolder** du compte Data Lake Storage Gen1.

5. De la même manière, utilisez Distcp pour copier des données d’un compte Data Lake Storage Gen1 vers WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Cette commande copie le contenu du répertoire **/myfolder** du compte Data Lake Storage Gen1 vers le dossier **/example/data/gutenberg/** de WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considérations de performances lors de l’utilisation de DistCp

La granularité la plus basse de DistCp étant un fichier unique, la définition du nombre maximal de copies simultanées est le paramètre le plus important à optimiser par rapport à Data Lake Storage Gen1. Vous contrôlez le nombre de copies simultanées en définissant le paramètre de nombre de mappeurs (‘m’) en ligne de commande. Ce paramètre spécifie le nombre maximal de mappeurs utilisés pour copier les données. La valeur par défaut est 20.

**Exemple**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Comment déterminer le nombre de mappeurs à utiliser ?

Voici quelques conseils à suivre.

* **Étape 1 : Déterminer la mémoire YARN totale** -la première étape consiste à déterminer la quantité de mémoire YARN disponible pour le cluster sur lequel vous exécutez la tâche DistCp. Ces informations sont disponibles dans le portail de Ambari associé au cluster. Accédez à YARN et affichez l’onglet Configurations pour voir la mémoire YARN. Pour obtenir la mémoire YARN totale, multipliez la mémoire YARN par nœud par le nombre de nœuds dans votre cluster.

* **Étape 2 : Calculer le nombre de mappeurs** - La valeur de **m** est égale au quotient de la mémoire YARN totale divisée par la taille du conteneur YARN. Les informations sur la taille du conteneur YARN sont également disponibles dans le portail Ambari. Accédez à YARN et affichez l’onglet Configurations. La taille du conteneur YARN s’affiche dans cette fenêtre. L’équation pour obtenir le nombre de mappeurs (**m**) est

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemple**

Supposons que vous avez 4 nœuds D14v2s dans le cluster et que vous essayez de transférer 10 To de données à partir de 10 dossiers différents. Chaque dossier contient différentes quantités de données, et la taille des fichiers dans chaque dossier est différente.

* Total de mémoire YARN : à partir du portail Ambari, vous déterminez que la mémoire YARN est de 96 Go pour un nœud D14. Ainsi, la mémoire YARN totale pour un cluster à quatre nœuds est : 

        YARN memory = 4 * 96GB = 384GB

* Nombre de mappeurs : à partir du portail Ambari, vous déterminez que la taille du conteneur YARN est de 3072 pour un nœud de cluster D14. Par conséquent, le nombre de mappeurs est :

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Si d’autres applications utilisent de la mémoire, vous pouvez choisir d’utiliser uniquement une partie de la mémoire de YARN de votre cluster pour DistCp.

### <a name="copying-large-datasets"></a>Copie de jeux de données volumineux

Lorsque la taille du jeu de données à déplacer est volumineuse (par exemple > 1 To) ou si vous avez de nombreux dossiers, vous devez envisager d’utiliser plusieurs tâches DistCp. Il n’y a probablement aucun gain de performance, mais cela répartit les tâches. Ainsi, si une tâche échoue, il vous suffit de redémarrer la tâche en question plutôt que l’ensemble complet.

### <a name="limitations"></a>Limites

* DistCp tente de créer des mappeurs similaires en taille pour optimiser les performances. L’augmentation du nombre de mappeurs n’améliore pas nécessairement les performances.

* DistCp est limité à un seul mappeur par fichier. Par conséquent, vous ne devez pas avoir plus de mappeurs que de fichiers. Étant donné que DistCp peut affecter un seul mappeur par fichier, cela limite la quantité d’exécutions simultanées utilisables pour copier des fichiers volumineux.

* Si vous avez un petit nombre de fichiers volumineux, vous devez les fractionner en plusieurs segments de fichier de 256 Mo pour avoir un meilleur potentiel de simultanéité. 
 
* Si vous copiez à partir d’un compte de stockage d’objets Blob Azure, votre tâche de copie peut être limitée sur le côté du stockage d’objets blob. Cela dégrade les performances de votre tâche de copie. Pour en savoir plus sur les limites du stockage Blob Azure, consultez les limites du stockage Azure sur [Abonnement Azure et limites du service](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Voir aussi
* [Copier des données d’objets blob Stockage Azure vers Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Sécuriser les données dans Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
