---
title: Qu’est Azure Data Lake Storage Gen1 ? | Microsoft Docs
description: Vue d’ensemble de Data Lake Storage Gen1 (anciennement appelé Azure Data Lake Store) et la valeur fournie sur d’autres magasins de données
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885550"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Qu’est Azure Data Lake Storage Gen1 ?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 est un référentiel d’entreprise à très grande échelle pour les charges de travail d’analyse du Big Data. Azure Data Lake vous permet de capturer les données de toute taille, de tout type et à toute vitesse d'ingestion dans un emplacement unique en vue d'une analyse opérationnelle et exploratoire.

Data Lake Storage Gen1 est accessible à partir de Hadoop (disponible avec le cluster HDInsight) avec les API REST compatibles WebHDFS. Il a conçu pour permettre l’analytique sur les données stockées et met l’accent sur les performances pour les scénarios d’analytique de données. Data Lake Storage Gen1 inclut toutes les fonctionnalités d’entreprise : sécurité, la facilité de gestion, d’évolutivité, fiabilité et disponibilité.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Fonctionnalités clés

Voici quelques-unes des fonctionnalités clés de Data Lake Storage Gen1.

### <a name="built-for-hadoop"></a>Conçu pour Hadoop

Génération 1 de Data Lake Storage est un système de fichiers Apache Hadoop qui est compatible avec Hadoop HDFS Distributed File System () et fonctionne avec l’écosystème Hadoop. Vos applications ou services HDInsight existants, qui utilisent l’API WebHDFS, peuvent s’intégrer facilement à Data Lake Storage Gen1. Data Lake Storage Gen1 offre également une interface REST compatible WebHDFS pour les applications.

Vous pouvez facilement analyser les données stockées dans Data Lake Storage Gen1 à l’aide des infrastructures d’analyse Hadoop comme MapReduce ou Hive. Vous pouvez approvisionner des clusters Azure HDInsight et les configurer pour accéder directement aux données stockées dans Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Stockage illimité, fichiers de l’ordre du pétaoctet

Data Lake Storage Gen1 fournit un stockage illimité et peut stocker une grande variété de données pour l’analytique. Il n’impose aucune limite de taille pour les comptes, taille de fichier ou la quantité de données qui peuvent être stockées dans un lac de données. Fichiers individuels peuvent varier de kilo-octets à la taille. Données sont stockées durablement en créant des copies multiples. Il n’existe aucune limite sur la durée de temps pour lequel les données peuvent être stockées dans le lac de données.

### <a name="performance-tuned-for-big-data-analytics"></a>Performances optimisées pour l'analyse du Big Data

Génération 1 de Data Lake Storage est conçu pour exécuter des systèmes d’analyse à grande échelle qui nécessitent un débit conséquent pour interroger et analyser de grandes quantités de données. Le lac de données répartit les différentes parties des fichiers sur plusieurs serveurs de stockage distincts. Cela améliore le débit de lecture lors de la lecture du fichier en parallèle de l'analyse de données.

### <a name="enterprise-ready-highly-available-and-secure"></a>Prêt pour l’entreprise : sécurisé et hautement disponible

Data Lake Storage Gen1 offre une fiabilité et une disponibilité aux normes industrielles. Vos données sont stockées durablement en créant des copies redondantes pour vous prémunir contre toute défaillance imprévue.

