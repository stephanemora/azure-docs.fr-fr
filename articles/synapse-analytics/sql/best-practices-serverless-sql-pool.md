---
title: Bonnes pratiques pour les pools SQL serverless
description: Recommandations et meilleures pratiques pour l’utilisation d’un pool SQL serverless.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7611d9067422a0e2e342bbafeb315b5b1545a212
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545187"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Meilleures pratiques pour un pool SQL serverless dans Azure Synapse Analytics

Cet article présente un ensemble de meilleures pratiques pour l’utilisation d’un pool SQL serverless. Un pool SQL serverless est une ressource dans Azure Synapse Analytics. Si vous utilisez un pool SQL dédié, consultez [Meilleures pratiques pour les pools SQL dédiés](best-practices-dedicated-sql-pool.md) afin d’obtenir une aide spécifique.

Un pool SQL serverless vous permet d’interroger des fichiers dans vos comptes Stockage Azure. Il ne dispose pas de capacités de stockage ou d’ingestion locales. Tous les fichiers que les requêtes ciblent sont externes au pool SQL serverless. Tout ce qui est lié à la lecture de fichiers à partir du stockage peut avoir un impact sur les performances des requêtes.

Voici quelques recommandations générales :

- Assurez-vous que vos applications clientes sont colocalisées avec le pool SQL serverless.
  - Si vous utilisez des applications clientes en dehors d’Azure, vérifiez que vous utilisez un pool SQL serverless dans une région proche de votre ordinateur client. Les exemples d’applications clientes incluent Power BI Desktop, SQL Server Management Studio et Azure Data Studio.
