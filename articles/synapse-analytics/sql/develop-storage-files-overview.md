---
title: Interroger des fichiers de stockage avec SQL à la demande (préversion) dans Synapse SQL
description: Décrit l’interrogation des fichiers de stockage avec des ressources SQL à la demande (préversion) dans Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 941fa8d2570d22b6c2a54de02a61b4a7ece2e632
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691874"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Interroger des fichiers de stockage avec des ressources SQL à la demande (préversion) dans Synapse SQL

SQL à la demande (préversion) vous permet d’interroger des données dans votre lac de données. Ce service offre une surface d’exposition de requête T-SQL qui prend en charge les requêtes de données semi-structurées et non structurées.

Pour l’interrogation, les aspects T-SQL suivants sont pris en charge :

- Surface d’exposition [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) complète, y compris la majorité des fonctions, opérateurs, etc. SQL.
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) crée une [table externe](develop-tables-external-tables.md), puis exporte, en parallèle, les résultats d’une instruction SELECT Transact-SQL vers le stockage Azure.

Pour plus d’informations sur ce qui est et n’est pas actuellement pris en charge, consultez l’article [Vue d’ensemble de SQL à la demande](on-demand-workspace-overview.md).

Lorsque les utilisateurs Azure AD exécutent des requêtes, par défaut les comptes de stockage sont accessibles à l’aide du protocole d’authentification directe Azure AD. Ainsi, les utilisateurs sont impersonnés, et les autorisations vérifiées au niveau du stockage. Vous pouvez [contrôler l’accès au stockage](develop-storage-files-storage-access-control.md) en fonction de vos besoins.

## <a name="extensions"></a>Extensions

Pour faciliter la prise en charge de l’interrogation sur place des données qui se trouvent dans les fichiers du stockage Azure, SQL à la demande utilise la fonction [OPENROWSET](develop-openrowset.md) avec des fonctionnalités supplémentaires :

