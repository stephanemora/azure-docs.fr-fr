---
title: Utiliser Azure Data Lake Storage Gen2 (préversion) avec des clusters Azure HDInsight
description: Découvrez comment interroger des données à partir d’Azure Data Lake Storage Gen2 (préversion) et à stocker les résultats de votre analyse.
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: ed7242a254064ceada05ed40a88e8f2ebabdf57a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251229"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Utiliser Azure Data Lake Storage Gen2 (préversion) avec des clusters Azure HDInsight

Pour analyser les données dans un cluster HDInsight, vous pouvez stocker les données dans n’importe quelle combinaison de stockage Blob Azure, Azure Blob Storage avec Azure Data Lake Storage Gen2 ou Azure Data Lake Storage Gen1. Toutes les options de stockage vous permettent de supprimer de manière sécurisée des clusters HDInsight servant au calcul, sans perte de données utilisateur.

Hadoop prend en charge une notion de système de fichiers par défaut. Le système de fichiers par défaut implique un schéma et une autorité par défaut. Il peut également être utilisé pour résoudre les chemins d'accès relatifs. Pendant le processus de création du cluster HDInsight, vous pouvez spécifier un conteneur d’objets blob dans le stockage Azure ou l'espace de noms hiérarchique offert par Azure Data Lake Storage Gen2 comme système de fichiers par défaut. Sinon, utilisez HDInsight 3.5 pour sélectionner un conteneur ou un espace de noms hiérarchique comme système de fichiers par défaut, avec quelques exceptions.

Dans cet article, vous découvrez le fonctionnement de Data Lake Storage Gen2 avec des clusters HDInsight. Pour plus d’informations sur la création d’un cluster HDInsight, consultez [Configurer des clusters HDInsight à l’aide d’Azure Data Lake Storage avec Hadoop, Spark, Kafka et bien plus encore](data-lake-storage-quickstart-create-connect-hdi-cluster.md).

Le stockage Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. HDInsight peut utiliser Azure Data Lake Storage comme système de fichiers par défaut pour le cluster. Grâce à une interface HDFS (système de fichiers DFS hadoop), l’ensemble des composants de HDInsight peut fonctionner directement sur les fichiers dans Azure Data Lake Storage.

Nous vous déconseillons d’utiliser le système de fichiers par défaut pour stocker des données métier. Supprimez le système de fichiers par défaut après chaque utilisation pour réduire les coûts de stockage. Notez que le conteneur par défaut contient les journaux système et les journaux des applications. Assurez-vous de récupérer les journaux avant de supprimer le conteneur.

Le partage d’un système de fichiers entre plusieurs clusters n’est pas pris en charge.

## <a name="hdinsight-storage-architecture"></a>Architecture de stockage HDInsight

Le schéma suivant résume l’architecture de stockage HDInsight relative au Stockage Azure :

![Les clusters Hadoop utilisent l’API HDFS pour accéder aux données structurées et non structurées et les stocker dans le stockage d’objets blob.](./media/data-lake-storage-use-hdi-cluster/HDI.ABFS.Arch.png "Architecture de stockage HDInsight")

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet, par exemple :

    hdfs://<NAME_NODE_HOST>/<PATH>

Par ailleurs, HDInsight permet d’accéder aux données stockées dans Azure Data Lake Storage. La syntaxe est :

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

Voici des points à prendre en compte quand vous utilisez un compte de stockage Azure avec des clusters HDInsight.

* Les **fichiers dans les comptes de stockage qui sont connectés à un cluster** ont le nom de compte et la clé associés au cluster au moment de la création. Cette configuration vous donne un accès complet aux fichiers du système de fichiers.

* Les **fichiers publics dans les comptes de stockage qui ne sont PAS connectés à un cluster** exposent des autorisations en lecture seule pour les fichiers du système de fichiers.
  
  > [!NOTE]
  > Les systèmes de fichiers publics permettent d’obtenir la liste de tous les fichiers disponibles dans le système de fichiers et d’accéder aux métadonnées. Les systèmes de fichiers publics vous permettent d’accéder aux fichiers uniquement si vous connaissez l’URL exacte. Pour plus d’informations, consultez [Limiter l’accès aux conteneurs et aux objets blob](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (les règles pour les conteneurs et les objets blob fonctionnent de la même façon pour les fichiers et les système de fichiers).
 
* Les **système de fichiers privés dans les comptes de stockage qui ne sont PAS connectés à un cluster** ne vous permettent pas d’accéder aux fichiers du système de fichiers, sauf si vous définissez le compte de stockage quand vous envoyez les travaux WebHCat. Les raisons de cette restriction sont expliquées plus loin dans cet article.

Les comptes de stockage définis durant la création et leurs clés sont stockés dans *%HADOOP_HOME%/conf/core-site.xml* sur les nœuds du cluster. Le comportement par défaut de HDInsight consiste à utiliser les comptes de stockage définis dans le fichier *core-site.xml*. Vous pouvez modifier ce paramètre avec [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md).

Plusieurs tâches WebHCat, notamment Hive, MapReduce, la diffusion en continu Hadoop et Pig, peuvent véhiculer avec elles une description des comptes de stockage et des métadonnées. (Cette approche fonctionne actuellement pour Pig avec des comptes de stockage, mais pas pour les métadonnées.) Pour plus d'informations, consultez la page [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

## <a id="benefits"></a>Avantages du stockage Azure

La réduction des performances entraînée par la séparation des clusters de calcul et des ressources de stockage est compensée par le fait que les clusters de calcul sont créés à proximité des ressources du compte de stockage dans la région Azure, où le réseau à haut débit permet aux nœuds de calcul d’accéder efficacement aux données dans le stockage Azure.

Voici les avantages offerts par le stockage de données dans un stockage Azure au lieu d’un système HDFS :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Vous pouvez accéder aux données du stockage Azure via les API HDFS ou les [API REST de stockage Blob][blob-storage-restAPI]. Vous pouvez donc utiliser un plus grand nombre d'applications (notamment d'autres clusters HDInsight) et d'outils pour produire et consommer des données.

