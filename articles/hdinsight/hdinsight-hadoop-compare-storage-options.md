---
title: Comparer les options de stockage à utiliser avec les clusters Azure HDInsight
description: Fournit une vue d’ensemble des types de stockage et de leur fonctionnement avec Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/20/2019
ms.openlocfilehash: 14db76068cc11d3f57a72e3e540a5e0da7e1c254
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853610"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparer les options de stockage à utiliser avec les clusters Azure HDInsight

Les utilisateurs Azure HDInsight peuvent choisir parmi plusieurs options de stockage lors de la création de clusters HDInsight :

* Azure Data Lake Storage Gen2
* Stockage Azure
* Azure Data Lake Storage Gen1

Cet article fournit une vue d’ensemble de ces différents types de stockage, ainsi que de leurs caractéristiques uniques.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Apache Hadoop dans Azure HDInsight

Pour plus d’informations sur Azure Data Lake Storage Gen2, consultez [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Azure Data Lake Storage Gen2 reprend les fonctionnalités principales d’Azure Data Lake Storage Gen1, comme un système de fichiers compatible Hadoop, Azure Active Directory et les listes de contrôle d’accès POSIX, et les intègre dans Stockage Blob Azure. Cette combinaison vous permet de tirer parti des performances d’Azure Data Lake Storage Gen1, tout en utilisant aussi la hiérarchisation et la gestion du cycle de vie des données de Stockage Blob.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Fonctionnalités principales d’Azure Data Lake Storage Gen2

* Accès compatible Hadoop : Azure Data Lake Storage Gen2 vous permet de gérer les données et d’y accéder comme vous le feriez avec un système de fichiers HDFS (Hadoop Distributed File System). Le pilote Azure Blob Filesystem (ABFS) est disponible dans tous les environnements Apache Hadoop, notamment Azure HDInsight et Azure Databricks, pour accéder aux données stockées dans Data Lake Storage Gen2.

* Surensemble d’autorisations POSIX : Le modèle de sécurité pour Data Lake Gen2 prend en charge les autorisations ACL et POSIX, ainsi que certaines granularités supplémentaires spécifiques à Data Lake Storage Gen2. Les paramètres peuvent être configurés via des outils d’administration ou des infrastructures comme Apache Hive et Apache Spark.

* Économique : Data Lake Storage Gen2 offre une capacité de stockage et des transactions à bas coût. Des fonctionnalités comme le cycle de vie de Stockage Blob Azure vous aident à réduire les coûts en adaptant les frais facturés au fil du cheminement des données dans son cycle de vie.

* Fonctionne avec les outils, les frameworks et les applications de Stockage Blob : Data Lake Storage Gen2 continue de fonctionner avec une large gamme d’outils, de frameworks et d’applications qui existent aujourd’hui pour Stockage Blob.

* Pilote optimisé : Le pilote ABFS est optimisé spécifiquement pour l’analytique Big Data. Les API REST correspondantes sont exposées via le point de terminaison dfs, dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Nouveautés d’Azure Data Lake Storage Gen2

#### <a name="managed-identities-for-secure-file-access"></a>Identités managées pour l’accès sécurisé aux fichiers

Azure HDInsight utilise des identités managées pour sécuriser l’accès du cluster aux fichiers dans Azure Data Lake Storage Gen2. Les identités managées sont une fonctionnalité d’Azure Active Directory qui fournit des services Azure avec un ensemble d’informations d’identification gérées automatiquement. Ces informations d’identification peuvent être utilisées pour s’authentifier auprès de n’importe quel service prenant en charge l’authentification Active Directory. L’utilisation des identités managées vous évite de stocker les informations d’identification dans les fichiers de code ou de configuration.

Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Pilote Azure Blob FileSystem (ABFS)

Les applications Apache Hadoop peuvent lire et écrire en mode natif des données dans un stockage sur disque local. Un pilote de système de fichiers Hadoop comme ABFS permet aux applications Hadoop d’utiliser un stockage cloud en émulant les opérations normales du système de fichiers Hadoop. Le pilote convertit les commandes reçues de l'application en opérations qui sont comprises par la plateforme de stockage cloud réelle.

Avant, le pilote de système de fichiers Hadoop convertissait toutes les opérations du système de fichiers en appels d’API REST Stockage Azure sur le côté client, puis appelait l’API REST. Cette conversion côté client entraînait cependant plusieurs appels d’API REST pour une même opération du système de fichiers, comme renommer un fichier. ABFS a déplacé une partie de la logique du système de fichiers Hadoop du côté client vers le côté serveur, et l’API Azure Data Lake Storage Gen2 s’exécute désormais en parallèle avec l’API Blob. Cette migration améliore les performances, car les opérations courantes du système de fichiers Hadoop peuvent désormais être exécutées avec un seul appel d’API REST.

Pour plus d’informations, consultez [Pilote ABFS (Azure Blob Filesystem) : pilote Stockage Azure dédié pour Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Schéma d'URI Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 utilise un nouveau schéma d’URI pour accéder aux fichiers de Stockage Azure à partir d’HDInsight :

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Le schéma d’URI fournit un accès SSL chiffré (préfixe `abfss://`) et un accès SSL non chiffré (préfixe `abfs://`). Nous vous recommandons d’utiliser `abfss` dans la mesure du possible, même pour accéder à des données qui se trouvent dans la même région Azure.

`<FILE_SYSTEM_NAME>` identifie le chemin du système de fichiers Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifie le nom du compte Stockage Azure. Un nom de domaine complet (FQDN) est requis.

`<PATH>` correspond au nom du chemin HDFS du fichier ou du répertoire.

Si vous ne spécifiez pas de valeurs pour `<FILE_SYSTEM_NAME>` et `<ACCOUNT_NAME>`, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, vous pouvez utiliser un chemin d'accès relatif ou absolu. Par exemple, vous pouvez référencer le fichier `hadoop-mapreduce-examples.jar` fourni avec les clusters HDInsight en utilisant un des chemins suivants :

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Le nom du fichier est `hadoop-examples.jar` sur les clusters HDInsight version 2.1 et 1.6. Quand vous utilisez des fichiers en dehors de HDInsight, la plupart des utilitaires ne reconnaissent pas le format ABFS et attendent plutôt un format de chemin de base, comme `example/jars/hadoop-mapreduce-examples.jar`.

Pour plus d’informations, consultez [Utiliser l’URI d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Utiliser le stockage Azure

Le stockage Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. HDInsight peut utiliser un conteneur d’objets blob dans le stockage Azure comme système de fichiers par défaut pour le cluster. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées en tant qu’objets blob.

> [!WARNING]  
> Plusieurs options sont disponibles lors de la création d’un compte de stockage Azure. Le tableau suivant fournit des informations sur les options prises en charge avec HDInsight :

| Type de compte de stockage | Services pris en charge | Niveaux de performances pris en charge | Niveaux d’accès pris en charge |
|----------------------|--------------------|-----------------------------|------------------------|
| Universel v2   | Blob               | standard                    | Chaud, froid, Archive*    |
| Universel v1   | Blob               | standard                    | N/A                    |
| Stockage d'objets blob         | Blob               | standard                    | Chaud, froid, archive*    |

Nous vous déconseillons d’utiliser le conteneur d’objets blob par défaut pour stocker des données d’entreprise. Nous vous recommandons de supprimer le conteneur d’objets blob par défaut après chaque utilisation pour réduire les coûts de stockage. Le conteneur par défaut contient les journaux des applications et du système. Assurez-vous de récupérer les journaux avant de supprimer le conteneur.

L'utilisation d’un conteneur d'objets blob en tant que système de fichiers par défaut sur plusieurs clusters n’est pas pris en charge.
 
 > [!NOTE]  
 > Le niveau d’accès archive est un niveau hors connexion dont la latence de récupération de plusieurs heures n’est pas recommandée pour une utilisation avec HDInsight. Pour plus d’informations, voir [Niveau d’accès archive](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>Architecture de stockage HDInsight
Le schéma suivant résume l’architecture de stockage HDInsight relative au Stockage Azure :

![Les clusters Hadoop utilisent l’API HDFS pour accéder aux données structurées et non structurées et les stocker dans le stockage d’objets blob.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "Architecture de stockage HDInsight")

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet, par exemple :

    hdfs://<namenodehost>/<path>

De plus, HDInsight permet d’accéder aux données stockées dans le stockage Azure. La syntaxe est :

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Voici des points à prendre en compte lorsque vous utilisez un compte de stockage Azure avec des clusters HDInsight.

* **Conteneurs dans les comptes de stockage connectés à un cluster :** Comme le nom et la clé du compte sont associés au cluster durant la création, vous disposez d’un accès complet aux blobs de ces conteneurs.

* **Conteneurs publics ou blobs publics dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous avez l’autorisation d’accès en lecture seule aux blobs dans les conteneurs.
  
  > [!NOTE]  
  > Des conteneurs publics vous permettent d'obtenir une liste de tous les objets blob disponibles, ainsi que ses métadonnées. Vous pouvez accéder aux objets blob d'un objet blob public uniquement si vous connaissez leur URL exacte. Pour plus d’informations, consultez [Gérer l’accès aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Conteneurs privés dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous ne pouvez pas accéder aux blobs se trouvant dans les conteneurs, sauf si vous définissez le compte de stockage quand vous envoyez des travaux WebHCat. Une explication sera fournie plus loin dans cet article.

Les comptes de stockage définis durant la création et leurs clés sont stockés dans %HADOOP_HOME%/conf/core-site.xml sur les nœuds du cluster. Le comportement par défaut de HDInsight consiste à utiliser les comptes de stockage définis dans le fichier core-site.xml. Vous pouvez modifier ce paramètre avec [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Plusieurs travaux WebHCat, notamment Apache Hive, MapReduce, streaming Apache Hadoop et Apache Pig, peuvent véhiculer avec eux une description des comptes de stockage et des métadonnées. (cela fonctionne actuellement pour Pig, pour les comptes de stockage, mais pas pour les métadonnées.) Pour plus d'informations, consultez la page [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Les objets blob peuvent être utilisés pour les données structurées et non structurées. Les conteneurs d’objets blob stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser la barre oblique (« / ») dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être `input/log1.txt`. Il n'existe pas de répertoire `input`, mais la barre oblique figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier.

### <a id="benefits"></a>Avantages du stockage Azure
La réduction des performances entraînée par la séparation des clusters de calcul et des ressources de stockage est compensée par le fait que les clusters de calcul sont créés à proximité des ressources du compte de stockage dans la région Azure, où le réseau à haut débit permet aux nœuds de calcul d’accéder efficacement aux données dans le stockage Azure.

Voici les avantages offerts par le stockage de données dans un stockage Azure au lieu d’un système HDFS :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Vous pouvez accéder aux données du stockage Azure via les API HDFS ou les API REST de stockage Blob. Vous pouvez donc utiliser un plus grand nombre d'applications (notamment d'autres clusters HDInsight) et d'outils pour produire et consommer des données.
* **Archivage des données :** le stockage de données dans le stockage Azure permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur.
* **Coût de stockage des données :** le stockage à long terme des données dans DFS est plus coûteux que le stockage des données dans un stockage Azure, car le coût d’un cluster de calcul est plus élevé que celui d’un stockage Azure. De plus, comme vous n'avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.
* **Scale-out élastique :** même si le système HDFS offre un système de fichiers scale-out, l’échelle est déterminée par le nombre de nœuds que vous créez pour votre cluster. Au lieu de procéder ainsi, il est parfois plus simple de profiter des capacités d’évolution flexible que vous obtenez automatiquement dans le stockage Azure.
* **Géoréplication :** vous pouvez géorépliquer le stockage Azure. Si cette fonctionnalité permet la récupération géographique et la redondance des données, un basculement vers un emplacement géo-répliqué affecte sérieusement les performances et peut entraîner des frais supplémentaires. Nous vous recommandons donc de peser sérieusement le pour et le contre avant de choisir la géo-réplication.

Certains packages et travaux MapReduce peuvent créer des résultats intermédiaires que vous ne voulez pas stocker dans un stockage Azure. Dans ce cas, vous pouvez choisir de stocker les données dans un système HDFS local. En fait, HDInsight utilise DFS pour plusieurs de ces résultats intermédiaires dans les tâches Hive et d'autres processus.

> [!NOTE]  
> La plupart des commandes HDFS (par exemple, `ls`, `copyFromLocal` et `mkdir`) fonctionnent toujours comme prévu. Seules les commandes propres à l’implémentation HDFS native (ou DFS), comme `fschk` et `dfsadmin`, se comportent différemment dans le stockage Azure.

## <a name="use-azure-data-lake-storage-gen1"></a>Utiliser Azure Data Lake Storage Gen1

Pour plus d’informations sur Azure Data Lake Store Gen1, consultez [Vue d’ensemble d'Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Azure Data Lake Storage Gen1 est un référentiel d’entreprise à très grande échelle pour les charges de travail d’analyse du Big Data. Azure Data Lake vous permet de capturer les données de toute taille, de tout type et à toute vitesse d'ingestion dans un emplacement unique en vue d'une analyse opérationnelle et exploratoire.

Data Lake Storage Gen1 est accessible à partir de Hadoop (disponible avec un cluster HDInsight) avec les API REST compatibles WebHDFS. Il est conçu pour permettre l'analyse des données stockées et met l'accent sur les performances des scénarios d'analyse des données. En standard, il inclut toutes les capacités de niveau entreprise (sécurité, facilité de gestion, évolutivité, fiabilité et disponibilité) essentielles pour les cas d'usage professionnels réels.

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "Architecture de stockage HDInsight")

Voici quelques-unes des fonctionnalités clés de Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Conçu pour Hadoop

Data Lake Storage Gen1 est un système de fichiers Apache Hadoop compatible avec HDFS (Hadoop Distributed File System) et fonctionnant avec l’écosystème Hadoop.  Vos applications ou services HDInsight existants, qui utilisent l’API WebHDFS, peuvent s’intégrer facilement à Data Lake Storage Gen1. Data Lake Storage Gen1 offre également une interface REST compatible WebHDFS pour les applications

Les données stockées dans Data Lake Storage Gen1 peuvent être facilement analysées avec les infrastructures d’analyse Hadoop, comme MapReduce ou Hive. Les clusters Microsoft Azure HDInsight peuvent être approvisionnés et configurés pour accéder directement aux données stockées dans Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Stockage illimité, fichiers de l’ordre du pétaoctet

Data Lake Storage Gen1 offre un stockage illimité et est approprié pour le stockage d’une grande variété de données à des fins d’analyse. Il n'impose aucune limite de taille de compte, de taille de fichier ou de quantité de données stockées dans un lac de données. La taille des fichiers individuels peut varier de l'ordre du kilooctet à l'ordre du pétaoctet, ce qui en fait le choix idéal pour stocker tout type de données. Les données sont stockées durablement en créant des copies multiples. De plus, il n'existe aucune limite de durée de stockage des données dans le lac de données.

### <a name="performance-tuned-for-big-data-analytics"></a>Performances optimisées pour l'analyse du Big Data

Data Lake Storage Gen1 est conçu pour faire fonctionner des systèmes d’analyse à grande échelle, nécessitant un débit élevé pour interroger et analyser de grands volumes de données. Le lac de données répartit les différentes parties des fichiers sur plusieurs serveurs de stockage distincts. Cela améliore le débit de lecture lors de la lecture du fichier en parallèle de l'analyse de données.

### <a name="enterprise-ready-highly-available-and-secure"></a>Adapté aux entreprises : sécurisé et hautement disponible

Data Lake Storage Gen1 offre une fiabilité et une disponibilité aux normes industrielles. Vos données sont stockées durablement en créant des copies redondantes pour vous prémunir contre toute défaillance imprévue. Les entreprises peuvent utiliser Data Lake Storage Gen1 dans leurs solutions et l’intégrer à leur plateforme de données existante.

Data Lake Storage Gen1 fournit également une sécurité de niveau entreprise des données stockées. Pour plus d’informations, consultez [Sécurisation des données dans Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Toutes les données

Data Lake Storage Gen1 peut stocker des données dans leur format natif, en l’état, sans nécessiter de transformations préalables. Data Lake Storage Gen1 ne requiert pas la définition d’un schéma avant le chargement des données, laissant ainsi à l’infrastructure d’analyse individuelle le soin d’interpréter les données et de définir un schéma au moment de l’analyse. La possibilité de stocker les fichiers de formats et de tailles arbitraires rend possible la gestion par Data Lake Storage Gen1 de données structurées, semi-structurées et non structurées.

Les conteneurs Data Lake Storage Gen1 pour données sont essentiellement des dossiers et des fichiers. Vous travaillez sur les données stockées à l'aide de kits de développement logiciel (SDK), du portail Azure et d’Azure Powershell. Tant que vous placez vos données dans le magasin en utilisant ces interfaces et les conteneurs appropriés, vous pouvez stocker n'importe quel type de données. Data Lake Storage Gen1 n’effectue aucune opération particulière sur les données en fonction du type de données qu’il contient.

## <a name="DataLakeStoreSecurity"></a>Sécurisation des données dans Data Lake Storage Gen1
Data Lake Storage Gen1 utilise Azure Active Directory pour l’authentification et les listes de contrôle d’accès pour gérer l’accès à vos données.

| Fonctionnalité | Description |
| --- | --- |
| Authentification |Data Lake Storage Gen1 s’intègre à Azure Active Directory (AAD) pour la gestion des identités et des accès de toutes les données stockées dans Data Lake Storage Gen1. Grâce à cette intégration, Data Lake Storage Gen1 tire parti de toutes les fonctionnalités d’AAD, notamment l’authentification multifacteur, l’accès conditionnel, le contrôle d’accès en fonction du rôle, l’analyse d’utilisation des applications, la surveillance de la sécurité et les alertes, etc. Data Lake Storage Gen1 prend en charge le protocole OAuth 2.0 pour l’authentification auprès de l’interface REST. Voir [Data Lake Storage Gen1 authentication](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md) (Authentification Data Lake Storage Gen1)|
| Contrôle d’accès |Data Lake Storage Gen1 offre un contrôle d’accès grâce à la prise en charge des autorisations de type POSIX exposées par le protocole WebHDFS. Les ACL peuvent être activées au niveau du dossier racine, des sous-dossiers et des fichiers individuels. Pour plus d’informations sur le fonctionnement des ACL dans le contexte de Data Lake Storage Gen1, consultez [Contrôle d’accès dans Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Chiffrement |Data Lake Storage Gen1 assure également le chiffrement des données stockées dans le compte. Vous spécifiez les paramètres de chiffrement lors de la création d’un compte Data Lake Storage Gen1. Vous pouvez choisir de chiffrer vos données ou non. Pour plus d’informations, consultez l’article [Chiffrement dans Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Pour savoir comment définir une configuration avec chiffrement, consultez [Prise en main d’Azure Data Lake Storage Gen1 à l’aide du Portail Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Vous souhaitez en savoir plus sur la sécurisation des données dans Data Lake Storage Gen1 ? Suivez les liens ci-dessous.

* Pour savoir comment sécuriser les données dans Data Lake Storage Gen1, consultez [Sécurisation des données dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Applications compatibles avec Data Lake Storage Gen1
Data Lake Storage Gen1 est compatible avec la plupart des composants open source de l’écosystème Hadoop. Par ailleurs, il s’intègre facilement avec d’autres services Azure.  Suivez les liens ci-dessous pour découvrir comment Data Lake Storage Gen1 peut être utilisé avec les composants open source ainsi que d’autres services Azure.

* Consultez [Applications et services compatibles avec Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) pour obtenir une liste des applications open source interopérables avec Data Lake Storage Gen1.
* Consultez [Intégration avec d’autres services Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) pour comprendre comment Data Lake Storage Gen1 peut être utilisé avec d’autres services Azure afin d’augmenter le nombre de scénarios.
* Consultez la page [Scénarios de données impliquant Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) pour découvrir comment utiliser Data Lake Storage Gen1 pour des scénarios tels que la réception, le traitement, le téléchargement et la visualisation de données.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Présentation du système de fichiers Data Lake Storage Gen1 (adl://)
Data Lake Storage Gen1 est accessible via le nouveau système de fichiers AzureDataLakeFilesystem (adl://) dans les environnements Hadoop (disponible avec le cluster HDInsight). Les applications et les services qui utilisent adl:// peuvent tirer parti d'autres optimisations des performances, qui ne sont actuellement pas disponibles dans WebHDFS. Par conséquent, Data Lake Storage Gen1 vous laisse la liberté, soit de bénéficier de performances optimales en utilisant adl:// (option recommandée), soit de maintenir le code existant en continuant d’utiliser l’API WebHDFS directement. Azure HDInsight tire pleinement parti du système de fichiers AzureDataLakeFilesystem pour optimiser les performances sur Data Lake Storage Gen1.

Vous pouvez accéder à vos données dans Data Lake Storage Gen1 à l’aide de `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Pour plus d’informations sur la façon d’accéder aux données dans Data Lake Storage Gen1, consultez [Afficher les propriétés des données stockées](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
* [Introduction à Azure Storage](../storage/common/storage-introduction.md)