- [Interroger plusieurs fichiers ou dossiers](#query-multiple-files-or-folders)
- [Format de fichier PARQUET](#parquet-file-format)
- [Options supplémentaires pour l’utilisation d’un texte délimité (marque de fin de champ, marque de fin de ligne, caractère d’échappement)](#additional-options-for-working-with-delimited-text)
- [Lire un sous-ensemble choisi de colonnes](#read-a-chosen-subset-of-columns)
- [Inférence de schéma](#schema-inference)
- [Fonction filename](#filename-function)
- [Fonction filepath](#filepath-function)
- [Utiliser des types complexes et des structures de données imbriquées ou répétées](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Interroger plusieurs fichiers ou dossiers

Pour exécuter une requête T-SQL sur un ensemble de fichiers, à l’intérieur d’un dossier ou d’un ensemble de dossiers, tout en les traitant en tant qu’entité ou ensemble de lignes unique, fournissez un chemin de dossier ou de modèle (à l’aide de caractères génériques) sur un ensemble de fichiers ou de dossiers.

Les règles suivantes s’appliquent :

- Les modèles peuvent apparaître comme faisant partie d’un chemin de répertoire, ou dans un nom de fichier.
- Plusieurs modèles peuvent apparaître dans la même étape de répertoire ou le même nom de fichier.
- S’il y a plusieurs caractères génériques, les fichiers présents dans tous les chemins correspondants sont inclus dans le jeu de fichiers obtenu.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Pour obtenir des exemples d’utilisation, reportez-vous à l’article [Interroger des dossiers et plusieurs fichiers](query-folders-multiple-csv-files.md).

### <a name="parquet-file-format"></a>Format de fichier PARQUET

Pour interroger les données sources Parquet, utilisez FORMAT = 'PARQUET'

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Pour obtenir des exemples d’utilisation, consultez l’article [Interroger des fichiers Parquet](query-parquet-files.md).

### <a name="additional-options-for-working-with-delimited-text"></a>Options supplémentaires pour l’utilisation de texte délimité

Ces paramètres supplémentaires sont introduits pour l’utilisation de fichiers CSV (texte délimité) :

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Spécifie le caractère dans le fichier qui est utilisé pour se placer lui-même dans une séquence d’échappement ainsi que toutes les valeurs de délimiteur dans le fichier. Si le caractère d’échappement est suivi d’une valeur autre que lui-même, ou que l’une des valeurs de délimiteur, le caractère d’échappement est supprimé lors de la lecture de la valeur.
Le paramètre ESCAPE_CHAR est appliqué, que FIELDQUOTE soit ou non activé. Il ne sera pas utilisé comme caractère d’échappement devant le caractère de délimitation. Le caractère de délimitation est placé dans une séquence d’échappement avec des guillemets doubles conformément au comportement CSV d’Excel.
- FIELDTERMINATOR = 'field_terminator' Spécifie la marque de fin de champ à utiliser. La virgule («  **,**  ») est la marque de fin de champ par défaut.
- ROWTERMINATOR = 'row_terminator' Spécifie la marque de fin de ligne à utiliser. Par défaut, il s’agit d’un caractère de nouvelle ligne :  **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Lire un sous-ensemble choisi de colonnes

Pour spécifier les colonnes que vous souhaitez lire, vous pouvez fournir une clause WITH facultative dans votre instruction OPENROWSET.

- S’il y a des fichiers de données CSV, pour lire toutes les colonnes, indiquez les noms des colonnes et leur type de données. Si vous souhaitez un sous-ensemble de colonnes, utilisez des nombres ordinaux pour sélectionner les colonnes des fichiers de données d’origine par ordinal. Les colonnes sont ainsi liées par la désignation ordinale.
- S’il existe des fichiers de données Parquet, fournissez des noms de colonne qui correspondent aux noms des colonnes dans les fichiers de données d’origine. Les colonnes sont alors liées par nom.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Pour obtenir des exemples, reportez-vous à [Lire des fichiers CSV sans spécifier toutes les colonnes](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Inférence de schéma

En omettant la clause WITH dans l’instruction OPENROWSET, vous pouvez demander au service de détecter automatiquement (déduire) le schéma à partir des fichiers sous-jacents.

> [!NOTE]
> Cette possibilité ne fonctionne actuellement que pour le format de fichier PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Assurez-vous que des [types de données déduits appropriés](best-practices-sql-on-demand.md#check-inferred-data-types) sont utilisés pour des performances optimales. 

### <a name="filename-function"></a>Fonction filename

Cette fonction retourne le nom du fichier d’où provient la ligne. 

Pour interroger des fichiers spécifiques, lisez la section Filename dans l’article [Interroger des fichiers spécifiques](query-specific-files.md#filename).

Le type de données de retour est nvarchar (1024). Pour des performances optimales, castez toujours le résultat de la fonction filename en un type de données approprié. Si vous utilisez le type de données caractères, assurez-vous que la longueur appropriée est utilisée.

### <a name="filepath-function"></a>Fonction filepath

Cette fonction retourne un chemin complet ou une partie de chemin :

- En cas d’appel sans paramètre, elle retourne le chemin complet du fichier d’où est issue une ligne.
- En cas d’appel avec paramètre, elle retourne une partie du chemin qui correspond au caractère générique, à la position spécifiée dans le paramètre. Par exemple, la valeur de paramètre 1 retourne une partie du chemin qui correspond au premier caractère générique.

Pour plus d’informations, consultez la section Filepath dans l’article [Interroger des fichiers spécifiques](query-specific-files.md#filepath).

Le type de données de retour est nvarchar (1024). Pour des performances optimales, castez toujours le résultat de la fonction filepath en un type de données approprié. Si vous utilisez le type de données caractères, assurez-vous que la longueur appropriée est utilisée.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Utiliser des types complexes et des structures de données imbriquées ou répétées

Pour faciliter l’utilisation des données stockées dans des types de données imbriquées ou répétées, comme dans les fichiers [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types), SQL à la demande a ajouté les extensions ci-dessous.

#### <a name="project-nested-or-repeated-data"></a>Projeter des données imbriquées ou répétées

Pour projeter des données, exécutez une instruction SELECT sur le fichier Parquet qui contient des colonnes de types de données imbriquées. En sortie, les valeurs imbriquées sont sérialisées en JSON et retournées sous la forme d’un type de données SQL varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Pour plus d’informations, reportez-vous à la section Projeter des données imbriquées ou répétées dans l’article [Interroger des types imbriqués Parquet](query-parquet-nested-types.md#project-nested-or-repeated-data).

#### <a name="access-elements-from-nested-columns"></a>Accéder aux éléments à partir de colonnes imbriquées

Pour accéder aux éléments imbriqués à partir d’une colonne imbriquée, telle que Struct, utilisez la « notation par points » pour concaténer les noms de champ dans le chemin. Indiquez le chemin en tant que column_name dans la clause WITH de la fonction OPENROWSET.

L’exemple de fragment de syntaxe est le suivant :

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Par défaut, la fonction OPENROWSET associe le chemin et le nom de champ de la source aux noms de colonne fournis dans la clause WITH. Les éléments contenus à des niveaux d’imbrication différents dans le même fichier Parquet source sont accessibles via la clause WITH.

**Valeurs retournées**

- La fonction retourne une valeur scalaire, telle que int, decimal et varchar, à partir de l’élément spécifié, et dans le chemin spécifié, pour tous les types Parquet qui ne sont pas dans le groupe Type imbriqué.
- Si le chemin pointe vers un élément qui est d’un type imbriqué, la fonction retourne un fragment JSON à partir de l’élément du plus haut niveau dans le chemin spécifié. Le fragment JSON est de type varchar(8000).
- Si la propriété est introuvable au niveau de l’élément column_name spécifié, la fonction retourne une erreur.
- Si la propriété est introuvable au niveau de l’élément column_name précisé, en fonction du [mode de chemin](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), la fonction retourne une erreur en mode strict, ou retourne la valeur Null en mode lax.

Pour obtenir des exemples de requêtes, consultez la section Accéder aux éléments à partir de colonnes imbriquées dans l’article [Interroger des types imbriqués Parquet](query-parquet-nested-types.md#access-elements-from-nested-columns).

#### <a name="access-elements-from-repeated-columns"></a>Accéder aux éléments à partir de colonnes répétées

Pour accéder aux éléments d’une colonne répétée, par exemple un élément d’un tableau ou d’un plan, utilisez la fonction [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour chaque élément scalaire que vous devez projeter, et fournissez les informations suivantes :

- Colonne imbriquée ou répétée, en tant que premier paramètre
- Un [chemin JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) qui spécifie la propriété ou l’élément auquel accéder, en tant que second paramètre

Pour accéder à des éléments non scalaires à partir d’une colonne répétée, utilisez la fonction [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour chaque élément non scalaire que vous devez projeter, et fournissez les informations suivantes :

- Colonne imbriquée ou répétée, en tant que premier paramètre
- Un [chemin JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) qui spécifie la propriété ou l’élément auquel accéder, en tant que second paramètre

Consultez le fragment de syntaxe ci-dessous :

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Vous trouverez des exemples de requêtes permettant d’accéder à des éléments depuis les colonnes répétées dans l’article [Interroger des types imbriqués Parquet](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interrogation de différents types de fichiers ainsi que la création et l’utilisation de vues, consultez les articles suivants :

- [Interroger un fichier CSV](query-single-csv-file.md)
- [Interroger des fichiers Parquet](query-parquet-files.md)
- [Interroger des fichiers JSON](query-json-files.md)
- [Interroger des types imbriqués Parquet](query-parquet-nested-types.md)
- [Interroger des dossiers et plusieurs fichiers CSV](query-folders-multiple-csv-files.md)
- [Utiliser des métadonnées de fichier dans les requêtes](query-specific-files.md)
- [Créer et utiliser des vues](create-use-views.md)
