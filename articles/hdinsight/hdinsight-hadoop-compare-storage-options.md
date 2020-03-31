---
title: Comparer les options de stockage à utiliser avec les clusters Azure HDInsight
description: Fournit une vue d’ensemble des types de stockage et de leur fonctionnement avec Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095553"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparer les options de stockage à utiliser avec les clusters Azure HDInsight

Vous pouvez choisir parmi plusieurs services de stockage Azure lors de la création de clusters HDInsight :

* Stockage Azure
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Cet article fournit une vue d’ensemble de ces types de stockage, ainsi que de leurs caractéristiques uniques.

Le tableau suivant récapitule les services de stockage Azure pris en charge avec les différentes versions de HDInsight :

| Service de stockage | Type de compte | Type d’espace de noms | Services pris en charge | Niveaux de performances pris en charge | Niveaux d’accès pris en charge | Version de HDInsight | Type de cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Universel v2 | Hiérarchique (système de fichiers) | Objet blob | standard | Chaud, froid, archive | 3.6+ | Tout sauf Spark 2.1 et 2.2|
|Stockage Azure| Universel v2 | Object | Objet blob | standard | Chaud, froid, archive | 3.6+ | Tous |
|Stockage Azure| Universel v1 | Object | Objet blob | standard | N/A | Tous | Tous |
|Stockage Azure| Stockage Blob** | Object | Objet blob de blocs | standard | Chaud, froid, archive | Tous | Tous |
|Azure Data Lake Storage Gen1| N/A | Hiérarchique (système de fichiers) | N/A | N/A | N/A | 3.6 uniquement | Tout sauf HBase |

** Pour les clusters HDInsight, seuls les comptes de stockage secondaires peuvent être de type BlobStorage. Par ailleurs, les objets blob de pages ne font pas partie des options de stockage prises en charge.

Pour plus d’informations sur les types de comptes de stockage Azure, consultez l’article [Vue d’ensemble des comptes de stockage Azure](../storage/common/storage-account-overview.md).

Pour plus d’informations sur les niveaux d’accès du stockage Azure, consultez [Stockage Blob Azure : niveaux de stockage Premium (préversion), chaud, froid et archive](../storage/blobs/storage-blob-storage-tiers.md)

Vous pouvez créer un cluster à l’aide de différentes combinaisons de services pour le stockage secondaire principal et facultatif. Le tableau suivant résume les configurations de stockage de cluster actuellement prises en charge dans HDInsight :

| Version de HDInsight | Stockage principal | Stockage secondaire | Prise en charge |
|---|---|---|---|
| 3.6 et 4.0 | Usage général V1, usage général V2 | Usage général V1, usage général V2, BlobStorage (objets BLOB de blocs) | Oui |
| 3.6 et 4.0 | Usage général V1, usage général V2 | Data Lake Storage Gen2 | Non |
| 3.6 et 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Oui |
| 3.6 et 4.0 | Data Lake Storage Gen2* | Usage général V1, usage général V2, BlobStorage (objets BLOB de blocs) | Oui |
| 3.6 et 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen 1 | Non |
| 3.6 | Data Lake Storage Gen 1 | Data Lake Storage Gen 1 | Oui |
| 3.6 | Data Lake Storage Gen 1 | Usage général V1, usage général V2, BlobStorage (objets BLOB de blocs) | Oui |
| 3.6 | Data Lake Storage Gen 1 | Data Lake Storage Gen2 | Non |
| 4.0 | Data Lake Storage Gen 1 | Quelconque | Non |
| 4.0 | Usage général V1, usage général V2 | Data Lake Storage Gen 1 | Non |

\* = Peut être un ou plusieurs comptes Data Lake Storage Gen2, tant qu’ils sont tous configurés pour utiliser la même identité gérée pour l’accès au cluster.

> [!Note] 
> Le stockage principal Data Lake Storage Gen2 n’est pas pris en charge pour les clusters Spark 2.1 ou 2.2. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Apache Hadoop dans Azure HDInsight

