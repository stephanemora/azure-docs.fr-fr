---
title: Interroger des données depuis un stockage Azure compatible avec HDFS - Azure HDInsight
description: Découvrez comment interroger des données à partir du stockage Azure et d’Azure Data Lake Storage pour stocker les résultats de votre analyse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6e0192029decef95dcaecc0c60dce5fd5b6f99ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479907"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Utiliser le stockage Azure avec des clusters Azure HDInsight

Pour analyser des données dans un cluster HDInsight, vous pouvez stocker les données dans [Stockage Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Store Gen 2](../storage/blobs/data-lake-storage-introduction.md), ou une combinaison. Ces options de stockage vous permettent de supprimer des clusters HDInsight servant aux calculs, sans perte de données utilisateur.

Apache Hadoop prend en charge une notion de système de fichiers par défaut. Le système de fichiers par défaut implique un schéma et une autorité par défaut. Il peut également être utilisé pour résoudre les chemins d'accès relatifs. Pendant le processus de création du cluster HDInsight, vous pouvez spécifier un conteneur d’objets blob dans Stockage Azure comme système de fichiers par défaut ou, avec HDInsight 3.6, vous pouvez sélectionner Stockage Azure ou Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 en tant que système de fichiers par défaut avec quelques exceptions. Pour la prise en charge de l’utilisation de Data Lake Store Gen 1 en tant que stockage associé et par défaut, consultez [Disponibilités pour le cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Dans cet article, vous découvrez le fonctionnement du stockage Azure avec des clusters HDInsight. Pour savoir comment Data Lake Storage Gen 1 fonctionne avec les clusters HDInsight, consultez [Utiliser Azure Data Lake Storage avec des clusters HDInsight](hdinsight-hadoop-use-data-lake-store.md). Pour plus d’informations sur la création d’un cluster HDInsight, consultez [Créer des clusters Apache Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Le stockage Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. HDInsight peut utiliser un conteneur d’objets blob dans le stockage Azure comme système de fichiers par défaut pour le cluster. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées en tant qu’objets blob.

> [!WARNING]  
> Le type de compte de stockage **BlobStorage** peut uniquement être utilisé comme stockage secondaire pour les clusters HDInsight.

| Type de compte de stockage | Services pris en charge | Niveaux de performances pris en charge | Niveaux d’accès pris en charge |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (usage général v2)  | Blob     | standard                    | Chaud, froid, archive\*   |
| Stockage (v1 universel)   | Blob     | standard                    | N/A                    |
| BlobStorage                    | Blob     | standard                    | Chaud, froid, archive\*   |

Nous vous déconseillons d’utiliser le conteneur d’objets blob par défaut pour stocker des données d’entreprise. Nous vous recommandons de supprimer le conteneur d’objets blob par défaut après chaque utilisation pour réduire les coûts de stockage. Le conteneur par défaut contient les journaux des applications et du système. Assurez-vous de récupérer les journaux d’activité avant de supprimer le conteneur.

Le partage d’un conteneur blob en tant que système de fichiers par défaut sur plusieurs clusters n’est pas pris en charge.

