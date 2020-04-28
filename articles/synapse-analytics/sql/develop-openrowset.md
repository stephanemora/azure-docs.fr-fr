---
title: Guide pratique pour utiliser OPENROWSET dans SQL à la demande (préversion)
description: Cet article décrit la syntaxe de OPENROWSET dans SQL à la demande (préversion) et explique comment utiliser des arguments.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680493"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Guide pratique pour utiliser OPENROWSET avec SQL à la demande (préversion)

La fonction OPENROWSET (BULK...) vous permet d’accéder aux fichiers dans le stockage Azure. Dans la ressource SQL à la demande (préversion), le fournisseur d’ensembles de lignes en bloc OPENROWSET est accessible en appelant la fonction OPENROWSET et en spécifiant l’option BULK.  

La fonction OPENROWSET peut être référencée dans la clause FROM d’une requête comme s’il s’agissait du nom d’une table OPENROWSET. Elle prend en charge les opérations en bloc par l’intermédiaire d’un fournisseur BULK intégré qui permet de lire les données d’un fichier et de les retourner sous la forme d’un ensemble de lignes.

OPENROWSET n’est pas pris en charge actuellement dans un pool SQL.

## <a name="syntax"></a>Syntaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Arguments

Vous avez deux possibilités pour les fichiers d’entrée qui contiennent les données cibles à interroger. Les valeurs autorisées sont :

- 'CSV' - Comprend tout fichier texte délimité par des séparateurs de lignes/colonnes. N’importe quel caractère peut être utilisé comme séparateur de champs, par exemple TSV : FIELDTERMINATOR = tab.

- 'PARQUET' - Fichier binaire au format Parquet 

**'unstructured_data_path'**

L’élément unstructured_data_path qui établit un chemin aux données est structuré comme suit :  
'\<préfixe>://\<chemin_compte_stockage>/\<chemin_stockage>'
 
 
 Vous trouverez ci-dessous les chemins de compte de stockage appropriés qui seront liés à votre source de données externe particulière. 

| Source de données externe       | Préfixe | Chemin de compte de stockage                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Stockage Blob Azure         | https  | \<compte_stockage>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<compte_stockage>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<compte_stockage>.dfs.core.windows.net              |
||||

'\<compte_stockage>'

 Spécifie un chemin au sein de votre stockage qui pointe vers le dossier ou le fichier que vous souhaitez lire. Si le chemin pointe vers un conteneur ou un dossier, tous les fichiers sont lus à partir de ce conteneur ou dossier particulier. Les fichiers des sous-dossiers ne sont pas inclus. 
 
 Vous pouvez utiliser des caractères génériques pour cibler plusieurs fichiers ou dossiers. L’utilisation de plusieurs caractères génériques non consécutifs est autorisée.
Dans l’exemple ci-dessous, tous les fichiers *csv* sont lus en débutant par *population* à partir de tous les dossiers en débutant par */csv/population* :  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Si vous spécifiez l’élément unstructured_data_path comme dossier, une requête SQL à la demande récupère les fichiers de ce dossier. 

> [!NOTE]
> Contrairement à Hadoop et à PolyBase, SQL à la demande ne retourne pas de sous-dossiers. Par ailleurs, à la différence d’Hadoop et de PloyBase, SQL à la demande retourne les fichiers dont le nom commence par un trait de soulignement (_) ou un point (.).

Dans l’exemple ci-dessous, si l’élément unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, une requête SQL à la demande retournera des lignes de mydata.txt et de _hidden.txt. Il ne retournera pas mydata2.txt ni mydata3.txt, car ces fichiers se trouvent dans un sous-dossier.

![Données récursives pour les tables externes](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

La clause WITH vous permet de préciser les colonnes que vous souhaitez lire des fichiers.

- Pour les fichiers de données CSV, si vous souhaitez lire toutes les colonnes, indiquez les noms des colonnes et leur type de données. Si vous désirez un sous-ensemble de colonnes, utilisez des nombres ordinaux pour sélectionner les colonnes des fichiers de données d’origine par ordinal. Les colonnes seront liées par la désignation ordinale. 

> [!IMPORTANT]
> La clause WITH est obligatoire pour les fichiers CSV.
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

**\<options_bloc>**

FIELDTERMINATOR ='field_terminator'

Spécifie la marque de fin de champ à utiliser. La marque de fin de champ par défaut est une virgule («  **,**  »).

ROWTERMINATOR ='row_terminator'`

Spécifie l’indicateur de fin de ligne à utiliser. Par défaut, il s’agit d’un caractère de nouvelle ligne, tel que \r\n.

ESCAPE_CHAR = 'char'

Spécifie le caractère dans le fichier qui est utilisé pour se placer lui-même dans une séquence d’échappement ainsi que toutes les valeurs de délimiteur dans le fichier. Si le caractère d’échappement est suivi d’une valeur autre que lui-même, ou que l’une des valeurs de délimiteur, le caractère d’échappement est supprimé lors de la lecture de la valeur. 

Le paramètre ESCAPE_CHAR est appliqué, que FIELDQUOTE soit ou non activé. Il ne sera pas utilisé comme caractère d’échappement devant le caractère de délimitation. Le caractère de délimitation est placé dans une séquence d’échappement avec des guillemets doubles conformément au comportement CSV d’Excel.

FIRSTROW = 'first_row' 

Numéro de la première ligne à charger. La valeur par défaut est 1. Cela indique la première ligne du fichier de données spécifié. Les numéros des lignes sont déterminés en comptant les indicateurs de fin de ligne. FIRSTROW commence à 1.

FIELDQUOTE = 'field_quote' 

Spécifie un caractère qui sera utilisé comme caractère de guillemet dans le fichier CSV. S’il n’est pas spécifié, le guillemet (") sera utilisé. 

## <a name="examples"></a>Exemples

L’exemple suivant retourne seulement deux colonnes avec les nombres ordinaux 1 et 4 à partir des fichiers population*.csv. Étant donné qu’il n’y a pas de ligne d’en-tête dans les fichiers, la lecture commence à la première ligne :

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

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
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples, accédez à [Démarrages rapides](query-data-storage.md) ou enregistrez les résultats de votre requête dans le stockage Azure à l’aide de [CETAS](develop-tables-cetas.md).