* **Archivage des données :** le stockage de données dans le stockage Azure permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur.

* **Coût de stockage des données :** le stockage à long terme des données dans le HDFS natif est plus coûteux que le stockage des données dans un stockage Azure, car le coût d’un cluster de calcul est plus élevé que celui d’un stockage Azure. De plus, comme vous n'avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.

* **Scale-out élastique :** même si le système HDFS offre un système de fichiers scale-out, l’échelle est déterminée par le nombre de nœuds que vous créez pour votre cluster. Au lieu de procéder ainsi, il est parfois plus simple de profiter des capacités d’évolution flexible que vous obtenez automatiquement dans le stockage Azure.

* **Géoréplication :** vous pouvez géorépliquer vos données de stockage Azure. Bien que cette possibilité permette la récupération géographique et la redondance des données, la prise en charge d’un basculement vers un emplacement géo-répliqué impacte sérieusement les performances et peut entraîner des frais supplémentaires. Ainsi, choisissez la géoréplication avec précaution et uniquement si la valeur des données justifie le coût supplémentaire.

* **Gestion du cycle de vie des données :** toutes les données dans n’importe quel système de fichiers ont leur propre cycle de vie. Au départ, les données sont souvent très précieuses et fréquemment sollicitées, puis perdent en valeur et en exigence d’accessibilité, avant d’être finalement archivées ou supprimées. Le Stockage Azure fournit des stratégies de hiérarchisation des données et de gestion du cycle de vie qui trient les données conformément à leur phase de cycle de vie.

Certains packages et travaux MapReduce peuvent créer des résultats intermédiaires que vous ne voulez pas stocker dans un stockage Azure. Dans ce cas, vous pouvez choisir de stocker les données dans un système HDFS local. En fait, HDInsight utilise l’implémentation HDFS native (ou DFS) pour plusieurs de ces résultats intermédiaires dans les travaux Hive et d’autres processus.

> [!NOTE]
> La plupart des commandes HDFS (par exemple, `ls`, `copyFromLocal` et `mkdir`) fonctionnent toujours comme prévu. Seules les commandes propres au DFS, comme `fschk` et `dfsadmin`, se comportent différemment dans le stockage Azure.

## <a name="create-a-data-lake-storage-file-system"></a>Créer un système de fichiers Data Lake Storage

Pour utiliser le système de fichiers, vous créez d’abord un [compte de stockage Azure][azure-storage-create]. Dans le cadre de ce processus, vous spécifiez une région Azure dans laquelle le compte de stockage est créé. Le cluster et le compte de stockage doivent être hébergés dans la même région. La base de données SQL Server de metastore Hive et la base de données SQL Server de metastore Oozie doivent également se trouver dans la même région.

Où qu’il réside, chaque objet blob que vous créez appartient à un système de fichiers de votre compte de stockage.

Le système de fichiers Data Lake Storage Gen2 par défaut stocke les informations spécifiques de cluster comme l’historique et les journaux des travaux. Ne partagez pas un système de fichiers Data Lake Storage Gen2 par défaut avec plusieurs clusters HDInsight. Cela est susceptible d’endommager l’historique des travaux. Nous vous conseillons d’utiliser un système de fichiers différent pour chaque cluster et de placer les données partagées sur un compte de stockage lié spécifié dans le déploiement de tous les clusters pertinents, plutôt que d’utiliser le compte de stockage par défaut. Pour plus d'informations sur la configuration des comptes de stockage liés, consultez la rubrique [Création de clusters HDInsight][hdinsight-creation]. Vous pouvez, toutefois, réutiliser un système de fichiers de stockage par défaut une fois le cluster HDInsight d’origine supprimé. Pour les clusters HBase, vous pouvez conserver le schéma et les données de la table HBase en créant un cluster HBase à l’aide du conteneur d’objets blob par défaut utilisé par un cluster HBase supprimé.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Quand vous créez un cluster HDInsight à partir du portail, vous avez la possibilité (comme indiqué dans la capture d’écran suivante) de fournir les détails du compte de stockage. Vous pouvez également spécifier si vous voulez associer un compte de stockage supplémentaire au cluster et, si c’est le cas, choisir une option de stockage disponible pour le stockage supplémentaire.

