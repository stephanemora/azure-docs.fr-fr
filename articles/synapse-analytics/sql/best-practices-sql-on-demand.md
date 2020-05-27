---
title: Meilleures pratiques pour SQL à la demande (préversion) dans Azure Synapse Analytics
description: Recommandations et meilleures pratiques à connaître si vous utilisez SQL à la demande.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 86678365d1510199247e8a1aaa48ec844d07de32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592931"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Meilleures pratiques pour SQL à la demande (préversion) dans Azure Synapse Analytics

Cet article présente un ensemble de meilleures pratiques pour l’utilisation de SQL à la demande (préversion). SQL à la demande est une ressource supplémentaire au sein d’Azure Synapse Analytics.

## <a name="general-considerations"></a>Considérations d’ordre général

SQL à la demande vous permet d’interroger des fichiers dans vos comptes de stockage Azure. Il ne dispose pas de capacités de stockage ou d’ingestion locales. Cela qui signifie que tous les fichiers que la requête cible sont externes à SQL à la demande. Tout ce qui est lié à la lecture de fichiers à partir du stockage peut avoir un impact sur les performances des requêtes.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocaliser le compte Stockage Azure et SQL à la demande

Pour réduire la latence, colocalisez votre compte de stockage Azure et votre point de terminaison SQL à la demande. Les comptes de stockage et les points de terminaison approvisionnés lors de la création de l’espace de travail se trouvent dans la même région.

Pour optimiser les performances, si vous accédez à d’autres comptes de stockage avec SQL à la demande, assurez-vous qu’ils se trouvent dans la même région. Autrement, vous obtiendrez une latence accrue pour le transfert réseau des données entre la région distante et la région du point de terminaison.

## <a name="azure-storage-throttling"></a>Limitation de Stockage Azure

Plusieurs applications et services peuvent accéder à votre compte de stockage. Lorsque les IOPS ou le débit combinés générés par des applications, services et charges de travail SQL à la demande dépassent les limites du compte de stockage, une limitation du stockage se produit. Celle-ci a un impact significatif sur les performances des requêtes.

Quand une limitation est détectée, SQL à la demande dispose d’une fonction intégrée pour la gestion de ce scénario. SQL à la demande ralentit la fréquence des demandes adressées au stockage jusqu’à ce que la limitation soit résolue.

> [!TIP]
> Pour optimiser l’exécution des requêtes, vous devez éviter de solliciter le compte de stockage avec d’autres charges de travail lors de l’exécution de la requête.

## <a name="prepare-files-for-querying"></a>Préparer les fichiers pour l’interrogation

Si possible, vous pouvez préparer les fichiers pour améliorer les performances :

