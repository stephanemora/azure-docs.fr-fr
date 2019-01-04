---
title: Utiliser Azure Data Lake Storage Gen2 avec des clusters Apache Hadoop dans Azure HDInsight
description: Fournit une vue d’ensemble d’Azure Data Lake Storage Gen2 et de son fonctionnement avec Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 2ae11afe1ecbe500a4851aab6d56e612fbe79ee6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976383"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Utiliser Azure Data Lake Storage Gen2 avec des clusters Apache Hadoop dans Azure HDInsight

Azure Data Lake Storage Gen2 reprend les fonctionnalités principales d’Azure Data Lake Storage Gen1, comme un système de fichiers compatible Hadoop, Azure Active Directory et les listes de contrôle d’accès POSIX, et les intègre dans Stockage Blob Azure. Cette combinaison vous permet de tirer parti des performances d’Azure Data Lake Storage Gen1, tout en utilisant aussi la hiérarchisation et la gestion du cycle de vie des données de Stockage Blob.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Fonctionnalités principales d’Azure Data Lake Storage Gen2

- Accès compatible Hadoop : Azure Data Lake Storage Gen2 vous permet de gérer les données et d’y accéder comme vous le feriez avec un système de fichiers HDFS (Hadoop Distributed File System). Le pilote ABFS est disponible dans tous les environnements Apache Hadoop, notamment Azure HDInsight et Azure Databricks, pour accéder aux données stockées dans Data Lake Storage Gen2.

- Surensemble d’autorisations POSIX : Le modèle de sécurité pour Data Lake Gen2 prend en charge les autorisations ACL et POSIX, ainsi que certaines granularités supplémentaires spécifiques à Data Lake Storage Gen2. Les paramètres peuvent être configurés via des outils d’administration ou des infrastructures comme Apache Hive et Apache Spark.

- Économique : Data Lake Storage Gen2 offre une capacité de stockage et des transactions à bas coût. Des fonctionnalités comme le cycle de vie de Stockage Blob Azure vous aident à réduire les coûts en adaptant les frais facturés au fil du cheminement des données dans son cycle de vie.

- Fonctionne avec les outils, les frameworks et les applications de Stockage Blob : Data Lake Storage Gen2 continue de fonctionner avec une large gamme d’outils, de frameworks et d’applications qui existent aujourd’hui pour Stockage Blob.

- Pilote optimisé : Le pilote ABFS est optimisé spécifiquement pour l’analytique Big Data. Les API REST correspondantes sont exposées via le point de terminaison dfs, dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Nouveautés d’Azure Data Lake Storage Gen2

### <a name="managed-identities-for-secure-file-access"></a>Identités managées pour l’accès sécurisé aux fichiers

Azure HDInsight utilise des identités managées pour sécuriser l’accès du cluster aux fichiers dans Azure Data Lake Storage Gen2. Les identités managées sont une fonctionnalité d’Azure Active Directory qui fournit des services Azure avec un ensemble d’informations d’identification gérées automatiquement. Ces informations d’identification peuvent être utilisées pour s’authentifier auprès de n’importe quel service prenant en charge l’authentification Active Directory. L’utilisation des identités managées vous évite de stocker les informations d’identification dans les fichiers de code ou de configuration.

Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Pilote Azure Blob FileSystem (ABFS)

Les applications Apache Hadoop peuvent lire et écrire en mode natif des données dans un stockage sur disque local. Un pilote de système de fichiers Hadoop comme ABFS permet aux applications Hadoop d’utiliser un stockage cloud en émulant les opérations normales du système de fichiers Hadoop pour l’application. Le pilote convertit ensuite ces opérations en opérations qui sont comprises par la plateforme de stockage cloud réelle.

Avant, le pilote de système de fichiers Hadoop convertissait toutes les opérations du système de fichiers en appels d’API REST Stockage Azure sur le côté client, puis appelait l’API REST. Cette conversion côté client entraînait cependant plusieurs appels d’API REST pour une même opération du système de fichiers, comme renommer un fichier. ABFS a déplacé une partie de la logique du système de fichiers Hadoop du côté client vers le côté serveur, et l’API ADLS Gen2 s’exécute désormais en parallèle avec l’API Blob. Cette migration améliore les performances, car les opérations courantes du système de fichiers Hadoop peuvent désormais être exécutées avec un seul appel d’API REST.

Pour plus d’informations, consultez [Pilote ABFS (Azure Blob Filesystem) : pilote Stockage Azure dédié pour Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>Schéma d’URI d’ADLS Gen 2

ADLS Gen2 utilise un nouveau schéma d’URI pour accéder aux fichiers de Stockage Azure à partir d’HDInsight :

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

Pour plus d’informations, consultez [Utiliser l’URI d’Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>Étapes suivantes
- [Présentation d’Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md)
- [Utiliser Azure Data Lake Storage Gen2 Preview avec des clusters Azure HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)