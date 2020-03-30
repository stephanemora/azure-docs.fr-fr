---
title: Groupes de données
description: En savoir plus sur les jeux de données dans Data Factory. Les jeux de données représentent des données d’entrée/sortie.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225557"
---
# <a name="datasets-in-azure-data-factory"></a>Jeux de données dans Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Version actuelle](concepts-datasets-linked-services.md)

Cet article décrit les jeux de données, comment ils sont définis au format JSON et comment ils sont utilisés dans les pipelines d’Azure Data Factory.

Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md) pour obtenir une vue d’ensemble.

## <a name="overview"></a>Vue d’ensemble
Une fabrique de données peut avoir un ou plusieurs pipelines. Un **pipeline** constitue un regroupement logique d’**activités** qui exécutent ensemble une tâche. Les activités d’un pipeline définissent les actions à effectuer sur les données. À présent, un **jeu de données** est une vue de données nommée qui pointe ou fait référence simplement aux données que vous souhaitez utiliser dans vos **activités** en tant qu’entrées et sorties. Les jeux de données identifient les données dans différents magasins de données, par exemple des tables, des fichiers, des dossiers et des documents. Par exemple, un jeu de données d’objets blob Azure spécifie le conteneur et le dossier du Stockage Blob à partir duquel l’activité doit lire les données.

Avant de créer un jeu de données, vous devez générer un [**service lié**](concepts-linked-services.md) afin d’établir un lien entre votre banque de données et la fabrique de données. Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Considérez les choses de la façon suivante : le jeu de données représente la structure des données à l’intérieur des magasins de données liés, et le service lié définit la connexion à la source de données. Par exemple, un service lié de stockage Azure relie un compte de stockage à la fabrique de données. Un jeu de données d'objets blob représente le conteneur d’objets blob et le dossier à l’intérieur de ce compte de stockage Azure contenant les objets blob d’entrée à traiter.

Voici un exemple de scénario. Pour copier des données d’un stockage Blob dans une base de données SQL, vous créez deux services liés : Stockage Azure et Azure SQL Database. Créez ensuite deux jeux de données : le jeu de données d’objets blob Azure (qui fait référence au service lié Stockage Azure) et le jeu de données de table Azure SQL (qui fait référence au service lié Azure SQL Database). Les services liés Stockage Azure et Azure SQL Database contiennent des chaînes de connexion utilisées par la fabrique de données pendant l’exécution pour se connecter à votre instance Stockage Azure et Azure SQL Database, respectivement. Le jeu de données d’objets blob Azure spécifie le conteneur d’objets blob et le dossier d’objets blob qui contient les objets blob d’entrée dans votre stockage Blob. Le jeu de données de table SQL Azure spécifie la table SQL dans votre base de données SQL dans laquelle les données doivent être copiées.

Le diagramme suivant montre la relation entre le pipeline, l’activité, le jeu de données et le service lié dans la fabrique de données :