- Assurez-vous que le stockage et le pool SQL serverless se trouvent dans la même région. Les exemples de stockage incluent Azure Data Lake Storage et Azure Cosmos DB.
- Essayez d’[optimiser la disposition du stockage](#prepare-files-for-querying) en utilisant le partitionnement et en maintenant vos fichiers dans une fourchette comprise entre 100 Mo et 10 Go.
- Si vous renvoyez un grand nombre de résultats, assurez-vous que vous utilisez SQL Server Management Studio ou Azure Data Studio, et non Azure Synapse Studio. Azure Synapse Studio est un outil web qui n’est pas conçu pour les jeux de résultats volumineux.
- Si vous filtrez les résultats par colonne de chaînes, essayez d’utiliser un classement `BIN2_UTF8`.
- Pensez à mettre en cache les résultats côté client en utilisant le mode d’importation de Power BI ou Azure Analysis Services et actualisez-les régulièrement. Les pools SQL serverless ne peuvent pas fournir une expérience interactive en mode Requête directe de Power BI si vous utilisez des requêtes complexes ou si vous traitez une grande quantité de données.

## <a name="client-applications-and-network-connections"></a>Applications clientes et connexions réseau

Assurez-vous que votre application cliente est connectée à l’espace de travail Azure Synapse le plus proche possible avec une connexion optimale.
- Colocalisez une application cliente avec l’espace de travail Azure Synapse. Si vous utilisez des applications telles que Power BI ou Azure Analysis Service, assurez-vous qu’elles se trouvent dans la même région que celle où vous avez placé votre espace de travail Azure Synapse. Si nécessaire, créez les espaces de travail distincts associés à vos applications clientes. Placer une application cliente et l’espace de travail Azure Synapse dans des régions différentes peut entraîner une latence plus importante et une diffusion plus lente des résultats.
- Si vous lisez des données à partir de votre application locale, assurez-vous que l’espace de travail Azure Synapse se trouve dans la région la plus proche de votre emplacement.
- Assurez-vous que vous n’avez pas de problèmes de bande passante réseau lors de la lecture d’une grande quantité de données.
- N’utilisez pas Azure Synapse Studio pour renvoyer une grande quantité de données. Azure Synapse Studio est un outil web qui utilise le protocole HTTPS pour transférer des données. Utilisez Azure Data Studio ou SQL Server Management Studio pour lire une grande quantité de données.

## <a name="storage-and-content-layout"></a>Stockage et disposition du contenu

Voici les meilleures pratiques pour le stockage et la disposition du contenu dans un pool SQL serverless.

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Colocaliser votre stockage et votre pool SQL serverless

Pour réduire la latence, colocalisez votre compte Stockage Azure ou votre stockage analytique Azure Cosmos DB avec votre point de terminaison de pool SQL serverless. Les comptes de stockage et les points de terminaison approvisionnés lors de la création de l’espace de travail se trouvent dans la même région.

Pour optimiser les performances, si vous accédez à d’autres comptes de stockage avec le pool SQL serverless, assurez-vous qu’ils se trouvent dans la même région. Autrement, vous obtiendrez une latence accrue pour le transfert réseau des données entre la région distante et la région du point de terminaison.

### <a name="azure-storage-throttling"></a>Limitation de Stockage Azure

Plusieurs applications et services peuvent accéder à votre compte de stockage. Lorsque les IOPS ou le débit combinés générés par des applications, des services et les charges de travail du pool SQL serverless dépassent les limites du compte de stockage, une limitation du stockage se produit. Celle-ci a un impact significatif sur les performances des requêtes.

Quand une limitation est détectée, le pool SQL serverless dispose d’une fonction de gestion intégrée pour résoudre le problème. Le pool SQL serverless ralentit la fréquence des demandes adressées au stockage jusqu’à ce que la limitation soit résolue.

> [!TIP]
> Pour optimiser l’exécution des requêtes, évitez de solliciter le compte de stockage avec d’autres charges de travail lors de l’exécution de la requête.

### <a name="azure-ad-pass-through-authentication-performance"></a>Performances de l’authentification directe Azure AD

Le pool SQL serverless vous permet d’accéder aux fichiers dans le stockage en utilisant l’authentification directe Azure Active Directory (Azure AD) ou les informations d’identification de la signature d’accès partagé. Il se peut que vous constatiez que l’authentification directe Azure AD est moins performante que les signatures d’accès partagé.

Si vous avez besoin d’un meilleur niveau de performance, essayez d’utiliser des informations d’identification de signature d’accès partagé pour accéder au stockage.

### <a name="prepare-files-for-querying"></a>Préparer les fichiers pour l’interrogation

Si possible, vous pouvez préparer les fichiers pour améliorer les performances :

- Convertissez les fichiers CSV et JSON volumineux au format Parquet. Parquet est un format en colonnes. Comme il est compressé, ses fichiers sont de plus petite taille que des fichiers CSV ou JSON contenant les mêmes données. Le pool SQL serverless ignore les colonnes et les lignes qui ne sont pas nécessaires dans une requête si vous lisez des fichiers Parquet. Le pool SQL serverless a besoin de moins de temps et de moins de demandes de stockage pour les lire.
- Si une requête cible un seul fichier volumineux, il est avantageux de fractionner celui-ci en fichiers plus petits.
- Essayez de conserver une taille de fichier CSV entre 100 Mo et 10 Go.
- Il est préférable d’avoir des fichiers de taille identique pour un chemin d’accès OPENROWSET unique ou un emplacement de table externe.
- Partitionnez vos données en stockant les partitions dans des dossiers ou sous des noms de fichiers différents. Consultez [Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques](#use-filename-and-filepath-functions-to-target-specific-partitions).

### <a name="colocate-your-azure-cosmos-db-analytical-storage-and-serverless-sql-pool"></a>Colocaliser votre stockage analytique Azure Cosmos DB et votre pool SQL serverless

Assurez-vous que votre stockage analytique Azure Cosmos DB est placé dans la même région qu’un espace de travail Azure Synapse. Les requêtes entre régions peuvent entraîner des latences énormes. Utilisez la propriété region dans la chaîne de connexion pour spécifier explicitement la région dans laquelle le magasin analytique est placé (voir [Interroger Azure Cosmos DB à l’aide du pool SQL serverless](query-cosmos-db-analytical-store.md#overview)) :

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>Optimisations de protocole CSV

Voici les meilleures pratiques pour utiliser des fichiers CSV dans un pool SQL serverless.

### <a name="use-parser_version-20-to-query-csv-files"></a>Utiliser PARSER_VERSION 2.0 pour interroger des fichiers CSV

Vous pouvez utiliser l’analyseur à performances optimisées lors de l’interrogation de fichiers CSV. Pour plus d’informations, consultez [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Créer manuellement des statistiques pour les fichiers CSV

Le pool SQL serverless s’appuie sur des statistiques pour générer des plans optimaux d’exécution des requêtes. Les statistiques sont automatiquement créées pour les colonnes de fichiers Parquet le cas échéant. Pour le moment, les statistiques ne sont pas créées automatiquement pour les colonnes des fichiers CSV. Créez manuellement des statistiques pour les colonnes que vous utilisez dans les requêtes, en particulier celles utilisées dans DISTINCT, JOIN, WHERE, ORDER BY et GROUP BY. Consultez [Statistiques dans un pool SQL serverless](develop-tables-statistics.md#statistics-in-serverless-sql-pool) pour plus d’informations.

## <a name="data-types"></a>Types de données

Voici les meilleures pratiques pour utiliser des types de données dans un pool SQL serverless.

### <a name="use-appropriate-data-types"></a>Utiliser les types de données appropriés

Les types de données que vous utilisez dans votre requête ont une incidence sur les performances et la concurrence. Vous pouvez obtenir de meilleures performances en suivant les instructions suivantes : 

- Utilisez la plus petite taille de données pouvant prendre en charge la plus grande valeur possible.
  - Si la longueur maximale est de 30 caractères, utilisez un type de données d’une longueur de 30 caractères.
  - Si toutes les valeurs de colonne de caractères ont une taille fixe, utilisez le type **char** ou **nchar**. Sinon, utilisez le type **varchar** ou **nvarchar**.
  - Si la valeur maximale de la colonne d’entiers est 500, utilisez le type **smallint**, car il s’agit du plus petit type de données pouvant prendre en charge cette valeur. Vous trouverez les plages de types d’entiers dans [cet article](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Autant que possible, utilisez les types **varchar** et **char** plutôt que les types **nvarchar** et **nchar**.
  - Utilisez le type **varchar** avec un classement UTF8 si vous lisez des données à partir de fichiers Parquet, Azure Cosmos DB, Delta Lake ou CSV avec un encodage UTF-8.
  - Utilisez le type **varchar** sans classement UTF8 si vous lisez des données à partir de fichiers CSV non Unicode (par exemple, ASCII).
  - Utilisez le type **nvarchar** si vous lisez des données à partir d’un fichier CSV UTF-16.
- Utilisez des types de données de type Integer si possible. Les opérations de tri (SORT), de jointure (JOIN) et de regroupement (GROUP BY) sont effectuées plus rapidement sur des entiers que sur des données caractères.
- Si vous utilisez l’inférence de schéma, [vérifiez les types de données déduits](#check-inferred-data-types) et remplacez-les explicitement par des types plus petits si possible.

### <a name="check-inferred-data-types"></a>Vérifier les types de données déduits

Une [inférence de schéma](query-parquet-files.md#automatic-schema-inference) vous permet d’écrire rapidement des requêtes et d’explorer des données sans connaître le schéma de fichier. Le coût de cette commodité est que les types de données déduits peuvent être plus volumineux que les types de données réels. Cette divergence se produit quand il n’y a pas assez d’informations dans les fichiers sources pour garantir que le type de données approprié est utilisé. Par exemple, des fichiers Parquet ne contiennent pas de métadonnées sur la longueur maximale des colonnes de caractères. Le pool SQL serverless en déduit qu’il s’agit d’une valeur varchar(8000).

Pour vérifier les types de données résultant de votre requête, vous pouvez utiliser la commande [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true).

L’exemple suivant montre comment vous pouvez optimiser les types de données déduits. Cette procédure permet d’afficher les types de données déduits :

```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Voici le jeu de résultats :

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------|
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

Une fois que vous connaissez les types de données déduits pour la requête, vous pouvez spécifier les types de données appropriés :

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Optimisation du filtre

Voici les meilleures pratiques pour utiliser des requêtes dans un pool SQL serverless.

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Envoyer (push) des caractères génériques à des niveaux inférieurs dans le chemin d’accès

Vous pouvez utiliser des caractères génériques dans votre chemin pour [interroger plusieurs fichiers et dossiers](query-data-storage.md#query-multiple-files-or-folders). Le pool SQL serverless répertorie les fichiers figurant dans votre compte de stockage, en commençant par le premier astérisque (*), à l’aide de l’API de stockage. Il élimine les fichiers qui ne correspondent pas au chemin d’accès spécifié. La réduction de la liste de fichiers initiale peut améliorer les performances si de nombreux fichiers correspondent au chemin spécifié jusqu’au premier caractère générique.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques

Les données sont souvent organisées en partitions. Vous pouvez demander au pool SQL serverless d’interroger des dossiers et fichiers particuliers. En procédant ainsi, vous réduisez le nombre de fichiers et la quantité de données que la requête doit lire et traiter. En prime, vous obtiendrez de meilleures performances.

Pour plus d’informations, consultez les fonctions [filename](query-data-storage.md#filename-function) et [filepath](query-data-storage.md#filepath-function), ainsi que les exemples d’[interrogation de fichiers spécifiques](query-specific-files.md).

> [!TIP]
> Castez toujours les résultats des fonctions filepath et filename vers les types de données appropriés. Si vous utilisez des types de données caractères, veillez à utiliser la longueur appropriée.

Les fonctions utilisées pour l’élimination de partition, filepath et filename, ne sont actuellement pas prises en charge pour des tables externes autres que celles créées automatiquement pour chaque table créée dans Apache Spark pour Azure Synapse Analytics.

Si vos données stockées ne sont pas partitionnées, envisagez de les partitionner. De cette façon, vous pourrez utiliser ces fonctions pour optimiser les requêtes ciblant ces fichiers. Lorsque vous [interrogez des tables Apache Spark pour Azure Synapse partitionnées](develop-storage-files-spark-tables.md) à partir du pool SQL serverless, la requête cible automatiquement les fichiers nécessaires uniquement.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Utilisez le classement approprié pour utiliser le pushdown des prédicats pour les colonnes de caractères

Les données d’un fichier Parquet sont organisées en groupes de lignes. Le pool SQL serverless ignore les groupes de lignes en fonction du prédicat spécifié dans la clause WHERE, ce qui réduit les entrées-sorties. En conséquence, les performances des requêtes sont accrues.

Le pushdown des prédicats pour les colonnes de caractères dans les fichiers Parquet est pris en charge pour le classement Latin1_General_100_BIN2_UTF8 uniquement. Vous pouvez spécifier le classement d’une colonne particulière à l’aide d’une clause WITH. Si vous ne spécifiez pas ce classement à l’aide d’une clause WITH, le classement de base de données sera utilisé.

## <a name="optimize-repeating-queries"></a>Optimiser les requêtes répétitives

Voici les meilleures pratiques pour utiliser CETAS dans un pool SQL serverless.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Utiliser CETAS pour améliorer les performances des requêtes et les jointures

[CETAS](develop-tables-cetas.md) est l’une des fonctionnalités les plus importantes disponibles dans le pool SQL serverless. CETAS est une opération parallèle qui crée des métadonnées de table externe et exporte le résultat de la requête SELECT vers un ensemble de fichiers dans votre compte de stockage.

Vous pouvez utiliser CETAS pour matérialiser un nouveau jeu de fichiers des parties de requêtes souvent utilisées, telles des tables de référence jointes. Vous pouvez ensuite établir une jointure avec cette table externe au lieu de répéter des jointures communes dans plusieurs requêtes.

Comme CETAS génère des fichiers Parquet, les statistiques sont automatiquement créées lorsque la première requête cible cette table externe. Il en résulte une amélioration des performances pour les requêtes ultérieures ciblant la table générée avec CETAS.

## <a name="next-steps"></a>Étapes suivantes

Pour trouver des solutions aux problèmes courants, consultez l’article sur la [résolution des problèmes](resources-self-help-sql-on-demand.md). Si vous utilisez un pool SQL dédié plutôt qu’un pool SQL serverless, consultez [Meilleures pratiques pour les pools SQL dédiés](best-practices-dedicated-sql-pool.md) afin d’obtenir une aide spécifique.