![source de données de création hadoop HDInsight](./media/data-lake-storage-use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> L’utilisation d’un compte de stockage supplémentaire dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si vous avez [installé et configuré Azure PowerShell][powershell-install], vous pouvez utiliser le code suivant dans l’invite Azure PowerShell pour créer un compte de stockage et un conteneur :

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

```azurepowershell
$SubscriptionID = "<Your Azure Subscription ID>"
$ResourceGroupName = "<New Azure Resource Group Name>"
$Location = "WEST US 2"

$StorageAccountName = "<New Azure Storage Account Name>"
$containerName = "<New Azure Blob Container Name>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubscriptionID

# Create resource group
New-AzResourceGroup -name $ResourceGroupName -Location $Location

# Create default storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
  -Name StorageAccountName `
  -Location $Location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
  -HierarchialNamespace $True

# Create default blob containers
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
$destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
New-AzStorageContainer -Name $containerName -Context $destContext
```

> [!NOTE]
> La création d’un conteneur revient à créer un système de fichiers dans Data Lake Storage Gen2.

### <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Si vous avez [installé et configuré l’interface de ligne de commande Azure](../../cli-install-nodejs.md), la commande suivante peut être utilisée sur un compte de stockage et un conteneur.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> Pendant la préversion publique de Data Lake Storage Gen2, seul `--sku Standard_LRS` est pris en charge.

Vous devez spécifier la région géographique dans laquelle est créé le compte de stockage. Créez le compte de stockage dans la même région où vous voulez créer votre cluster HDInsight.

Une fois le compte de stockage créé, utilisez la commande suivante pour récupérer les clés du compte de stockage :

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Pour créer un conteneur, utilisez la commande suivante :

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> La création d’un conteneur revient à créer un système de fichiers dans Data Lake Storage Gen2.

## <a name="address-files-in-azure-storage"></a>Adressage des fichiers dans le stockage Azure

Le modèle d’URI pour accéder aux fichiers du stockage Azure à partir de HDInsight est le suivant :

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

Le schéma d’URI offre un accès non chiffré (avec le préfixe *abfs:*) et un accès chiffré SSL (avec *abfss*). Dans la mesure du possible, nous vous recommandons d’utiliser *abfss*, même s’il s’agit d’accéder à des données qui résident dans la même région Azure.

* &lt;FILE_SYSTEM_NAME&gt; identifie le chemin du système de fichiers Data Lake Storage Gen2.
* &lt;ACCOUNT_NAME&gt; identifie le nom du compte de stockage Azure. Un nom de domaine complet (FQDN) est requis.

    Si les valeurs de &lt;FILE_SYSTEM_NAME&gt; ou &lt;ACCOUNT_NAME&gt; n’ont pas été spécifiées, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, vous pouvez utiliser un chemin d'accès relatif ou absolu. Par exemple, le fichier *hadoop-mapreduce-examples.jar* fourni avec les clusters HDInsight peut être désigné à l’aide d’un des chemins suivants :
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Le nom du fichier est *hadoop-examples.jar* sur les clusters HDInsight version 2.1 et 1.6.

* Le &lt;PATH&gt; correspond au nom du chemin du fichier ou du répertoire HDFS.

> [!NOTE]
> Quand vous utilisez des fichiers en dehors de HDInsight, la plupart des utilitaires ne reconnaissent pas le format ABFS et attendent plutôt un format de chemin de base, comme `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>Utiliser des comptes de stockage supplémentaires

Lorsque vous créez un cluster HDInsight, vous spécifiez le compte de stockage Azure que vous souhaitez lui associer. Outre ce compte de stockage, vous pouvez en ajouter d’autres à partir du même abonnement Azure ou à partir d’autres abonnements Azure pendant le processus de création ou à l’issue de la création d’un cluster. Pour en savoir plus sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Création de clusters HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> L’utilisation d’un compte de stockage supplémentaire dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser un stockage Azure compatible avec HDFS avec HDInsight. Cette approche vous permet de créer des solutions à long terme et scalables d’acquisition et d’archivage de données, et d’utiliser HDInsight pour déverrouiller les informations des données structurées et non structurées stockées.

Pour plus d'informations, consultez les pages suivantes :

* [Pilote de système de fichiers ABFS Hadoop pour Azure Data Lake Storage Gen2 (préversion)](data-lake-storage-abfs-driver.md)
* [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Configurer des clusters HDInsight à l’aide d’Azure Data Lake Storage Gen2 avec Hadoop, Spark, Kafka et bien plus encore](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
* [Ingérer des données dans Azure Data Lake Storage Gen2 à l’aide de distcp](data-lake-storage-use-distcp.md)

[powershell-install]: /powershell/azure/install-az-ps
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/data-lake-storage-use-hdi-cluster/HDI.PowerShell.BlobCommands.png
