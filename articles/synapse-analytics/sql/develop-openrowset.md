---
title: Guide pratique pour utiliser OPENROWSET dans SQL à la demande (préversion)
description: Cet article décrit la syntaxe de OPENROWSET dans SQL à la demande (préversion) et explique comment utiliser des arguments.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a03c031f8874471794f2533285ce65b395d43c2d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86241996"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Guide pratique pour utiliser OPENROWSET avec SQL à la demande (préversion)

La fonction `OPENROWSET(BULK...)` vous permet d’accéder à des fichiers dans Stockage Azure. La fonction `OPENROWSET` lit le contenu d’une source de données distante (par exemple, un fichier) et retourne le contenu sous la forme d’un ensemble de lignes. Dans la ressource SQL à la demande (préversion), le fournisseur d’ensembles de lignes en bloc OPENROWSET est accessible en appelant la fonction OPENROWSET et en spécifiant l’option BULK.  

Il est possible de référencer la fonction `OPENROWSET` dans la clause `FROM` d’une requête comme s’il s’agissait d’un nom de table `OPENROWSET`. Elle prend en charge les opérations en bloc par l’intermédiaire d’un fournisseur BULK intégré qui permet de lire les données d’un fichier et de les retourner sous la forme d’un ensemble de lignes.

## <a name="data-source"></a>Source de données

La fonction OPENROWSET dans Synapse SQL lit le contenu des fichiers à partir d’une source de données. La source de données est un compte de stockage Azure et peut être référencée explicitement dans la fonction `OPENROWSET` ou être déduite de façon dynamique à partir de l’URL des fichiers que vous souhaitez lire.
La fonction `OPENROWSET` peut éventuellement contenir un paramètre `DATA_SOURCE` pour spécifier la source de données contenant des fichiers.
- Vous pouvez utiliser la fonction `OPENROWSET` sans le paramètre `DATA_SOURCE` pour lire directement le contenu des fichiers à partir de l’emplacement d’URL spécifié en tant qu’option `BULK` :

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

Il s’agit d’un moyen simple et rapide de lire le contenu des fichiers sans configuration préalable. Cette option vous permet d’utiliser l’option d’authentification de base pour accéder au stockage (authentification directe Azure AD pour les connexions Azure AD et jeton SAP pour les connexions SQL). 

