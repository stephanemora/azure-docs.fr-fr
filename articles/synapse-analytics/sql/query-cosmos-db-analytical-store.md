---
title: Interroger des données d’Azure Cosmos DB à l’aide d’un pool SQL serverless dans Azure Synapse Link (préversion)
description: Cet article explique comment interroger Azure Cosmos DB à l’aide de SQL à la demande dans Azure Synapse Link (préversion).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 2b1af6fa5b0ccb95476c4ae169481e4aaa15f4f9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737835"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Interroger des données d’Azure Cosmos DB avec un pool SQL serverless dans Azure Synapse Link (préversion)

Un pool Synapse SQL serverless vous permet d’analyser les données figurant dans vos conteneurs Azure Cosmos DB activés avec [Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) en quasi-temps réel, sans que cela affecte les performances de vos charges de travail transactionnelles. Il offre une syntaxe T-SQL familière pour interroger les données du [magasin analytique](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), et une connectivité intégrée à un vaste éventail d’outils décisionnels et d’interrogation ad hoc via l’interface T-SQL.

Pour l’interrogation d’Azure Cosmos DB, tout la surface d’exposition [SELECT](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) est prise en charge via la fonction [OPENROWSET](develop-openrowset.md), y compris la majorité des [fonctions et opérateurs SQL](overview-features.md). Vous pouvez également stocker les résultats de la requête qui lit des données d’Azure Cosmos DB ainsi que des données du Stockage Blob Azure ou d’Azure Data Lake Storage à l’aide de la commande [create external table as select](develop-tables-cetas.md#cetas-in-sql-on-demand). Actuellement, vous ne pouvez pas stocker les résultats d’une requête de pool SQL serverless dans Azure Cosmos DB à l’aide de [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

Cet article explique comment écrire une requête à l’aide d’un pool SQL serverless, qui interrogera les données de conteneurs Azure Cosmos DB pour lesquels la fonctionnalité Synapse Link est activée. [Ce tutoriel](./tutorial-data-analyst.md) fournit également des informations supplémentaires sur la création de vues de pool SQL serverless sur des conteneurs Azure Cosmos DB, et leur connexion à des modèles Power BI. 

> [!IMPORTANT]
> Ce tutoriel utilise un conteneur avec un [schéma bien défini Azure Cosmos DB](../../cosmos-db/analytical-store-introduction.md#schema-representation). L’expérience de requête qu’offre un pool SQL serverless pour un [schéma de fidélité optimale Azure Cosmos DB](#full-fidelity-schema) est un comportement temporaire qui sera modifié en fonction des commentaires sur la préversion. Ne vous fiez pas au schéma du jeu de résultats de la fonction `OPENROWSET` sans la clause `WITH` qui lit les données d’un conteneur avec un schéma de fidélité optimale, car l’expérience de requête pourrait être modifiée et alignée avec un schéma bien défini. Publiez vos commentaires sur le [Forum des commentaires Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics), ou contactez l’[équipe produit Synapse Link](mailto:cosmosdbsynapselink@microsoft.com) pour leur en faire part.

## <a name="overview"></a>Vue d’ensemble

Pour prendre en charge l’interrogation et l’analyse des données d’un magasin analytique Azure Cosmos DB, le pool SQL serverless utilise la syntaxe `OPENROWSET` suivante :

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

La chaîne de connexion Azure Cosmos DB spécifie le nom du compte Azure Cosmos DB, le nom de la base de données, la clé principale du compte de base de données et un nom de région facultatif pour la fonction `OPENROWSET`. 

> [!IMPORTANT]
> Veillez à utiliser l’alias après `OPENROWSET`. Il existe un [problème connu](#known-issues) qui provoque un problème de connexion à un point de terminaison Synapse serverless SQL si vous ne spécifiez pas l’alias après la fonction `OPENROWSET`.

Le format de la chaîne de connexion est le suivant :
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Le nom du conteneur Azure Cosmos DB est spécifié sans guillemets dans la syntaxe `OPENROWSET`. Si le nom du conteneur contient des caractères spéciaux (par exemple un tiret « - »), le nom doit être encapsulé entre crochets `[]` dans la syntaxe `OPENROWSET`.

> [!NOTE]
> Le pool SQL serverless ne prend pas en charge l’interrogation du magasin transactionnel d’Azure Cosmos DB.

## <a name="sample-data-set"></a>Exemple de jeu de données

Les exemples de cet article sont basés sur des données relatives aux [Cas de COVID-19 du Centre européen pour la prévention et le contrôle des maladies (CEPCM)](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) et du [COVID-19 Open Research DataSet (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Vous pouvez voir la licence et la structure des données sur ces pages, et télécharger des exemples de données pour les jeux de données [CEPCM](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) et [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json).

Pour suivre cet article montrant comment interroger les données de Cosmos DB avec un pool SQL serverless, veillez à créer les ressources suivantes :
* un compte de base de données Azure Cosmos DB [compatible Synapse Link](../../cosmos-db/configure-synapse-link.md) ;
* une base de données Azure Cosmos DB nommée `covid` ;
* deux conteneurs Azure Cosmos DB nommés `EcdcCases` et `Cord19` avec les exemples de jeux de données ci-dessus chargés.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Explorer des données d’Azure Cosmos DB avec une inférence de schéma automatique

Le moyen le plus simple d’explorer des données dans Azure Cosmos DB consiste à tirer parti de la fonctionnalité d’inférence de schéma automatique. En omettant la clause `WITH` de l’instruction `OPENROWSET`, vous pouvez donner pour instruction à un pool SQL serverless de détecter automatiquement (inférer) le schéma du magasin analytique du conteneur Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
Dans l’exemple ci-dessus, nous donnons pour instruction à un pool SQL serverless de se connecter à la base de données `covid` dans un compte Azure Cosmos DB `MyCosmosDbAccount` authentifié à l’aide de la clé Azure Cosmos DB (factice dans l’exemple ci-dessus). Nous accédons ensuite au magasin analytique `EcdcCases` du conteneur dans la région `West US 2`. Étant donné qu’il n’y a aucune projection de propriétés spécifiques, la fonction `OPENROWSET` retourne toutes les propriétés des éléments d’Azure Cosmos DB. 

En supposant que les éléments se trouvant dans le conteneur Cosmos DB aient les propriétés `date_rep`, `cases` et `geo_id`, les résultats de cette requête figurent dans le tableau suivant :

| date_rep | cas | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Si vous devez explorer les données de l’autre conteneur dans la même base de données Azure Cosmos DB, vous pouvez utiliser la même chaîne de connexion et le conteneur de référence requis comme troisième paramètre :

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Spécifier explicitement le schéma

Bien que la fonctionnalité d’inférence de schéma automatique dans `OPENROWSET` offre une expérience de requête simple et facile à utiliser, vos scénarios d’entreprise peuvent vous obliger à spécifier explicitement le schéma pour lire uniquement les propriétés pertinentes à partir des données d’Azure Cosmos DB.

`OPENROWSET` vous permet de spécifier de manière explicite les propriétés que vous souhaitez lire à partir des données du conteneur, ainsi que de spécifier leurs types de données. Supposons que nous avons importé des données du [jeu de données COVID du CEPCM](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) avec la structure suivante dans Azure Cosmos DB :

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Ces documents JSON plats dans Azure Cosmos DB peuvent être représentés sous la forme d’un ensemble de lignes et de colonnes dans Synapse SQL. La fonction `OPENROWSET` vous permet de spécifier un sous-ensemble de propriétés que vous souhaitez lire et les types de colonnes exacts dans la clause `WITH` :

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Le résultat de cette requête peut ressembler à ceci :

| date_rep | cas | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Pour plus d’informations sur les types SQL à utiliser pour la valeur Azure Cosmos DB, consultez les [règles pour les mappages de type SQL](#azure-cosmos-db-to-sql-type-mappings) à la fin de l’article.

## <a name="querying-nested-objects-and-arrays"></a>Interrogation d’objets et de tableaux imbriqués

Azure Cosmos DB vous permet de représenter des modèles de données plus complexes en les composant en tant qu’objets ou tableaux imbriqués. La fonctionnalité de synchronisation automatique de Synapse Link pour Azure Cosmos DB gère la représentation de schéma dans le magasin analytique prêt à l’emploi qui comprend la gestion des types de données imbriqués, ce qui permet d’effectuer des requêtes enrichies à partir d’un pool SQL serverless.

Par exemple, le jeu de données [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) contient des documents JSON suivant la structure suivante :

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Les objets et tableaux imbriqués dans Azure Cosmos DB sont représentés en tant que chaînes JSON dans le résultat de la requête lorsque la fonction `OPENROWSET` les lit. Une option pour lire les valeurs de ces types complexes en tant que colonnes SQL utilise des fonctions JSON SQL :

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Le résultat de cette requête peut ressembler à ceci :

| title | authors | first_autor_name |
| --- | --- | --- |
| Informations supplémentaires An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

En guise d’alternative, vous pouvez également spécifier les chemins d’accès aux valeurs imbriquées dans les objets lors de l’utilisation de la clause `WITH` :

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Apprenez-en davantage sur l’analyse des [types de données complexes dans Synapse Link](../how-to-analyze-complex-schema.md) et les [structures imbriquées dans un pool SQL serverless](query-parquet-nested-types.md).

> [!IMPORTANT]
> Si vous voyez des caractères inattendus dans votre texte, par exemple `MÃƒÂ©lade` au lieu de `Mélade`, cela signifie que votre classement de base de données n’est pas défini sur le classement [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8). 
> [Remplacez le classement de la base de données](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) par un classement UTF8 à l’aide d’une instruction SQL telle que `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`.

## <a name="flattening-nested-arrays"></a>Aplatissement de tableaux imbriqués

Il se peut que les données Azure Cosmos DB comprennent des sous-tableaux imbriqués tels que le tableau d’auteurs du jeu de données [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Dans certains cas, il se peut que vous deviez « joindre » les propriétés de l’élément supérieur (métadonnées) à tous les éléments du tableau (authors). Un pool SQL serverless vous permet d’aplatir des structures imbriquées en appliquant la fonction `OPENJSON` au tableau imbriqué :

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Le résultat de cette requête peut ressembler à ceci :

| title | authors | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Informations supplémentaires An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informations supplémentaires An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Informations supplémentaires An eco-epidemi… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informations supplémentaires An eco-epidemi… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Si vous voyez des caractères inattendus dans votre texte, par exemple `MÃƒÂ©lade` au lieu de `Mélade`, cela signifie que votre classement de base de données n’est pas défini sur le classement [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8). 
> [Remplacez le classement de la base de données](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) par un classement UTF8 à l’aide d’une instruction SQL telle que `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Mappages de type Azure Cosmos DB à SQL

Il est important de commencer par noter que, si le magasin transactionnel Azure Cosmos DB est indépendant du schéma, le magasin analytique est schématisé afin de l’optimiser pour les performances de requête analytique. Avec la fonctionnalité de synchronisation automatique de Synapse Link, Azure Cosmos DB gère la représentation de schéma dans le magasin analytique prêt à l’emploi qui comprend la gestion des types de données imbriqués. Étant donné qu’un pool SQL serverless interroge le magasin analytique, il est important de comprendre comment mapper des types de données d’entrée Azure Cosmos DB à des types de données SQL.

Les comptes Azure Cosmos DB de l’API SQL (Core) prennent en charge les types de propriété JSON nombre, chaîne, booléen, null, objet imbriqué ou tableau. Vous devez choisir des types SQL correspondant à ces types JSON si vous utilisez `WITH` dans la clause `OPENROWSET`. Consultez les types de colonnes SQL ci-dessous à utiliser pour les différents types de propriétés dans Azure Cosmos DB.

| Type de propriété Azure Cosmos DB | Type de colonne SQL |
| --- | --- |
| Booléen | bit |
| Integer | bigint |
| Decimal | float |
| String | varchar (classement de base de données UTF8) |
| Date et heure (chaîne au format ISO) | varchar(30) |
| Date et heure (horodatage Unix) | bigint |
| Null | `any SQL type` 
| Objet ou tableau imbriqués | varchar(max) (classement de base de données UTF8), sérialisé en tant que texte JSON |

## <a name="full-fidelity-schema"></a>Schéma de fidélité optimale

Le schéma de fidélité optimale Azure Cosmos DB enregistre les valeurs et leurs meilleurs types de correspondance pour chaque propriété au sein d’un conteneur.
La fonction `OPENROWSET` sur un conteneur avec un schéma de fidélité optimale fournit le type et la valeur réelle dans chaque cellule. Supposons que la requête suivante lit les éléments d’un conteneur avec un schéma de fidélité optimale :

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

Le résultat de cette requête renvoie des types et des valeurs au format de texte JSON : 

| date_rep | cas | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

Pour chaque valeur, vous pouvez voir le type identifié dans l’élément de conteneur Cosmos DB. La plupart des valeurs pour la propriété `date_rep` sont des valeurs `date`, mais certaines sont stockées de manière incorrecte en tant que chaînes (string) dans Cosmos DB. Le schéma de fidélité optimale retourne les valeurs `date` correctement typées et les valeurs `string` mises en forme de manière incorrecte.
Le nombre de cas est une information stockée en tant que valeur `int32`, mais il existe une valeur entrée sous la forme d’un nombre décimal. Cette valeur a le type `float64`. Si certaines valeurs dépassent le plus grand nombre `int32`, elles sont stockées en tant que type `int64`. Toutes les valeurs `geo_id` de cet exemple sont stockées en tant que types `string`.

> [!IMPORTANT]
> La fonction `OPENROWSET` sans la clause `WITH` expose les valeurs avec les types attendus et les valeurs dont les types sont incorrectement entrés. Cette fonction est conçue pour l’exploration de données, non pour la création de rapports. N’analysez pas les valeurs JSON retournées par cette fonction pour créer des rapports, et utilisez une [clause WITH](#querying-items-with-full-fidelity-schema) explicite pour créer vos rapports.
> Vous devez nettoyer les valeurs dont les types sont incorrects dans un conteneur Azure Cosmos DB pour appliquer une correction dans un magasin analytique de fidélité optimale. 

Pour interroger des comptes Azure Cosmos DB du type d’API Mongo DB, apprenez-en davantage sur la représentation du schéma de fidélité optimale dans le magasin analytique, et sur les noms de propriétés étendues à utiliser [ici](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="querying-items-with-full-fidelity-schema"></a>Interrogation d’éléments avec un schéma de fidélité optimale

Lors de l’interrogation du schéma de fidélité optimale, vous devez spécifier explicitement le type SQL et le type de propriété Cosmos DB attendu dans une clause `WITH`. N’utilisez pas la fonction `OPENROWSET` sans clause `WITH` dans les rapports, car le format du jeu de résultats pourrait être modifié dans la préversion résultant des commentaires.

Dans l’exemple suivant, nous supposons que le type `string` est correct pour la propriété `geo_id`, et que le type `int32` est correct pour la propriété `cases`:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Des valeurs `geo_id` et `cases` d’autres types sont retournées en tant que valeurs de `NULL`. Cette requête fait référence uniquement aux `cases` avec le type spécifié dans l’expression (`cases.int32`).

Si vous avez des valeurs avec d’autres types (`cases.int64`, `cases.float64`) qui ne peuvent pas être nettoyées dans le conteneur Cosmos DB, vous devez les référencer explicitement dans une clause `WITH` et combiner les résultats. La requête suivante agrège les valeurs `int32`, `int64` et `float64` stockées dans la colonne `cases`:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Dans cet exemple, le nombre de cas est stocké sous la forme de valeurs `int32`, `int64` ou `float64` qui doivent toutes être extraites pour calculer le nombre de cas par pays. 

## <a name="known-issues"></a>Problèmes connus

- L’alias **DOIT** être spécifié après la fonction `OPENROWSET` (par exemple, `OPENROWSET (...) AS function_alias`). L’omission de l’alias peut entraîner un problème de connexion, et le point de terminaison Synapse serverless SQL risque d’être temporairement indisponible. Ce problème sera résolu en novembre 2020.
- L’expérience de requête qu’offre un pool SQL serverless pour un [schéma de fidélité optimale Azure Cosmos DB](#full-fidelity-schema) est un comportement temporaire qui sera modifié en fonction des commentaires sur la préversion. Ne vous fiez pas au schéma que la fonction `OPENROWSET` sans clause `WITH` fournit pendant la période de préversion publique, car l’expérience de requête pourrait être alignée avec un schéma bien défini en fonction des commentaires des clients. Contactez l’[équipe produit Synapse Link](mailto:cosmosdbsynapselink@microsoft.com) pour lui faire part de vos commentaires.

Les erreurs possibles et les actions de résolution des problèmes sont répertoriées dans le tableau suivant :

| Erreur | Cause racine |
| --- | --- |
| Erreurs de syntaxe :<br/> - Syntaxe incorrecte près de 'Openrowset'<br/> - `...` n’est pas une option de fournisseur BULK OPENROWSET reconnue.<br/> - Syntaxe incorrecte près de `...` | Causes principales possibles<br/> - N’utilise pas 'CosmosDB' comme premier paramètre,<br/> - Utilisation d’un littéral de chaîne au lieu d’un identificateur dans le troisième paramètre,<br/> - Ne spécifie pas le troisième paramètre (nom de conteneur) |
| Une erreur s’est produite dans la chaîne de connexion CosmosDB | - Le compte, la base de données ou la clé n’est pas spécifié <br/> - Une option dans la chaîne de connexion n’est pas reconnue.<br/> - Un point-virgule `;` est placé à la fin de la chaîne de connexion |
| La résolution du chemin d’accès CosmosDB a échoué avec l’erreur « Nom de compte incorrect » ou « Nom de base de données incorrect » | Le nom de compte, le nom de la base de données ou le conteneur spécifié est introuvable, ou le stockage analytique n’a pas été activé pour la collection spécifiée|
| La résolution du chemin d’accès CosmosDB a échoué avec l’erreur « Valeur de secret incorrecte » ou « Secret null ou vide » | La clé du compte n’est pas valide ou est manquante. |
| La colonne `column name` de type `type name` n’est pas compatible avec le type de données externe `type name` | Le type de colonne spécifié dans la clause `WITH` ne correspond pas au type dans le conteneur Cosmos DB. Essayez de modifier le type de colonne tel que décrit dans la section [Mappages de type Azure Cosmos DB à SQL](#azure-cosmos-db-to-sql-type-mappings) ou utilisez le type `VARCHAR`. |
| La colonne contient `NULL` valeurs dans toutes les cellules. | Possibilité d’erreur de nom de colonne ou d’expression de chemin d’accès dans la clause `WITH`. Le nom de colonne (ou l’expression de chemin d’accès après le type de colonne) dans la clause `WITH` doit correspondre à un nom de propriété dans la collection Cosmos DB. La comparaison **respecte la casse** (par exemple, `productCode` et `ProductCode` sont des propriétés différentes). |

Vous pouvez nous faire part de vos suggestions et signaler des problèmes dans la [page de commentaires Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Utiliser Power BI et un pool Synapse SQL serverless avec Azure Synapse Link](../../cosmos-db/synapse-link-power-bi.md)
- [Comment créer et utiliser des vues dans SQL à la demande](create-use-views.md) 
- [Tutoriel sur la création de vues SQL à la demande sur Azure Cosmos DB et leur connexion à des modèles Power BI via DirectQuery](./tutorial-data-analyst.md)