> [!NOTE]  
> Le niveau d’accès archive est un niveau hors connexion dont la latence de récupération de plusieurs heures n’est pas recommandée pour une utilisation avec HDInsight. Pour plus d’informations, voir [Niveau d’accès archive](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Si vous choisissez de sécuriser votre compte de stockage à l’aide des restrictions de **pare-feu et réseaux virtuels** sur des **réseaux sélectionnés**, veillez à activer l’exception **Autoriser les services approuvés de Microsoft...** afin que HDInsight puisse accéder à votre compte de stockage.

## <a name="hdinsight-storage-architecture"></a>Architecture de stockage HDInsight
Le schéma suivant résume l’architecture de stockage HDInsight relative au Stockage Azure :

![Les clusters Hadoop utilisent l’API HDFS pour accéder aux données structurées et non structurées et les stocker dans le stockage d’objets blob.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architecture de stockage HDInsight")

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet, par exemple :

    hdfs://<namenodehost>/<path>

De plus, HDInsight permet d’accéder aux données stockées dans le stockage Azure. La syntaxe est :

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Voici des points à prendre en compte lorsque vous utilisez un compte de stockage Azure avec des clusters HDInsight.

* **Conteneurs dans les comptes de stockage connectés à un cluster :** Comme le nom et la clé du compte sont associés au cluster durant la création, vous disposez d’un accès complet aux blobs de ces conteneurs.

* **Conteneurs publics ou blobs publics dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous avez l’autorisation d’accès en lecture seule aux blobs dans les conteneurs.
  
> [!NOTE]  
> Des conteneurs publics vous permettent d'obtenir une liste de tous les objets blob disponibles, ainsi que ses métadonnées. Vous pouvez accéder aux objets blob d'un objet blob public uniquement si vous connaissez leur URL exacte. Pour plus d’informations, consultez [Gérer l’accès aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Conteneurs privés dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous ne pouvez pas accéder aux objets blob situés dans les conteneurs, sauf si vous définissez le compte de stockage lors de l'envoi des travaux WebHCat. Une explication sera fournie plus loin dans cet article.

Les comptes de stockage définis lors du processus de création et leurs clés sont stockés dans `%HADOOP_HOME%/conf/core-site.xml` sur les nœuds du cluster. Le comportement par défaut de HDInsight consiste à utiliser les comptes de stockage définis dans le fichier core-site.xml. Vous pouvez modifier ce paramètre avec [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Plusieurs travaux WebHCat, notamment Apache Hive, MapReduce, streaming Apache Hadoop et Apache Pig, peuvent véhiculer avec eux une description des comptes de stockage et des métadonnées. (cela fonctionne actuellement pour Pig, pour les comptes de stockage, mais pas pour les métadonnées.) Pour plus d'informations, consultez la page [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Les objets blob peuvent être utilisés pour les données structurées et non structurées. Les conteneurs d’objets blob stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser la barre oblique (« / ») dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire *input* , mais la barre oblique figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier.

## <a id="benefits"></a>Avantages du stockage Azure
La réduction des performances entraînée par la séparation des clusters de calcul et des ressources de stockage est compensée par le fait que les clusters de calcul sont créés à proximité des ressources du compte de stockage dans la région Azure, où le réseau à haut débit permet aux nœuds de calcul d’accéder efficacement aux données dans Stockage Azure.

Voici les avantages offerts par le stockage de données dans un stockage Azure au lieu d’un système HDFS :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Vous pouvez accéder aux données du stockage Azure via les API HDFS ou les [API REST de stockage Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Vous pouvez donc utiliser un plus grand nombre d'applications (notamment d'autres clusters HDInsight) et d'outils pour produire et consommer des données.

* **Archivage des données :** le stockage de données dans le stockage Azure permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur.

* **Coût de stockage des données :** le stockage à long terme des données dans DFS est plus coûteux que le stockage des données dans un stockage Azure, car le coût d’un cluster de calcul est plus élevé que celui d’un stockage Azure. De plus, comme vous n'avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.

* **Scale-out élastique :** même si le système HDFS offre un système de fichiers scale-out, l’échelle est déterminée par le nombre de nœuds que vous créez pour votre cluster. Au lieu de procéder ainsi, il est parfois plus simple de profiter des capacités d’évolution flexible que vous obtenez automatiquement dans le stockage Azure.

* **Géoréplication :** vous pouvez géorépliquer le stockage Azure. Si cette fonctionnalité permet la récupération géographique et la redondance des données, un basculement vers un emplacement géo-répliqué affecte sérieusement les performances et peut entraîner des frais supplémentaires. Nous vous recommandons donc de peser sérieusement le pour et le contre avant de choisir la géo-réplication.

Certains packages et travaux MapReduce peuvent créer des résultats intermédiaires que vous ne voulez pas stocker dans un stockage Azure. Dans ce cas, vous pouvez choisir de stocker les données dans un système HDFS local. En fait, HDInsight utilise DFS pour plusieurs de ces résultats intermédiaires dans les tâches Hive et d'autres processus.

> [!NOTE]  
> La plupart des commandes HDFS (par exemple, `ls`, `copyFromLocal` et `mkdir`) fonctionnent toujours comme prévu. Seules les commandes propres à l’implémentation HDFS native (ou DFS), comme `fschk` et `dfsadmin`, se comportent différemment dans le stockage Azure.

## <a name="address-files-in-azure-storage"></a>Adressage des fichiers dans le stockage Azure
Le modèle d’URI pour accéder aux fichiers du stockage Azure à partir de HDInsight est le suivant :

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Le modèle d'URI offre à la fois un accès non chiffré (avec le préfixe *wasb:* ) et un accès chiffré SSL (avec *wasbs*). Dans la mesure du possible, nous vous recommandons d’utiliser *wasbs* , même lorsqu’il s’agit d’accéder à des données qui résident dans la même région Azure.

`<BlobStorageContainerName>` identifie le nom du conteneur d’objets blob dans le stockage Azure.
`<StorageAccountName>` identifie le nom du compte Stockage Azure. Un nom de domaine complet (FQDN) est requis.

Si `<BlobStorageContainerName>` et `<StorageAccountName>` ne sont pas spécifiés, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, vous pouvez utiliser un chemin d'accès relatif ou absolu. Par exemple, le fichier *hadoop-mapreduce-examples.jar* fourni avec les clusters HDInsight peut être désigné pour l'une des utilisations suivantes :

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Le nom du fichier est `hadoop-examples.jar` sur les clusters HDInsight version 2.1 et 1.6.

Le chemin d'accès correspond au nom du chemin du fichier ou du répertoire HDFS. Comme les conteneurs du stockage Azure sont des magasins de valeurs de clés, il n'y a pas de système de fichiers hiérarchique. Une barre oblique (« / ») à l'intérieur d'une clé d'objet blob est interprétée comme un séparateur de répertoire. Par exemple, le nom d'objet blob pour *hadoop-mapreduce-examples.jar* est :

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Lorsque vous utilisez des objets blob hors de HDInsight, la plupart des utilitaires ne reconnaissent pas le format WASB et attendent plutôt un format de chemin d’accès basique, comme `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Conteneurs d’objets blob
Pour utiliser des objets blob, commencez par créer un [compte Stockage Azure](../storage/common/storage-create-storage-account.md). À cette étape, vous spécifiez une région Azure dans laquelle le compte de stockage est créé. Le cluster et le compte de stockage doivent être hébergés dans la même région. La base de données SQL Server de metastore Hive et la base de données SQL Server de metastore Apache Oozie doivent également se trouver dans la même région.

Où qu’il réside, chaque objet blob que vous créez appartient à un conteneur de votre compte de stockage Azure. Ce conteneur peut être un objet blob existant créé hors de HDInsight ou un conteneur créé pour un cluster HDInsight.

Le conteneur d’objets blob par défaut stocke les informations spécifiques de cluster telles que l’historique et les journaux d’activité des travaux. Ne partagez pas un conteneur d’objets blob par défaut avec plusieurs clusters HDInsight. Cela est susceptible d’endommager l’historique des travaux. Il est recommandé d’utiliser un conteneur différent pour chaque cluster et de placer des données partagées sur un compte de stockage lié spécifié dans le déploiement de tous les clusters pertinents plutôt que d’utiliser le compte de stockage par défaut. Pour plus d'informations sur la configuration des comptes de stockage liés, consultez [Créer des clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Vous pouvez, toutefois, réutiliser un conteneur de stockage par défaut une fois le cluster HDInsight d'origine supprimé. Pour les clusters HBase, vous pouvez conserver le schéma et les données de la table HBase en créant un cluster HBase à l’aide du conteneur d’objets blob par défaut utilisé par un cluster HBase ayant été supprimé.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interaction avec Stockage Azure

Microsoft fournit les outils suivants pour utiliser Stockage Azure :

| Outil | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Interface de ligne de commande Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Utiliser des comptes de stockage supplémentaires

Lorsque vous créez un cluster HDInsight, vous spécifiez le compte de stockage Azure que vous souhaitez lui associer. Outre ce compte de stockage, vous pouvez en ajouter d’autres à partir du même abonnement Azure ou à partir d’autres abonnements Azure pendant le processus de création ou à l’issue de la création d’un cluster. Pour en savoir plus sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> L’utilisation d’un compte de stockage supplémentaire dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser un stockage Azure compatible avec HDFS avec HDInsight. Ceci vous permet de créer des solutions à long terme et évolutives d’acquisition et d’archivage de données et d’utiliser HDInsight pour déverrouiller les informations des données structurées et non structurées stockées.

Pour plus d'informations, consultez les pages suivantes :

* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Bien démarrer avec Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Téléchargement de données vers HDInsight](hdinsight-upload-data.md)
* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utiliser des signatures d’accès partagé Stockage Azure pour restreindre l’accès aux données avec HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)