- Vous pouvez utiliser la fonction `OPENROWSET` avec le paramètre `DATA_SOURCE` pour accéder aux fichiers sur un compte de stockage spécifié :

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```


    Cette option vous permet de configurer l’emplacement du compte de stockage dans la source de données et de spécifier la méthode d’authentification à utiliser pour accéder au stockage. 
    
    > [!IMPORTANT]
    > La fonction `OPENROWSET` sans le paramètre `DATA_SOURCE` fournit un moyen simple et rapide d’accéder aux fichiers de stockage, mais offre des options d’authentification limitées. Par exemple, les principaux Azure AD peuvent accéder à des fichiers uniquement en utilisant leur [identité Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity), ou à des fichiers publiquement disponibles. Si vous avez besoin d’options d’authentification plus puissantes, utilisez l’option `DATA_SOURCE` et définissez les informations d’identification que vous souhaitez utiliser pour accéder au stockage.


## <a name="security"></a>Sécurité

Un utilisateur de base de données doit disposer de l’autorisation `ADMINISTER BULK OPERATIONS` pour utiliser la fonction `OPENROWSET`.

L’administrateur de stockage doit également permettre à un utilisateur d’accéder aux fichiers en fournissant un jeton SAP valide ou en permettant au principal Azure AD d’accéder aux fichiers de stockage. Pour en savoir plus sur le contrôle d’accès au stockage, consultez [cet article](develop-storage-files-storage-access-control.md).

La fonction `OPENROWSET` utilise les règles suivantes pour déterminer comment s’authentifier auprès du stockage :
- Dans `OPENROWSET` sans `DATA_SOURCE`, le mécanisme d’authentification dépend du type d’appelant.
  - Tout utilisateur peut utiliser `OPENROWSET` sans `DATA_SOURCE` pour lire des fichiers accessibles publiquement sur le stockage Azure.
  - Les connexions Azure AD peuvent accéder à des fichiers protégés en utilisant leur propre [identité Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) si le stockage Azure permet à l’utilisateur Azure AD d’accéder aux fichiers sous-jacents (par exemple, si l’appelant dispose de l’autorisation `Storage Reader` sur le stockage Azure).
  - Des connexions SQL peuvent également recourir à `OPENROWSET` sans `DATA_SOURCE` pour accéder à des fichiers en disponibilité publique, à des fichiers protégés par jeton SAP ou à l’identité managée d’un espace de travail Synapse. Vous devez [créer des informations d’identification incluses dans l’étendue du serveur](develop-storage-files-storage-access-control.md#examples) pour autoriser l’accès aux fichiers de stockage. 
- Dans `OPENROWSET` avec `DATA_SOURCE`, le mécanisme d’authentification est défini dans les informations d’identification étendues à la base de données affectées à la source de données référencée. Cette option vous permet d’accéder au stockage publiquement disponible, ou d’accéder au stockage à l’aide du jeton SAP, de l’identité managée de l’espace de travail ou de l’[identité Azure AD de l’appelant](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (si celui-ci est un principal Azure AD). Si `DATA_SOURCE` fait référence à un stockage Azure qui n’est pas public, vous devez [créer des informations d’identification étendues à la base de données](develop-storage-files-storage-access-control.md#examples) et y faire référence dans `DATA SOURCE` pour autoriser l’accès aux fichiers de stockage.

L’appelant doit disposer de l’autorisation `REFERENCES` sur les informations d’identification afin de pouvoir les utiliser pour l’authentification auprès du stockage.

## <a name="syntax"></a>Syntaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Arguments

Vous avez deux possibilités pour les fichiers d’entrée qui contiennent les données cibles à interroger. Les valeurs autorisées sont :

- 'CSV' - Comprend tout fichier texte délimité par des séparateurs de lignes/colonnes. N’importe quel caractère peut être utilisé comme séparateur de champs, par exemple TSV : FIELDTERMINATOR = tab.

- 'PARQUET' - Fichier binaire au format Parquet 

**'unstructured_data_path'**

L’élément unstructured_data_path qui établit un chemin d’accès aux données peut être un chemin d’accès absolu ou relatif :
- Un chemin d’accès absolu au format « \<prefix>://\<storage_account_path>/\<storage_path> » permet à un utilisateur de lire directement les fichiers.
- Chemin relatif au format « < storage_path > » qui doit être utilisé avec le paramètre `DATA_SOURCE` et décrit le modèle de fichier dans l’emplacement <storage_account_path> défini dans `EXTERNAL DATA SOURCE`. 

 Vous trouverez ci-dessous les valeurs <storage account path> appropriées qui établiront un lien à votre source de données externe particulière. 

| Source de données externe       | Préfixe | Chemin de compte de stockage                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Stockage Blob Azure         | http[s]  | \<storage_account>.blob.core.windows.net/$$$path/file   |
| Stockage Blob Azure         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/$$$path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net $$$/path/file   |
| Azure Data Lake Store Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/chemin/fichier](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

 Spécifie un chemin au sein de votre stockage qui pointe vers le dossier ou le fichier que vous souhaitez lire. Si le chemin pointe vers un conteneur ou un dossier, tous les fichiers sont lus à partir de ce conteneur ou dossier particulier. Les fichiers des sous-dossiers ne sont pas inclus. 

 Vous pouvez utiliser des caractères génériques pour cibler plusieurs fichiers ou dossiers. L’utilisation de plusieurs caractères génériques non consécutifs est autorisée.
Dans l’exemple ci-dessous, tous les fichiers *csv* sont lus en débutant par *population* à partir de tous les dossiers en débutant par */csv/population* :  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Si vous spécifiez l’élément unstructured_data_path comme dossier, une requête SQL à la demande récupère les fichiers de ce dossier. 

> [!NOTE]
> Contrairement à Hadoop et à PolyBase, SQL à la demande ne retourne pas de sous-dossiers. Par ailleurs, à la différence d’Hadoop et de PolyBase, SQL à la demande retourne les fichiers dont le nom commence par un trait de soulignement (_) ou un point (.).

Dans l’exemple ci-dessous, si l’élément unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, une requête SQL à la demande retournera des lignes de mydata.txt et de _hidden.txt. Il ne retournera pas mydata2.txt ni mydata3.txt, car ces fichiers se trouvent dans un sous-dossier.

![Données récursives pour les tables externes](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

La clause WITH vous permet de préciser les colonnes que vous souhaitez lire des fichiers.

- Pour les fichiers de données CSV, si vous souhaitez lire toutes les colonnes, indiquez les noms des colonnes et leur type de données. Si vous désirez un sous-ensemble de colonnes, utilisez des nombres ordinaux pour sélectionner les colonnes des fichiers de données d’origine par ordinal. Les colonnes seront liées par la désignation ordinale. 

    > [!IMPORTANT]
    > La clause WITH est obligatoire pour les fichiers CSV.
    >
    
- Pour les fichiers de données Parquet, fournissez des noms de colonne qui correspondent aux noms des colonnes des fichiers de données d’origine. Les colonnes seront liées par nom. Si la clause WITH est omise, toutes les colonnes des fichiers Parquet seront retournées.

column_name = Nom de la colonne de sortie. S’il est fourni, ce nom remplace le nom de la colonne dans le fichier source.

column_type = Type de données de la colonne de sortie. La conversion implicite du type de données aura lieu ici.

column_ordinal = Nombre ordinal de la colonne dans le ou les fichiers sources. Cet argument est ignoré pour les fichiers Parquet, car la liaison est effectuée par nom. L’exemple suivant retourne une deuxième colonne uniquement depuis un fichier CSV :

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Spécifie la marque de fin de champ à utiliser. La marque de fin de champ par défaut est une virgule («  **,**  »).

ROWTERMINATOR ='row_terminator'`

