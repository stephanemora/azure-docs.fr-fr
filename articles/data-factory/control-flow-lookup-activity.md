---
title: Activité de recherche dans Azure Data Factory
description: Découvrez comment utiliser l’activité Lookup pour rechercher une valeur à partir d’une source externe. Cette sortie peut être référencée davantage par des activités complémentaires.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417997"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Activité de recherche dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’activité Lookup peut récupérer un jeu de données à partir de n’importe quelle source de données compatible Azure Data Factory. Utilisez-la dans le scénario suivant :
- Déterminez de manière dynamique sur quels objets intervenir dans une activité ultérieure, au lieu de coder en dur le nom d’objet. Les fichiers et les tables constituent quelques exemples d’objet.

L’activité Lookup lit et retourne le contenu d’une table ou d’un fichier de configuration. Elle retourne également le résultat de l’exécution d’une requête ou d’une procédure stockée. La sortie de l’activité Lookup peut être utilisée dans une activité de transformation ou de copie ultérieure s’il s’agit d’une valeur singleton. La sortie peut être utilisée dans une activité ForEach, s’il s’agit d’un tableau d’attributs.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Les sources de données suivantes sont prises en charge pour l’activité Lookup. Le plus grand nombre de lignes pouvant être retournées par l’activité Lookup est 5 000, jusqu’à une taille de 2 Mo. Actuellement, la durée la plus longue pour l’activité Lookup avant l’expiration du délai d’attente est d’une heure.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Propriétés type

Name | Description | Type | Requis ?
---- | ----------- | ---- | --------
dataset | Fournit la référence de jeu de données pour la recherche. Pour plus d’informations, voir la section **Propriétés du jeu de données** dans chaque article traitant du connecteur correspondant. | Paire clé/valeur | Oui
source | Contient des propriétés sources spécifiques au jeu de données, identiques à la source de l’activité Copy. Pour plus d’informations, consultez la section **Propriétés de l’activité Copy** dans chaque article traitant du connecteur correspondant. | Paire clé/valeur | Oui
firstRowOnly | Indique s’il faut retourner uniquement la première ligne ou toutes les lignes. | Boolean | Non. Par défaut, il s’agit de `true`.

> [!NOTE]
> 
> * Les colonnes sources avec le type **ByteArray** ne sont pas prises en charge.
> * Il n’y a pas de prise en charge pour **Structure** dans les définitions des jeux de données. Pour les fichiers de format texte, utilisez la ligne d’en-tête pour mentionner le nom de la colonne.
> * Si votre source de recherche est un fichier JSON, le paramètre `jsonPathDefinition` pour la mise en forme de l’objet JSON n’est pas pris en charge. Les objets entiers sont récupérés.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Utiliser le résultat de l’activité Lookup dans une activité ultérieure

Le résultat de la recherche est retourné dans la section `output` du résultat de l’exécution d’activité.

* **Quand `firstRowOnly` a la valeur `true` (par défaut)** , le format de la sortie se présente comme dans le code suivant. Le résultat de la recherche se trouve dans une clé `firstRow` fixe. Pour utiliser le résultat dans une activité suivante, utilisez le modèle `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quand `firstRowOnly` a la valeur `false`** , le format de la sortie se présente comme dans le code suivant. Un champ `count` indique le nombre d’enregistrements qui sont retournés. Les valeurs détaillées sont affichées sous un tableau `value` fixe. Dans ce cas, l’activité Lookup est suivie d’une [activité Foreach](control-flow-for-each-activity.md). Vous passez le tableau `value` au champ `items` de l’activité ForEach en utilisant le modèle `@activity('MyLookupActivity').output.value`. Pour accéder aux éléments du tableau `value`, utilisez la syntaxe suivante : `@{activity('lookupActivity').output.value[zero based index].propertyname}`. par exemple `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Exemple de l’activité Copy
Dans cet exemple, l’activité Copy copie les données d’une table SQL de votre instance Azure SQL Database dans le stockage Blob Azure. Le nom de la table SQL est stocké dans un fichier JSON dans le stockage Blob. L’activité Lookup recherche le nom de la table lors de l’exécution. Le fichier JSON est modifié de manière dynamique avec cette approche. Vous n’avez pas besoin de redéployer les pipelines ou les jeux de données. 

Cet exemple illustre une recherche pour la première ligne uniquement. Pour effectuer une recherche portant sur toutes les lignes et chaîner les résultats avec l’activité ForEach, consultez les exemples dans [Copier plusieurs tables en bloc à l’aide d’Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Ce pipeline contient deux activités : Lookup et Copy. 

- L’activité Lookup est configurée pour utiliser **LookupDataset**, qui fait référence à un emplacement dans le stockage Blob Azure. L’activité Lookup lit le nom de la table SQL à partir d’un fichier JSON dans cet emplacement. 
- L’activité Copy utilise la sortie de l’activité Lookup, qui correspond au nom de la table SQL. La propriété **tableName** dans **SourceDataset** est configurée pour utiliser la sortie de l’activité Lookup. L’activité Copy copie les données de la table SQL dans un emplacement du stockage Blob Azure. L’emplacement est spécifié par la propriété **SinkDataset**. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Jeu de données de recherche
Le jeu de données **lookup** est le fichier **sourcetable.json** dans le dossier de recherche du stockage Azure spécifié par le type **AzureStorageLinkedService**. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>Jeu de données **source** pour l’activité Copy
Le jeu de données **source** utilise la sortie de l’activité Lookup, qui correspond au nom de la table SQL. L’activité Copy copie les données de cette table SQL dans un emplacement du stockage Blob Azure. L’emplacement est indiqué par le jeu de données **sink**. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Jeu de données **sink** pour l’activité Copy
L’activité Copy copie les données de la table SQL dans le fichier **filebylookup.csv** du dossier **csv** du stockage Azure. Le fichier est spécifié par la propriété **AzureStorageLinkedService**. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Service lié Stockage Azure
Ce compte de stockage contient le fichier JSON avec les noms des tables SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Service lié Azure SQL Database
Cette instance Azure SQL Database contient les données à copier dans le stockage Blob. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Ensemble d’objets

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Tableau d’objets

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Limitations et solutions de contournement

Voici quelques limitations de l’activité de recherche et des suggestions de solutions de contournement.

| Limitation | Solution de contournement |
|---|---|
| L’activité de recherche a un maximum de 5 000 lignes et une taille maximale de 2 Mo. | Concevoir un pipeline sur deux niveaux où le pipeline externe itère sur un pipeline interne, qui récupère les données ne dépassant pas le nombre maximal de lignes ou la taille de conception. |
| | |

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Activité ForEach](control-flow-for-each-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Activité Web](control-flow-web-activity.md)
