---
title: Mappage de schéma dans l’activité de copie
description: Découvrez comment l’activité de copie dans Azure Data Factory mappe les schémas et les types de données des données de la source aux données du récepteur lors de la copie.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423623"
---
# <a name="schema-mapping-in-copy-activity"></a>Mappage de schéma dans l’activité de copie

Cet article décrit la manière dont l’activité de copie d’Azure Data Factory effectue un mappage de schéma et de type de données, des données sources au données du récepteur lors de la copie.

## <a name="schema-mapping"></a>Mappage de schéma

Le mappage de colonnes s’applique lors de la copie des données source vers les données du récepteur. Par défaut, l’activité de copie **mappe les données sources au récepteur par noms de colonnes**. Vous pouvez spécifier un [mappage explicite](#explicit-mapping) pour personnaliser le mappage de colonne selon vos besoins. Plus spécifiquement, l’activité de copie comprend les trois phases suivantes :

1. Lire les données de la source et déterminer le schéma de celle-ci
2. Utilisez le mappage des colonnes par défaut pour mapper les colonnes par nom, ou appliquez un mappage de colonnes explicite s’il est spécifié.
3. Écrire les données sur le récepteur

### <a name="explicit-mapping"></a>Mappage explicite

Vous pouvez spécifier les colonnes à mapper dans une activité de copie -> propriété `translator` -> `mappings`. L’exemple suivant définit une activité de copie dans un pipeline pour copier des données à partir de texte délimité dans Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

Les propriétés suivantes sont prises en charge sous `translator` -> `mappings` -> objet avec `source` et `sink`:

| Propriété | Description                                                  | Obligatoire |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nom de la colonne source ou récepteur.                           | Oui      |
| ordinal  | Index de colonne. Commence par 1. <br>À appliquer et requis lors de l’utilisation de texte sans ligne d’en-tête délimité. | Non       |
| path     | Expression de chemin JSON pour l’extraction ou le mappage de chaque champ. À appliquer aux données hiérarchiques, par exemple MongoDB/REST.<br>Pour les champs situés sous l’objet racine, le chemin JSON commence par $ racine ; pour ceux qui se trouvent dans le tableau sélectionné par la propriété `collectionReference`, le chemin JSON commence par l’élément de tableau. | Non       |
| type     | Type de données intermédiaires Data Factory de la colonne source ou récepteur. | Non       |
| culture  | Culture de la colonne source ou récepteur. <br>À appliquer lorsque le type est `Datetime` ou `Datetimeoffset`. Par défaut, il s’agit de `en-us`. | Non       |
| format   | Chaîne de format à utiliser lorsque le type est `Datetime` ou `Datetimeoffset`. Reportez-vous à [Chaînes de format Date et Heure personnalisées](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sur la mise en forme des date/heure. | Non       |

Les propriétés suivantes sont prises en charge sous `translator` -> `mappings` en plus de l’objet avec `source` et `sink` :

| Propriété            | Description                                                  | Obligatoire |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Pris en charge uniquement lorsque des données hiérarchiques, par exemple MongoDB/REST, sont la source.<br>Si vous souhaitez effectuer une itération et extraire des données à partir des objets situés **à l’intérieur d’un champ de tableau** présentant le même modèle et effectuer une conversion par ligne et par objet, spécifiez le chemin JSON de ce tableau afin d’effectuer une application croisée. | Non       |

### <a name="alternative-column-mapping"></a>Mappage de colonne alternatif

Vous pouvez spécifier l’activité de copie -> `translator` -> `columnMappings` pour le mappage entre les données mises en forme tabulaire. Dans ce cas, la section « structure » est requise pour les jeux de données d’entrée et de sortie. Le mappage de colonnes prend en charge le **mappage de la totalité ou d’un sous-ensemble des colonnes de la « structure » du jeu de données de la source à toutes les colonnes de la « structure » du jeu de données du récepteur**. Voici une liste de conditions d’erreur qui entraînent la levée d’une exception :

* Le résultat de la requête de banque de données source n’a pas de nom de colonne spécifié dans la section « structure » du jeu de données d’entrée.
* La banque de données du récepteur (si un schéma est prédéfini) n’a pas de nom de colonne spécifié dans la section « structure » du jeu de données de sortie.
* La « structure » du jeu de données du récepteur contient un nombre de colonnes inférieur ou supérieur à celui spécifié par le mappage.
* Mappage en double.

Dans l’exemple suivant, le jeu de données d’entrée possède une structure et pointe vers une table dans une base de données Oracle locale.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Dans cet exemple, le jeu de données de sortie possède une structure et pointe vers une table dans Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Le JSON suivant définit une activité de copie dans un pipeline. Les colonnes de la source sont mappées aux colonnes dans le récepteur en utilisant la propriété **translator** -> **columnMappings**.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Si vous utilisiez la syntaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` pour spécifier le mappage de colonnes, il est toujours pris en charge tel quel.

### <a name="alternative-schema-mapping"></a>Mappage de schéma alternatif

Vous pouvez spécifier l’activité de copie -> `translator` -> `schemaMapping` pour mapper entre des données au format hiérarchique et tabulaire, par exemple la copie à partir de MongoDB/REST vers un fichier texte et la copie à partir d’Oracle vers l'API Azure Cosmos DB pour MongoDB. Les propriétés suivantes sont prises en charge dans la section `translator` de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du traducteur d’activité de copie doit être définie sur : **TabularTranslator** | Oui |
| schemaMapping | Une collection de paires clé-valeur, qui représente la relation de mappage **du côté source au côté récepteur**.<br/>- **Clé :** représente la source. Pour une **source tabulaire**, spécifiez le nom de colonne tel que défini dans la structure du jeu de données ; pour une **source hiérarchique**, spécifiez l’expression de chemin JSON pour chaque champ à extraire et mapper.<br>- **Valeur :** représente le récepteur. Pour un **récepteur tabulaire**, spécifiez le nom de colonne tel que défini dans la structure du jeu de données ; pour un **récepteur hiérarchique**, spécifiez l’expression de chemin JSON pour chaque champ à extraire et mapper. <br>Dans le cas de données hiérarchiques, pour les champs situés sous l’objet racine, le chemin JSON commence par $ racine ; pour ceux qui se trouvent dans le tableau sélectionné par la propriété `collectionReference`, le chemin JSON commence par l’élément de tableau.  | Oui |
| collectionReference | Si vous souhaitez effectuer une itération et extraire des données à partir des objets situés **à l’intérieur d’un champ de tableau** présentant le même modèle et effectuer une conversion par ligne et par objet, spécifiez le chemin JSON de ce tableau afin d’effectuer une application croisée. Cette propriété est prise en charge uniquement quand des données hiérarchiques sont la source. | Non |

**Exemple : copier à partir de MongoDB vers Oracle :**

Par exemple, si vous avez un document MongoDB avec le contenu suivant :

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

Vous souhaitez copier ce fichier dans une table SQL Azure au format suivant, en mettant à plat les données se trouvant dans le tableau *(order_pd and order_price)* et en effectuant une jointure croisée avec les informations racines communes *(numéro, date et ville)*  :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configurez la règle de mappage de schéma comme l’exemple JSON d’activité de copie suivant :

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mappage de types de données

L’activité de copie effectue un mappage des types de la source aux types du récepteur selon l’approche en 2 étapes suivante :

1. Conversion des types de données natifs de la source en types de données intermédiaires d’Azure Data Factory
2. Conversion des types de données intermédiaires d’Azure Data Factory en types de données natifs du récepteur

Vous pouvez trouver le mappage du type natif au type intermédiaire dans la section « Mappage de type de données » de chaque rubrique du connecteur.

### <a name="supported-data-types"></a>Types de données pris en charge

Data Factory prend en charge les types de données intermédiaires suivants : Vous pouvez spécifier les valeurs ci-dessous lors de la configuration des informations de type dans la configuration de [structure du jeu de données](concepts-datasets-linked-services.md#dataset-structure-or-schema) :

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Unique
* String
* Timespan

## <a name="next-steps"></a>Étapes suivantes
Voir les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