Azure Data Lake Storage Gen2 offre les principales fonctionnalités d’Azure Data Lake Storage Gen1 et les intègre au Stockage Blob Azure. Ces fonctionnalités incluent un système de fichiers compatible avec Hadoop, Azure Active Directory (Azure AD) et les listes de contrôle d’accès (ACL) POSIX. Cette combinaison vous permet de tirer parti des performances d’Azure Data Lake Storage Gen1, tout en utilisant la hiérarchisation et la gestion du cycle de vie des données de Stockage Blob.

Pour plus d’informations sur Azure Data Lake Storage Gen2, consultez [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Fonctionnalités principales d’Azure Data Lake Storage Gen2

* **Accès compatible avec Hadoop :** dans Azure Data Lake Storage Gen2, vous pouvez gérer les données et y accéder comme vous le feriez avec un système de fichiers HDFS (Hadoop Distributed File System). Le pilote ABFS (Azure Blob Filesystem) est disponible dans tous les environnements Apache Hadoop, notamment Azure HDInsight et Azure Databricks. Utilisez ABFS pour accéder aux données stockées dans Data Lake Storage Gen2.

* **Surensemble d’autorisations POSIX :** Le modèle de sécurité pour Data Lake Gen2 prend en charge les autorisations ACL et POSIX, ainsi que certaines granularités supplémentaires spécifiques à Data Lake Storage Gen2. Les paramètres peuvent être configurés par le biais d’outils d’administration ou de frameworks comme Apache Hive et Apache Spark.

* **Rentabilité :** Data Lake Storage Gen2 offre une capacité de stockage et des transactions à bas coût. Des fonctionnalités comme le cycle de vie de Stockage Blob Azure vous aident à réduire les coûts en adaptant les frais facturés au fil du cheminement des données dans son cycle de vie.

* **Compatibilité avec les outils, les frameworks et les applications de Stockage Blob :** Data Lake Storage Gen2 continue de fonctionner avec une large gamme d’outils, de frameworks et d’applications pour Stockage Blob.

* **Pilote optimisé :** Le pilote ABFS est optimisé spécifiquement pour l’analytique Big Data. Les API REST correspondantes sont exposées par le biais du point de terminaison DFS (Distributed File System), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Nouveautés d’Azure Data Lake Storage Gen2

#### <a name="managed-identities-for-secure-file-access"></a>Identités managées pour l’accès sécurisé aux fichiers

Azure HDInsight utilise des identités managées pour sécuriser l’accès du cluster aux fichiers dans Azure Data Lake Storage Gen2. Les identités managées sont une fonctionnalité d’Azure Active Directory qui fournit des services Azure avec un ensemble d’informations d’identification gérées automatiquement. Ces informations d’identification peuvent être utilisées pour s’authentifier auprès de n’importe quel service prenant en charge l’authentification Active Directory. L’utilisation des identités managées vous évite de stocker les informations d’identification dans les fichiers de code ou de configuration.

Pour plus d’informations, voir [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Pilote Azure Blob File System

Les applications Apache Hadoop peuvent lire et écrire en mode natif des données dans un stockage sur disque local. Un pilote de système de fichiers Hadoop comme ABFS permet aux applications Hadoop d’utiliser un stockage cloud en émulant les opérations normales du système de fichiers Hadoop. Le pilote convertit les commandes reçues de l'application en opérations qui sont comprises par la plateforme de stockage cloud réelle.

Avant, le pilote de système de fichiers Hadoop convertissait toutes les opérations du système de fichiers en appels d’API REST Stockage Azure du côté client, puis appelait l’API REST. Cette conversion côté client entraînait cependant plusieurs appels d’API REST pour une même opération du système de fichiers, comme le renommage d’un fichier. ABFS a déplacé une partie de la logique de système de fichiers Hadoop du côté client vers le côté serveur. L’API Azure Data Lake Storage Gen2 s’exécute désormais en parallèle avec l’API d’objet blob. Cette migration améliore les performances, car les opérations courantes du système de fichiers Hadoop peuvent désormais être exécutées avec un seul appel d’API REST.

Pour plus d’informations, consultez [Pilote ABFS (Azure Blob Filesystem) : pilote Stockage Azure dédié pour Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schéma d’URI Azure Data Lake Storage Gen2 

Azure Data Lake Storage Gen2 utilise un nouveau schéma d’URI pour accéder aux fichiers de Stockage Azure à partir d’HDInsight :

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Le schéma d’URI fournit un accès chiffré SSL.

`<FILE_SYSTEM_NAME>` identifie le chemin du système de fichiers Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifie le nom du compte Stockage Azure. Un nom de domaine complet (FQDN) est requis.

`<PATH>` correspond au nom du chemin HDFS du fichier ou du répertoire.

Si vous ne spécifiez pas de valeurs pour `<FILE_SYSTEM_NAME>` et `<ACCOUNT_NAME>`, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, utilisez un chemin relatif ou absolu. Par exemple, vous pouvez référencer le fichier `hadoop-mapreduce-examples.jar` fourni avec les clusters HDInsight en utilisant un des chemins suivants :

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Le nom du fichier est `hadoop-examples.jar` sur les clusters HDInsight version 2.1 et 1.6. Quand vous utilisez des fichiers en dehors de HDInsight, vous constaterez que la plupart des utilitaires ne reconnaissent pas le format ABFS et attendent plutôt un format de chemin de base, comme `example/jars/hadoop-mapreduce-examples.jar`.

Pour plus d’informations, consultez [Utiliser l’URI d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Stockage Azure

Stockage Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. HDInsight peut utiliser un conteneur d’objets blob dans le stockage Azure comme système de fichiers par défaut pour le cluster. Grâce à une interface HDFS, l’ensemble des composants de HDInsight peut opérer directement sur les données structurées ou non structurées stockées en tant qu’objets blob.

Nous vous recommandons d’utiliser des conteneurs de stockage distincts pour votre stockage de cluster par défaut et vos données d’entreprise, pour isoler les journaux de HDInsight et les fichiers temporaires de vos propres données d’entreprise. Nous recommandons également de supprimer le conteneur d’objets blob par défaut, qui contient les journaux des applications et système, après chaque utilisation pour réduire le coût de stockage. Assurez-vous de récupérer les journaux d’activité avant de supprimer le conteneur.

Si vous choisissez de sécuriser votre compte de stockage à l’aide des restrictions de **pare-feu et réseaux virtuels** sur des **réseaux sélectionnés**, veillez à activer l’exception **Autoriser les services approuvés de Microsoft...** afin que HDInsight puisse accéder à votre compte de stockage.

### <a name="hdinsight-storage-architecture"></a>Architecture de stockage HDInsight

Le schéma suivant résume l’architecture HDInsight de Stockage Azure :

![Architecture de stockage HDInsight](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Architecture de stockage HDInsight")

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet, par exemple :

    hdfs://<namenodehost>/<path>

Avec HDInsight, vous pouvez également accéder aux données dans Stockage Azure. La syntaxe est la suivante :

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Voici quelques points à prendre en compte quand vous utilisez un compte Stockage Azure avec des clusters HDInsight :

* **Conteneurs dans les comptes de stockage connectés à un cluster :** Comme le nom et la clé du compte sont associés au cluster durant la création, vous disposez d’un accès complet aux blobs de ces conteneurs.

* **Conteneurs publics ou objets blob publics dans les comptes de stockage qui ne sont *pas* connectés à un cluster :** vous avez l’autorisation d’accès en lecture seule aux blobs dans les conteneurs.
  
  > [!NOTE]  
  > Les conteneurs publics vous permettent d’obtenir une liste de tous les objets blob disponibles dans ce conteneur, ainsi que ses métadonnées. Vous pouvez accéder aux objets blob d'un objet blob public uniquement si vous connaissez leur URL exacte. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Conteneurs privés dans les comptes de stockage qui ne sont *pas* connectés à un cluster :** vous ne pouvez pas accéder aux blobs se trouvant dans les conteneurs, sauf si vous définissez le compte de stockage quand vous envoyez des travaux WebHCat. 

Les comptes de stockage définis durant la création et leurs clés sont stockés dans %HADOOP_HOME%/conf/core-site.xml sur les nœuds du cluster. Par défaut, HDInsight utilise les comptes de stockage définis dans le fichier core-site.Xml. Vous pouvez modifier ce paramètre avec [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Plusieurs travaux WebHCat, notamment Apache Hive, MapReduce, streaming Apache Hadoop et Apache Pig, peuvent véhiculer avec eux une description des comptes de stockage et des métadonnées. (Cela est actuellement valable pour Pig avec les comptes de stockage, mais pas pour les métadonnées.) Pour plus d’informations, consultez [Utilisation d’un cluster HDInsight avec des comptes de stockage et des metastores secondaires](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Les objets blob peuvent être utilisés pour les données structurées et non structurées. Les conteneurs d’objets blob stockent des données en tant que paires clé/valeur et ils n’ont pas de hiérarchie de répertoires. Cependant, le nom de la clé peut inclure une barre oblique (« / ») pour faire comme si un fichier était stocké dans une structure de répertoires. Par exemple, une clé d’objet blob peut être `input/log1.txt`. Il n’existe aucun répertoire `input` réel, mais en raison de la présence de la barre oblique dans le nom de la clé, celle-ci apparaît comme un chemin de fichier.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Avantages du stockage Azure
Les clusters de calcul et les ressources de stockage qui ne sont pas colocalisés ont des coûts implicites en termes de performances. Ces coûts sont atténués par la façon dont les clusters de calcul sont créés à proximité des ressources de compte de stockage à l’intérieur de la région Azure. Dans cette région, les nœuds de calcul peuvent accéder efficacement aux données par le biais du réseau à haut débit à l’intérieur de Stockage Azure.

Quand vous stockez les données dans Stockage Azure plutôt qu’HDFS, vous obtenez plusieurs avantages :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Les données dans Stockage Azure, quant à elles, sont accessibles par le biais des API HDFS ou des API REST de Stockage Blob. Vous pouvez ainsi utiliser un plus grand nombre d’applications (notamment d’autres clusters HDInsight) et d’outils pour produire et consommer des données.

* **Archivage des données :** quand les données sont stockées dans Stockage Azure, les clusters HDInsight utilisés pour les calculs peuvent être supprimés sans perte de données utilisateur.

* **Coût de stockage des données :** le stockage à long terme des données dans DFS est plus coûteux que le stockage des données dans Stockage Azure, car le coût d’un cluster de calcul est plus élevé que celui de Stockage Azure. De plus, comme vous n’avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.

* **Scale-out élastique :** même si le système HDFS offre un système de fichiers scale-out, l’échelle est déterminée par le nombre de nœuds que vous créez pour votre cluster. Il est parfois plus simple de profiter des capacités de mise à l’échelle élastique que vous obtenez automatiquement dans le stockage Azure, plutôt que de modifier l’échelle.

* **Géoréplication :** vous pouvez géorépliquer votre Stockage Azure. Si la géoréplication permet la récupération à partir d’autres régions géographiques et la redondance des données, un basculement vers un emplacement géorépliqué affecte sérieusement les performances et peut entraîner des frais supplémentaires. Par conséquent, choisissez la géoréplication avec précaution et uniquement si la valeur des données justifie le coût supplémentaire.

Certains packages et travaux MapReduce peuvent créer des résultats intermédiaires qu’il est préférable de ne pas stocker dans Stockage Azure. Dans ce cas, vous pouvez choisir de stocker les données dans un système HDFS local. HDInsight utilise DFS pour plusieurs de ces résultats intermédiaires dans les tâches Hive et d’autres processus.

> [!NOTE]  
> La plupart des commandes HDFS (par exemple, `ls`, `copyFromLocal` et `mkdir`) fonctionnent comme prévu dans Stockage Azure. Seules les commandes propres à l’implémentation HDFS native (ou DFS), comme `fschk` et `dfsadmin`, se comportent différemment dans Stockage Azure.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Vue d’ensemble d’Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 est un référentiel d’entreprise à très grande échelle pour les charges de travail d’analytique du Big Data. Grâce à Azure Data Lake, vous pouvez capturer des données de toute taille, de tout type et à toute vitesse d’ingestion dans un emplacement unique à des fins d’analytique opérationnelle et exploratoire.

Accédez à Data Lake Storage Gen1 à partir d’Hadoop (disponible avec un cluster HDInsight) avec les API REST compatibles WebHDFS. Data Lake Storage Gen1 est conçu pour permettre l’analytique des données stockées, et optimisé pour les performances des scénarios d’analytique des données. Par défaut, il offre les fonctionnalités qui sont essentielles pour les cas d’usage professionnels du monde réel. Ces fonctionnalités incluent la sécurité, la facilité de gestion, la scalabilité, la fiabilité et la disponibilité.

Pour obtenir des informations détaillées sur Azure Data Lake Store Gen1, consultez [Vue d’ensemble d’Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Voici quelques-unes des fonctionnalités clés de Data Lake Storage Gen1.

### <a name="compatibility-with-hadoop"></a>Compatibilité avec Hadoop

Data Lake Storage Gen1 est un système de fichiers Apache Hadoop compatible avec HDFS et fonctionnant avec l’écosystème Hadoop.  Vos applications ou services HDInsight existants, qui utilisent l’API WebHDFS, peuvent s’intégrer facilement à Data Lake Storage Gen1. Data Lake Storage Gen1 offre également une interface REST compatible WebHDFS pour les applications.

Les données stockées dans Data Lake Storage Gen1 peuvent être facilement analysées avec les infrastructures d’analyse Hadoop, comme MapReduce ou Hive. Les clusters Azure HDInsight peuvent être provisionnés et configurés pour accéder directement aux données stockées dans Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Stockage illimité, fichiers de l’ordre du pétaoctet

Data Lake Storage Gen1 offre un stockage illimité et est approprié pour le stockage d’une grande variété de données à des fins d’analyse. Il n’impose aucune limite de taille de compte, de taille de fichier ou de quantité de données stockées dans un Data Lake. La taille des fichiers individuels peut varier de l’ordre du kilooctet à l’ordre du pétaoctet, ce qui fait de Data Lake Storage Gen1 un choix idéal pour stocker tout type de données. Les données sont stockées durablement en créant des copies multiples, et il n’existe aucune limite de durée de stockage des données dans le Data Lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Optimisation des performances pour l’analytique du Big Data

Data Lake Storage Gen1 est conçu pour exécuter des systèmes d’analytique à grande échelle, nécessitant un débit élevé pour interroger et analyser de grands volumes de données. Le Data Lake répartit les différentes parties des fichiers sur plusieurs serveurs de stockage distincts. Quand vous analysez des données, cette configuration améliore le débit de lecture en cas de lecture du fichier en parallèle.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Préparation pour l’entreprise : sécurisé et hautement disponible

Data Lake Storage Gen1 offre une fiabilité et une disponibilité aux normes industrielles. Les ressources de données sont stockés durablement : des copies redondantes offrent une protection contre toute défaillance inattendue. Les entreprises peuvent utiliser Data Lake Storage Gen1 dans leurs solutions et l’intégrer à leur plateforme de données existante.

Data Lake Storage Gen1 fournit également une sécurité de niveau entreprise des données stockées. Pour plus d’informations, consultez [Sécurisation des données dans Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Structures de données flexibles

Data Lake Storage Gen1 peut stocker des données dans leur format natif, en l’état, sans nécessiter de transformations préalables. Data Lake Storage Gen1 ne nécessite pas la définition d’un schéma avant le chargement des données. Le framework d’analytique interprète les données et définit un schéma au moment de l’analyse. Étant capable de stocker des fichiers de formats et de tailles arbitraires, Data Lake Storage Gen1 peut gérer des données structurées, semi-structurées et non structurées.

Les conteneurs Data Lake Storage Gen1 pour données sont essentiellement des dossiers et des fichiers. Vous travaillez sur les données stockées à l’aide de SDK, du portail Azure et d’Azure Powershell. Tant que vous placez vos données dans le magasin en utilisant ces interfaces et les conteneurs appropriés, vous pouvez stocker n’importe quel type de données. Data Lake Storage Gen1 n’effectue aucune opération particulière sur les données en fonction du type de données qu’il contient.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Sécurité des données dans Data Lake Storage Gen1
Data Lake Storage Gen1 utilise Azure Active Directory pour l’authentification, et des listes de contrôle d’accès pour gérer l’accès à vos données.

| **Fonctionnalité** | **Description** |
| --- | --- |
| Authentification |Data Lake Storage Gen1 s’intègre à Azure Active Directory (Azure AD) pour la gestion des identités et des accès de toutes les données stockées dans Data Lake Storage Gen1. Grâce à cette intégration, Data Lake Storage Gen1 tire parti de toutes les fonctionnalités d’Azure AD, notamment l’authentification multifacteur, l’accès conditionnel, le contrôle d’accès en fonction du rôle, la supervision de l’utilisation des applications, la supervision de la sécurité, les alertes, et ainsi de suite. Data Lake Storage Gen1 prend en charge le protocole OAuth 2.0 pour l’authentification auprès de l’interface REST. Voir [Authentification auprès d’Azure Data Lake Storage Gen1 à l’aide d’Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).|
| Contrôle d’accès |Data Lake Storage Gen1 offre un contrôle d’accès grâce à la prise en charge des autorisations de type POSIX exposées par le protocole WebHDFS. Les ACL peuvent être activées au niveau du dossier racine, des sous-dossiers et des fichiers individuels. Pour plus d’informations sur le fonctionnement des listes ACL dans le contexte de Data Lake Storage Gen1, consultez [Contrôle d’accès dans Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Chiffrement |Data Lake Storage Gen1 assure également le chiffrement des données stockées dans le compte. Vous spécifiez les paramètres de chiffrement lors de la création d’un compte Data Lake Storage Gen1. Vous pouvez choisir de chiffrer vos données ou non. Pour plus d’informations, consultez l’article [Chiffrement dans Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Pour savoir comment définir une configuration avec chiffrement, consultez [Bien démarrer avec Azure Data Lake Storage Gen1 à l’aide du Portail Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Pour en savoir plus sur la sécurisation des données dans Data Lake Storage Gen1, consultez [Sécurisation des données stockées dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Applications compatibles avec Data Lake Storage Gen1
Data Lake Storage Gen1 est compatible avec la plupart des composants open source de l’écosystème Hadoop. Par ailleurs, il s’intègre facilement avec d’autres services Azure.  Suivez les liens ci-dessous pour découvrir comment Data Lake Storage Gen1 peut être utilisé avec les composants open source ainsi que d’autres services Azure.

* Consultez [Applications Big Data open source qui fonctionnent avec Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) pour obtenir une liste des applications open source interopérables avec Data Lake Storage Gen1.
* Consultez [Intégration d’Azure Data Lake Storage Gen1 à d’autres services Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) pour comprendre comment utiliser Data Lake Storage Gen1 avec d’autres services Azure afin d’augmenter le nombre de scénarios.
* Consultez [Utilisation d’Azure Data Lake Storage Gen1 pour le Big Data](../data-lake-store/data-lake-store-data-scenarios.md) pour découvrir comment utiliser Data Lake Storage Gen1 pour des scénarios tels que l’ingestion, le traitement, le téléchargement et la visualisation de données.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Système de fichiers Data Lake Storage Gen1 (adl://)
Dans les environnements Hadoop (disponibles avec un cluster HDInsight), vous pouvez accéder à Data Lake Storage Gen1 par le biais du nouveau système de fichiers AzureDataLakeFilesystem (adl://). Les performances des applications et services qui utilisent adl:// peuvent être optimisées de plusieurs manières qui ne sont actuellement pas disponibles dans WebHDFS. Ainsi, quand vous utilisez Data Lake Storage Gen1, vous avez le choix de bénéficier de performances optimales en utilisant adl:// (option recommandée) ou de conserver le code existant en continuant à utiliser l’API WebHDFS directement. Azure HDInsight tire pleinement parti du système de fichiers AzureDataLakeFilesystem pour optimiser les performances sur Data Lake Storage Gen1.

Accédez à vos données dans Data Lake Storage Gen1 en utilisant la commande suivante :

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Pour plus d’informations sur la façon d’accéder aux données dans Data Lake Storage Gen1, consultez [Actions disponibles sur les données stockées](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduction à Azure Storage](../storage/common/storage-introduction.md)