Spécifie l’indicateur de fin de ligne à utiliser. Si la marque de fin de ligne n’est pas spécifiée, l’une des marques par défaut sera utilisée. Les indicateurs de fin par défaut pour PARSER_VERSION = ’1.0’ sont \r\n, \n et \r. Les indicateurs de fin par défaut pour PARSER_VERSION = ’2.0’ sont \r\n et \n.

ESCAPE_CHAR = 'char'

Spécifie le caractère dans le fichier qui est utilisé pour se placer lui-même dans une séquence d’échappement ainsi que toutes les valeurs de délimiteur dans le fichier. Si le caractère d’échappement est suivi d’une valeur autre que lui-même, ou que l’une des valeurs de délimiteur, le caractère d’échappement est supprimé lors de la lecture de la valeur. 

Le paramètre ESCAPE_CHAR est appliqué, que FIELDQUOTE soit ou non activé. Il ne sera pas utilisé comme caractère d’échappement devant le caractère de délimitation. Le caractère de délimitation doit être placé dans une séquence d’échappement avec un autre caractère de délimitation. Le caractère de délimitation peut apparaître dans la valeur de colonne seulement si la valeur est encapsulée avec des caractères de délimitation.

FIRSTROW = 'first_row' 

Numéro de la première ligne à charger. La valeur par défaut est 1. Cela indique la première ligne du fichier de données spécifié. Les numéros des lignes sont déterminés en comptant les indicateurs de fin de ligne. FIRSTROW commence à 1.

FIELDQUOTE = 'field_quote' 

Spécifie un caractère qui sera utilisé comme caractère de guillemet dans le fichier CSV. S’il n’est pas spécifié, le guillemet (") sera utilisé. 

DATA_COMPRESSION = ’data_compression_method’

Spécifie la méthode de compression. La méthode de compression suivante est prise en charge :

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = ’parser_version’

Spécifie la version d’analyseur à utiliser lors de la lecture de fichiers. Sont actuellement prises en charge les versions 1.0 et 2.0 de l’analyseur CSV :

- PARSER_VERSION = ’1.0’
- PARSER_VERSION = ’2.0’

La version 1.0 de l’analyseur CSV (version par défaut) est riche en fonctionnalités, tandis que la version 2.0, conçue pour les performances, ne prend pas en charge l’ensemble des options et des encodages. 

Caractéristiques la version 2.0 de l’analyseur CSV :

- Certains types de données ne sont pas pris en charge.
- La limite maximale de taille de ligne est de 8 Mo.
- Les options suivantes ne sont pas prises en charge : DATA_COMPRESSION.
- La chaîne vide entre guillemets ("") est interprétée comme une chaîne vide.

## <a name="examples"></a>Exemples

L’exemple suivant retourne seulement deux colonnes avec les nombres ordinaux 1 et 4 à partir des fichiers population*.csv. Étant donné qu’il n’y a pas de ligne d’en-tête dans les fichiers, la lecture commence à la première ligne :

```sql
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

L’exemple suivant retourne toutes les colonnes de la première ligne du jeu de données de recensement au format Parquet, sans spécifier les noms des colonnes et les types de données : 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples, consultez le [Guide de démarrage rapide du stockage de données de requête](query-data-storage.md) pour savoir comment utiliser `OPENROWSET` pour lire les formats de fichiers [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md) et [JSON](query-json-files.md). Vous pouvez également apprendre à enregistrer les résultats de votre requête dans Stockage Azure à l’aide de [CETAS](develop-tables-cetas.md).