Data Lake Storage Gen1 fournit également une sécurité de niveau entreprise des données stockées. Pour plus d’informations, consultez [Sécurisation des données dans Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Toutes les données

Data Lake Storage Gen1 peut stocker des données dans son format natif, sans nécessiter de transformations préalables. Data Lake Storage Gen1 ne requiert pas la définition d’un schéma avant le chargement des données, laissant ainsi à l’infrastructure d’analyse individuelle le soin d’interpréter les données et de définir un schéma au moment de l’analyse. La possibilité de stocker les fichiers de formats et de tailles arbitraires rend possible pour Data Lake Storage Gen1 gérer les données structurées, semi-structurées et non structurées.

Les conteneurs Data Lake Storage Gen1 pour données sont essentiellement des dossiers et des fichiers. Vous travaillez sur les données stockées à l’aide de kits de développement logiciel, le portail Azure et d’Azure Powershell. Si vous placez vos données dans le magasin à l’aide de ces interfaces et les conteneurs appropriés, vous pouvez stocker n’importe quel type de données. Data Lake Storage Gen1 n’effectue aucune opération particulière sur les données en fonction du type de données qu’il contient.

## <a name="DataLakeStoreSecurity"></a>Sécurisation des données

Listes (ACL) pour gérer l’accès de contrôle d’utilisations de stockage Gen1 Data Lake Azure Active Directory (Azure AD) pour l’authentification et l’accès à vos données.

| Fonctionnalité | Description |
| --- | --- |
| Authentification |Data Lake Storage Gen1 s’intègre avec Azure AD pour la gestion des identités et des accès pour toutes les données stockées dans Data Lake Storage Gen1. En raison de l’intégration, les avantages de Data Lake Storage Gen1 à partir de tous les Azure AD des fonctionnalités telles que l’authentification multifacteur, accès conditionnel, contrôle d’accès en fonction du rôle, surveillance de l’utilisation d’application, surveillance de la sécurité et d’alerte et ainsi de suite. Data Lake Storage Gen1 prend en charge le protocole OAuth 2.0 pour l’authentification auprès de l’interface REST. Consultez [l’authentification Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Contrôle d’accès |Data Lake Storage Gen1 offre un contrôle d’accès grâce à la prise en charge des autorisations de type POSIX exposées par le protocole WebHDFS. Vous pouvez activer les ACL sur le dossier racine, des sous-dossiers et des fichiers individuels. Pour plus d’informations sur le fonctionnement des ACL dans le contexte de Data Lake Storage Gen1, consultez [contrôle d’accès dans Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Chiffrement |Data Lake Storage Gen1 fournit également le chiffrement pour les données qui sont stockées dans le compte. Vous spécifiez les paramètres de chiffrement lors de la création d’un compte Data Lake Storage Gen1. Vous pouvez choisir de chiffrer vos données ou non. Pour plus d’informations, consultez l’article [Chiffrement dans Data Lake Storage Gen1](data-lake-store-encryption.md). Pour obtenir des instructions sur la façon de fournir une configuration avec chiffrement, consultez [prise en main Data Lake Storage Gen1 à l’aide du portail Azure](data-lake-store-get-started-portal.md). |

Pour savoir comment sécuriser les données dans Data Lake Storage Gen1, consultez [Sécurisation des données dans Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Compatibilité des applications

Data Lake Storage Gen1 est compatible avec la plupart des composants open source de l’écosystème Hadoop. Il s’intègre également bien avec d’autres services Azure. Pour en savoir plus sur comment vous pouvez utiliser Data Lake Storage Gen1 avec les composants open source et d’autres services Azure, utilisez les liens suivants :

- Consultez [Applications et services compatibles avec Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) pour obtenir une liste des applications open source interopérables avec Data Lake Storage Gen1.
- Consultez [l’intégration avec d’autres services Azure](data-lake-store-integrate-with-other-services.md) pour comprendre comment utiliser Data Lake Storage Gen1 avec d’autres services Azure pour permettre un plus large éventail de scénarios.
- Consultez la page [Scénarios de données impliquant Data Lake Storage Gen1](data-lake-store-data-scenarios.md) pour découvrir comment utiliser Data Lake Storage Gen1 pour des scénarios tels que la réception, le traitement, le téléchargement et la visualisation de données.

## <a name="data-lake-storage-gen1-file-system"></a>Système de fichiers Data Lake Storage Gen1

Génération 1 de Data Lake Storage est accessible via le système de fichiers AzureDataLakeFilesystem (adl : / /) dans les environnements Hadoop (disponibles avec un cluster HDInsight). Applications et services qui utilisent adl : / / peuvent tirer parti de davantage d’optimisations des performances qui ne sont pas actuellement disponibles dans WebHDFS. Par conséquent, donne de Data Lake Storage Gen1 la possibilité soit procéder à utiliser les meilleures performances avec l’option recommandée de l’utilisation d’adl : / / ou conserver le code existant en continuant à utiliser l’API WebHDFS directement. Azure HDInsight tire pleinement parti du système de fichiers AzureDataLakeFilesystem pour optimiser les performances sur Data Lake Storage Gen1.

Vous pouvez accéder à vos données dans Data Lake Storage Gen1 à l’aide de `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Pour plus d’informations sur la façon d’accéder aux données dans Data Lake Storage Gen1, consultez [afficher les propriétés des données stockées](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Étapes suivantes

- [Prise en main Data Lake Storage Gen1 à l’aide du portail Azure](data-lake-store-get-started-portal.md)
- [Prise en main Data Lake Storage Gen1 à l’aide du Kit de développement logiciel .NET](data-lake-store-get-started-net-sdk.md)
- [Utiliser Azure HDInsight avec Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)