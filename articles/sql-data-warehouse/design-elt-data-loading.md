---
title: Concevoir un processus ELT au lieu d’un processus ETL
description: Plutôt qu’ETL, concevoir un processus d’extraction, de chargement et de transformation (ELT) pour charger les données dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 11/07/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220bf0cf94eaae6ddc945e83deac2a6041158d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748513"
---
# <a name="data-loading-strategies-for-azure-sql-data-warehouse"></a>Stratégies de chargement de données pour Azure SQL Data Warehouse

Les entrepôts de données SMP traditionnels utilisent un processus d’extraction, transformation et chargement (ETL) pour le chargement des données. Azure SQL Data Warehouse est une architecture de traitement massivement parallèle (MPP) qui tire parti de la scalabilité et de la flexibilité des ressources de calcul et de stockage. L’utilisation d’un processus d’extraction, chargement et transformation (ELT) permet d’exploiter MPP et d’éliminer les ressources nécessaires à la transformation des données avant le chargement. Bien que SQL Data Warehouse prenne en charge de nombreuses méthodes de chargement, notamment des options SQL Server bien connues, comme BCP et les API BulkCopy SQL, le moyen le plus rapide et le plus évolutif pour charger des données est d’utiliser des tables externes PolyBase et l’[instruction COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (préversion).  Avec PolyBase et l’instruction COPY, vous pouvez accéder à des données externes stockées dans Stockage Blob Azure ou dans Azure Data Lake Store via le langage T-SQL. Pour une plus grande flexibilité lors du chargement dans SQL Data Warehouse, nous vous recommandons d’utiliser l’instruction COPY. 

> [!NOTE]  
> L’instruction COPY est actuellement en préversion publique. Pour fournir un feedback, envoyez un e-mail à la liste de distribution suivante : sqldwcopypreview@service.microsoft.com.
>
        
 
> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>ELT, qu’est-ce que ça veut dire ?

ELT (Extract, Load, and Transform - Extraire, charger et transformer) est un processus par lequel des données sont extraites d’un système source, chargées dans un entrepôt de données, puis transformées. 

Les étapes de base pour implémenter un processus ELT pour SQL Data Warehouse sont les suivantes :

1. Extrayez les données sources dans des fichiers texte.
2. Placez les données dans le stockage Blob Azure ou Azure Data Lake Store.
3. Préparez les données pour le chargement.
4. Chargez les données dans des tables intermédiaires SQL Data Warehouse avec PolyBase ou la commande COPY. 
5. Transformez les données.
6. Insérez les données dans des tables de production.


Pour suivre un tutoriel sur le chargement PolyBase, consultez [Utiliser PolyBase pour charger des données depuis Stockage Blob Azure sur Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Pour en savoir plus, voir l’article de blog [Loading patterns](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/) (Modèles de charge). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraire les données sources dans des fichiers texte

L’extraction des données à partir de votre système source dépend de l’emplacement de stockage.  L’objectif est de déplacer les données dans PolyBase et les fichiers texte délimités ou CSV pris en charge par COPY. 

### <a name="polybase-and-copy-external-file-formats"></a>Formats des fichiers externes PolyBase et COPY

Avec PolyBase et l’instruction COPY, vous pouvez charger des données à partir de fichiers texte ou CSV encodés au format UTF-8 et UTF-16. En plus des fichiers texte délimités ou CSV, il charge des données à partir de formats de fichiers Hadoop, comme ORC et Parquet. PolyBase et l’instruction COPY peuvent aussi charger des données à partir de fichiers compressés Gzip et Snappy. Le format ASCII étendu de largeur fixe et les formats imbriqués, comme WinZip ou XML, ne sont pas pris en charge. Si vous exportez à partir de SQL Server, vous pouvez utiliser l’[outil en ligne de commande bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) pour exporter les données dans des fichiers texte délimités. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Placer les données dans le stockage Blob Azure ou Azure Data Lake Store

Pour charger les données dans Stockage Azure, vous pouvez les déplacer dans [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md) ou dans [Azure Data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md). Quel que soit l’emplacement choisi, les données doivent être stockées dans des fichiers texte. PolyBase et l’instruction COPY peuvent charger depuis l’un ou l’autre emplacement.

Voici des outils et services que vous pouvez utiliser pour déplacer des données dans le stockage Azure.

- Le service [Azure ExpressRoute](../expressroute/expressroute-introduction.md) améliore le débit, les performances et la prévisibilité du réseau. ExpressRoute est un service qui achemine vos données via une connexion privée dédiée vers Azure. Les connexions ExpressRoute n’acheminent pas vos données via le réseau Internet public. Elles offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions publiques sur Internet.
- L’[utilitaire AZCopy](../storage/common/storage-moving-data.md) déplace les données vers le stockage Azure via l’Internet public. Il fonctionne si la taille de vos données ne dépasse pas les 10 To. Pour effectuer des chargements réguliers avec AZCopy, testez la vitesse du réseau pour voir si elle est acceptable. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) dispose d’une passerelle que vous pouvez installer sur votre serveur local. Ensuite, vous pouvez créer un pipeline pour déplacer des données à partir de votre serveur local vers le stockage Azure. Pour utiliser Data Factory avec SQL Data Warehouse, consultez [Chargement de données dans SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Préparer les données pour le chargement

Avant de pouvoir charger les données de votre compte de stockage dans SQL Data Warehouse, vous devrez peut-être les préparer et les nettoyer. Vous pouvez préparer vos données pendant qu’elles sont dans la source, pendant que vous exportez les données dans des fichiers texte ou une fois que les données se trouvent dans le stockage Azure.  Plus vous les préparez tôt, plus ce sera facile.  

### <a name="define-external-tables"></a>Définir des tables externes

Si vous utilisez PolyBase, vous devez définir des tables externes dans votre entrepôt de données avant le chargement. Les tables externes ne sont pas nécessaires pour l’instruction COPY. PolyBase utilise des tables externes pour définir les données dans le stockage Azure et y accéder. Une table externe est similaire à une vue de base de données. La table externe contient le schéma de table et pointe vers les données stockées en dehors de l’entrepôt de données. 

La définition des tables externes implique de spécifier la source des données, le format des fichiers texte et les définitions de la table. Les rubriques sur la syntaxe T-SQL dont vous aurez besoin sont :
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Lors du chargement de fichiers Parquet, le mappage des types de données avec SQL DW est :

| **Type de données Parquet** |                      **Type de données SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        string         |                            nchar                             |
|        string         |                           nvarchar                           |
|        string         |                             char                             |
|        string         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        décimal        |                            décimal                           |

Pour voir un exemple de création d’objets externes, consultez l’étape [Créer des tables externes](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) du didacticiel sur le chargement.

### <a name="format-text-files"></a>Formater les fichiers texte

Si vous utilisez PolyBase, les objets externes définis doivent aligner les lignes des fichiers texte avec la table externe et la définition du format de fichier. Les données de chaque ligne du fichier texte doivent être alignées avec la définition de la table.
Pour formater les fichiers texte :

- Si vos données proviennent d’une source non relationnelle, vous devez les transformer en lignes et en colonnes. Que les données proviennent d’une source relationnelle ou non relationnelle, elles doivent être transformées pour être alignées avec les définitions des colonnes pour la table dans laquelle vous souhaitez les charger. 
- Formatez les données dans le fichier texte pour les aligner avec les types de colonnes et de données dans la table de destination SQL Data Warehouse. Un décalage entre les types de données dans les fichiers texte externes et la table de l’entrepôt de données cause le rejet des lignes lors du chargement.
- Séparez les champs dans le fichier texte à l’aide d’une marque de fin.  Assurez-vous d’utiliser un caractère ou une séquence de caractères qui ne se trouve pas dans votre source de données. Utilisez la marque de fin que vous avez spécifiée avec l’instruction [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase-or-the-copy-statement"></a>4. Charger les données dans des tables intermédiaires SQL Data Warehouse avec PolyBase ou la commande COPY

Il est recommandé de charger des données dans une table de mise en lots. Les tables de mise en lots vous permettent de gérer les erreurs sans interférer avec les tables de production. Une table de mise en lots vous donne également la possibilité d’utiliser SQL Data Warehouse MPP pour transformer les données avant de les insérer dans des tables de production. La table doit être créée au préalable lors du chargement dans une table intermédiaire avec COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Options de chargement avec PolyBase et l’instruction COPY

Pour charger des données avec PolyBase, vous pouvez utiliser l’une des options de chargement suivantes :

- [PolyBase avec T-SQL](load-data-from-azure-blob-storage-using-polybase.md) fonctionne bien lorsque vos données se trouvent dans le stockage Blob Azure ou dans Azure Data Lake Store. Il vous offre un contrôle optimal sur le processus de chargement, mais nécessite également que vous définissiez des objets de données externes. Les autres méthodes définissent ces objets en arrière-plan pendant que vous mappez les tables sources vers les tables de destination.  Pour orchestrer les chargements T-SQL, vous pouvez utiliser Azure Data Factory, SSIS ou les fonctions Azure. 
- [PolyBase avec SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) fonctionne bien lorsque vos données sources se trouvent dans SQL Server, que ce soit SQL Server sur site ou dans le cloud. SSIS définit le mappage de la table « source vers destination » et orchestre aussi le chargement. Si vous disposez déjà de packages SSIS, vous pouvez modifier les packages pour travailler avec le nouvel entrepôt de données de destination. 
- [PolyBase et l’instruction COPY avec Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) est un autre outil d’orchestration.  Il définit un pipeline et planifie les travaux. 
- [PolyBase avec Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) transfère les données d’une table SQL Data Warehouse vers une trame de données Databricks et/ou écrit des données d’une trame de données Databricks dans une table SQL Data Warehouse à l’aide de la technologie PolyBase.

### <a name="other-loading-options"></a>Autres options de chargement

En plus de PolyBase et de l’instruction COPY, vous pouvez utiliser [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) ou l’[API SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). L’outil bcp charge directement dans SQL Data Warehouse sans passer par le stockage Blob Azure et est destiné uniquement aux petits chargements. Notez que les performances de chargement de ces options sont beaucoup plus lentes qu’avec PolyBase et l’instruction COPY. 


## <a name="5-transform-the-data"></a>5. Transformer les données

Pendant que les données se trouvent dans la table de mise en lots, effectuez les transformations requises par votre charge de travail. Déplacez ensuite les données dans une table de production.


## <a name="6-insert-the-data-into-production-tables"></a>6. Insérer les données dans des tables de production

L’instruction INSERT INTO... SELECT déplace les données depuis la table de mise en lots vers la table permanente. 

Lorsque vous concevez un processus ETL, commencez par exécuter le processus sur un petit échantillon. Essayez d’extraire 1 000 lignes de la table dans un fichier, déplacez-le vers Azure, puis essayez de le charger dans une table de mise en lots. 


## <a name="partner-loading-solutions"></a>Solutions de chargement des partenaires

La plupart de nos partenaires proposent des solutions de chargement. Pour en savoir plus, consultez la liste de nos [partenaires de solutions](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des instructions sur le chargement, consultez [Meilleures pratiques de chargement de données](guidance-for-loading-data.md).