- Convertir CSV et JSON en Parquet – Parquet est un format en colonnes. Dans la mesure où il est compressé, ses fichiers sont de plus petite taille que des fichiers CSV ou JSON contenant les mêmes données. SQL à la demande a besoin de moins de temps et de demandes de stockage pour le lire.
- Si une requête cible un seul fichier volumineux, il est avantageux de fractionner celui-ci en fichiers plus petits.
- Essayez de conserver une taille de fichier CSV inférieure à 10 Go.
- Il est préférable d’avoir des fichiers de taille identique pour un chemin d’accès OPENROWSET unique ou un emplacement de table externe.
- Partitionnez vos données en stockant des partitions dans différents dossiers ou noms de fichiers. Consultez [Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Envoyer (push) des caractères génériques à des niveaux inférieurs dans le chemin

Vous pouvez utiliser des caractères génériques dans votre chemin pour [interroger plusieurs fichiers et dossiers](develop-storage-files-overview.md#query-multiple-files-or-folders). SQL à la demande liste les fichiers de votre compte de stockage à partir du premier * à l’aide de l’API de stockage et élimine les fichiers qui ne correspondent pas au chemin spécifié. La réduction de la liste de fichiers initiale peut améliorer les performances si de nombreux fichiers correspondent au chemin spécifié jusqu’au premier caractère générique.

## <a name="use-appropriate-data-types"></a>Utiliser les types de données appropriés

Les types de données utilisés dans votre requête affectent les performances. Vous pouvez obtenir de meilleures performances si vous : 

- Utilisez la plus petite taille de données pouvant prendre en charge la plus grande valeur possible.
  - Si la longueur maximale de la valeur de caractère est de 30 caractères, utilisez le type de données caractères de longueur 30.
  - Si toutes les valeurs de colonne de caractères ont une taille fixe, utilisez char ou nchar. Sinon, utilisez varchar ou nvarchar.
  - Si la valeur maximale de la colonne d’entiers est 500, utilisez smallint parce qu’il s’agit du plus petit type de données pouvant prendre en charge cette valeur. Vous trouverez les plages des types de données Integer [ici](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Utilisez, si possible, varchar et char au lieu de nvarchar et nchar.
- Utilisez des types de données de type Integer si possible. Les opérations de tri, de jointure et de regroupement sont effectuées plus rapidement sur des entiers que sur des données caractères.
- Si vous utilisez l’inférence de schéma, [vérifiez le type de données déduit](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Vérifier les types de données déduits

L’[inférence de schéma](query-parquet-files.md#automatic-schema-inference) vous permet d’écrire rapidement des requêtes et d’explorer les données sans connaître le schéma de fichier. La contrepartie de ce confort est que les types de données déduits sont plus grands que leur taille réelle. Cela se produit quand il n’y a pas assez d’informations dans les fichiers sources pour que le type de données approprié soit utilisé. Par exemple, les fichiers Parquet ne contiennent pas de métadonnées sur la longueur maximale des colonnes de caractères et SQL à la demande déduit qu’il s’agit du type de données varchar(8000). 

Vous pouvez vérifier les types de données résultants de votre requête à l’aide de [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

L’exemple suivant montre comment vous pouvez optimiser les types de données déduits. La procédure est utilisée pour afficher les types de données déduits. 
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

Voici le jeu de résultats obtenu.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

Une fois que nous connaissons les types de données déduits pour la requête, nous pouvons spécifier les types de données appropriés :

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Utiliser les fonctions filename et filepath pour cibler des partitions spécifiques

Les données sont souvent organisées en partitions. Vous pouvez donner pour instruction à SQL à la demande d’interroger des dossiers et fichiers particuliers. Cela permet de réduire le nombre de fichiers et la quantité de données que la requête doit lire et traiter. En prime, vous obtiendrez de meilleures performances.

Pour plus d’informations, consultez les fonctions [filename](develop-storage-files-overview.md#filename-function) et [filepath](develop-storage-files-overview.md#filepath-function), ainsi que les exemples montrant comment [interroger des fichiers spécifiques](query-specific-files.md).

> [!TIP]
> Castez toujours le résultat des fonctions filepath et fileinfo en types de données appropriés. Si vous utilisez des types de données caractères, assurez-vous que la longueur appropriée est utilisée.

> [!NOTE]
> Les fonctions utilisées pour l’élimination de partition, filepath et fileinfo, ne sont actuellement pas prises en charge pour des tables externes autres que celles créées automatiquement pour chaque table créée dans Apache Spark pour Azure Synapse Analytics.

Si vos données stockées ne sont pas partitionnées, envisagez de les partitionner afin de pouvoir utiliser ces fonctions pour optimiser les requêtes ciblant ces fichiers. Lorsque de l’[interrogation de tables Spark partitionnées](develop-storage-files-spark-tables.md) à partir de SQL à la demande, la requête cible automatiquement uniquement les fichiers nécessaires.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Utiliser PARSER_VERSION 2.0 pour l’interrogation de fichiers CSV

Vous pouvez utiliser l’analyseur à performances optimisées lors de l’interrogation des fichiers CSV. Pour plus d’informations, consultez [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Utiliser CETAS pour améliorer les performances des requêtes et les jointures

[CETAS](develop-tables-cetas.md) est l’une des fonctionnalités les plus importantes disponibles dans SQL à la demande. CETAS est une opération parallèle qui crée des métadonnées de table externe et exporte le résultat de la requête SELECT vers un ensemble de fichiers dans votre compte de stockage.

Vous pouvez utiliser CETAS pour stocker dans un nouveau jeu de fichiers des parties de requêtes souvent utilisées, telles des tables de référence jointes. Ensuite, vous pouvez joindre une telle table externe au lieu de répéter des jointures communes dans plusieurs requêtes.

Comme CETAS génère des fichiers Parquet, les statistiques sont automatiquement créées lorsque la première requête cible cette table externe, ce qui améliore les performances.

## <a name="aad-pass-through-performance"></a>Performances directes AAD

SQL à la demande vous permet d’accéder à des fichiers dans le stockage en utilisant une connexion directe AAD ou des informations d’identification SAP. Vous pouvez constater un ralentissement des performances de la connexion directe AAD en comparaison de SAP. 

Si vous avez besoin de meilleures performances, essayez d’utiliser les informations d’identification SAP pour accéder au stockage jusqu’à ce que les performances directes AAD soient améliorées.

## <a name="next-steps"></a>Étapes suivantes

Consultez notre article [Dépannage](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) concernant les problèmes courants et leurs solutions. Si vous utilisez un pool SQL plutôt que SQL à la demande, consultez l’article [Bonnes pratiques concernant les pools SQL](best-practices-sql-pool.md) pour obtenir des instructions spécifiques.