![Relation entre le pipeline, l’activité, le jeu de données et les services liés](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Jeu de données JSON
Un jeu de données dans Data Factory est défini au format JSON suivant :

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
La table suivante décrit les propriétés dans le JSON ci-dessus :

Propriété | Description | Obligatoire |
-------- | ----------- | -------- |
name | Nom du jeu de données Voir [Azure Data Factory - Règles d’affectation des noms](naming-rules.md). |  Oui |
type | Type du jeu de données. Spécifiez un des types pris en charge par la fabrique de données (par exemple : AzureBlob, AzureSqlTable). <br/><br/>Pour plus de détails, voir [Types de jeux de données](#dataset-type). | Oui |
structure | Schéma du jeu de données. Pour plus de détails, consultez [Schéma de jeux de données](#dataset-structure-or-schema). | Non |
typeProperties | Les propriétés de type sont différentes pour chaque type (par exemple : Blob Azure, table SQL Azure). Pour plus d’informations sur les types pris en charge et leurs propriétés, consultez [Type du jeu de données](#dataset-type). | Oui |

### <a name="data-flow-compatible-dataset"></a>Jeu de données compatible avec Data Flow



Consultez [Types de jeux de données pris en charge](#dataset-type) pour obtenir la liste des types de jeux de données qui sont compatibles avec [Data Flow](concepts-data-flow-overview.md). Les jeux de données compatibles avec Data Flow requièrent une définition précise du jeu de données pour les transformations. Ainsi, la définition JSON est légèrement différente. Les jeux de données compatibles avec Data Flow ont une propriété _structure_ à la place d’une propriété _schéma_.

Dans Data Flow, les jeux de données sont utilisés dans les transformations de source et de récepteur. Les jeux de données définissent les schémas de données de base. Si vos données n’ont pas de schéma, vous pouvez utiliser la dérive du schéma pour votre source et votre récepteur. Dans le jeu de données, le schéma représente le type de données physiques et leurs formes.

En définissant le schéma à partir du jeu de données, vous obtiendrez les types de données associés, les formats de données, l’emplacement du fichier et les informations de connexion du service lié associé. Les métadonnées des jeux de données s’affichent dans votre transformation source en tant que *projection* source. La projection dans la transformation source représente les données Data Flow avec les types et les noms définis.

Lorsque vous importez le schéma d’un jeu de données Data Flow, sélectionnez le bouton **Importer un schéma**, puis importez-le à partir de la source ou depuis un fichier local. Dans la plupart des cas, vous allez importer le schéma directement à partir de la source. Cependant, si vous disposez déjà d’un fichier de schéma local (un fichier Parquet ou CSV avec en-têtes), vous pouvez demander à Data Factory de baser le schéma sur ce fichier.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

La table suivante décrit les propriétés dans le JSON ci-dessus :

Propriété | Description | Obligatoire |
-------- | ----------- | -------- |
name | Nom du jeu de données Voir [Azure Data Factory - Règles d’affectation des noms](naming-rules.md). |  Oui |
type | Type du jeu de données. Spécifiez un des types pris en charge par la fabrique de données (par exemple : AzureBlob, AzureSqlTable). <br/><br/>Pour plus de détails, voir [Types de jeux de données](#dataset-type). | Oui |
schéma | Schéma du jeu de données. Pour plus d’informations, consultez [Jeux de données compatibles avec Data Flow](#dataset-type). | Non |
typeProperties | Les propriétés de type sont différentes pour chaque type (par exemple : Blob Azure, table SQL Azure). Pour plus d’informations sur les types pris en charge et leurs propriétés, consultez [Type du jeu de données](#dataset-type). | Oui |


## <a name="dataset-example"></a>Exemple de jeu de données
Dans l’exemple suivant, le jeu de données représente une table nommée MyTable dans une base de données SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Notez les points suivants :

- Le type est défini sur AzureSQLTable.
- La propriété de type tableName (propre au type AzureSqlTable) est définie sur MyTable.
- linkedServiceName fait référence à un service lié de type AzureSqlDatabase, qui est défini dans l’extrait de code JSON suivant.

## <a name="dataset-type"></a>Type de jeu de données
Il existe différents types de jeux de données, selon la banque de données que vous utilisez. Vous trouverez la liste des données stockées prises en charge par Data Factory dans l’article [Vue d’ensemble des connecteurs](connector-overview.md). Cliquez sur un magasin de données pour savoir comment créer un service lié et un jeu de données pour ce magasin de données.

Dans l’exemple de la section précédente, le type du jeu de données est défini sur **AzureSqlTable**. De même, pour un jeu de données d’objets blob Azure, le type du jeu de données est défini sur **AzureBlob**, comme indiqué dans le JSON suivant :

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Structure ou schéma d’un jeu de données
La section **structure** ou les jeux de données de section **schéma** (compatible avec Data Flow) sont facultatifs. Elle définit le schéma du jeu de données en contenant une collection de noms et de types de données de colonnes. Vous utilisez la section structure pour fournir des informations sur le type utilisé pour convertir les types et colonnes de mappage depuis la source vers la destination.

Chaque colonne de la structure contient les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
name | Nom de la colonne. | Oui
type | Type de données de la colonne. Data Factory prend en charge les types de données temporaires suivants, comme les valeurs autorisées : **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset et Timespan** | Non
culture | Culture .NET à utiliser lorsque le type est un type .NET : `Datetime` ou `Datetimeoffset`. Par défaut, il s’agit de `en-us`. | Non
format | Chaîne de format à utiliser lorsque le type est un type .NET : `Datetime` ou `Datetimeoffset`. Reportez-vous à [Chaînes de format Date et Heure personnalisées](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sur la mise en forme des date/heure. | Non

### <a name="example"></a>Exemple
Dans l’exemple suivant, supposons que les données Blob sources sont au format CSV et contiennent trois colonnes : userid, nom et lastlogindate. Elles sont de type Int64, String et Datetime avec un format de date et d'heure personnalisé utilisant des noms abrégés en français pour le jour de la semaine.

Définissez la structure du jeu de données blob comme suit, ainsi que des définitions de types pour les colonnes :

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Assistance

Les recommandations suivantes vous permettent de comprendre quand inclure les informations de structure et qu’inclure dans la section **structure**. Apprenez-en davantage sur la manière dont la fabrique de données mappe les données sources au récepteur et quand spécifier les informations de structure à partir de [Mappage de schémas et de types](copy-activity-schema-and-type-mapping.md).

- **Pour des sources de données de schéma fortes**, spécifiez la section structure uniquement si vous souhaitez mapper les colonnes sources aux colonnes du récepteur et que leurs noms ne sont pas identiques. Ce type de source de données structurées stocke les informations de schéma et de type de données, ainsi que les données elles-mêmes. SQL Server, Oracle et Azure SQL Database sont des exemples de sources de données structurées.<br/><br/>Comme les informations de type sont déjà disponibles pour les sources de données structurées, vous ne devez pas les inclure lorsque vous incluez la section structure.
- **Pour aucune source ou des sources de données de schéma faibles, par ex. un fichier texte dans le stockage blob**, incluez la structure lorsque le jeu de données est une entrée d’activité de copie et que les types de données du jeu de données source doivent être convertis en types natifs pour le récepteur. Et incluez la structure lorsque vous souhaitez mapper les colonnes sources aux colonnes de récepteur.

## <a name="create-datasets"></a>Créez les jeux de données
Vous pouvez créer des jeux de données en utilisant l’un des outils ou kits de développement logiciel suivants : [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), modèle Azure Resource Manager et portail Azure

## <a name="current-version-vs-version-1-datasets"></a>Jeux de données de la version actuelle et de la version 1

Voici quelques différences entre les jeux de données Data Factory et Data Factory version 1 :

- La propriété externe n’est pas prise en charge dans la version actuelle. Elle est remplacée par un [déclencheur](concepts-pipeline-execution-triggers.md).
- Les propriétés de stratégie et de disponibilité ne sont pas prises en charge dans la version actuelle. L’heure de début d’un pipeline dépend des [déclencheurs](concepts-pipeline-execution-triggers.md).
- Les jeux de données limités (définis dans un pipeline) ne sont pas pris en charge dans la version actuelle.

## <a name="next-steps"></a>Étapes suivantes
Consultez les didacticiels suivants pour obtenir des instructions pas à pas sur la création de pipelines et de jeux de données à l’aide de l’un de ces outils ou Kits de développement logiciel (SDK).

- [Démarrage rapide : Créer une fabrique de données avec .NET](quickstart-create-data-factory-dot-net.md)
- [Démarrage rapide : créer une fabrique de données à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)
- [Démarrage rapide : créer une fabrique de données à l’aide d’une API REST](quickstart-create-data-factory-rest-api.md)
- [Démarrage rapide : créer une fabrique de données à l’aide du Portail Azure](quickstart-create-data-factory-portal.md)
