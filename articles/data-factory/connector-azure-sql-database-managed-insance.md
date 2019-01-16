---
title: Copier des données depuis/vers Azure SQL Database Managed Instance à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment déplacer des données depuis et vers Azure SQL Database Managed Instance à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: jingwang
ms.openlocfilehash: df8d337e7950400a86dcab14de4484f4811f43e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025077"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Copier des données depuis et vers Azure SQL Database Managed Instance à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Azure SQL Database Managed Instance. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données depuis Azure SQL Database Managed Instance vers toute banque de données réceptrice prise en charge, ou copier des données depuis toute banque de données source prise en charge vers Managed Instance. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Database Managed Instance prend en charge :

- La copie des données à l’aide de l’authentification **SQL** ou **Windows**
- En tant que source, la récupération de données à l’aide d’une procédure stockée ou d’une requête SQL
- En tant que récepteur, l’ajout de données à une table de destination ou l’appel d’une procédure stockée avec une logique personnalisée pendant la copie.

## <a name="prerequisites"></a>Prérequis

Pour utiliser les données de copie à partir d’Azure SQL Database Managed Instance dans le réseau virtuel, vous devez configurer un runtime d’intégration autohébergé capable d’accéder à la base de données dans le même réseau virtuel. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques du connecteur Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure SQL Database Managed Instance sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur : **SqlServer** | Oui |
| connectionString |Spécifiez les informations connectionString nécessaires pour établir une connexion à Managed Instance à l’aide de l’authentification SQL ou de l’authentification Windows. Consultez l’exemple suivant. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| userName |Spécifiez le nom d’utilisateur si vous utilisez l’authentification Windows. Exemple : **domainname\\username**. |Non  |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez défini pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Non  |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Provisionner le runtime d’intégration autohébergé dans le même réseau virtuel que Managed Instance. |Oui |

>[!TIP]
>Si vous rencontrez une erreur avec le code d’erreur « UserErrorFailedToConnectToSqlServer » et un message tel que « La limite de session pour la base de données est XXX et a été atteinte. », ajoutez `Pooling=false` à votre chaîne de connexion, puis réessayez.

**Exemple 1 : Utilisation de l’authentification SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Utilisation de l’authentification Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
             }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database Managed Instance.

Pour copier des données depuis/vers Azure SQL Database Managed Instance, définissez la propriété type du jeu de données sur **SqlServerTable**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **SqlServerTable** | Oui |
| TableName |Nom de la table ou de la vue dans l’instance de base de données à laquelle le service lié fait référence. | Non pour Source, Oui pour Récepteur |

**Exemple**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Azure SQL Database Managed Instance.

### <a name="azure-sql-database-managed-instance-as-source"></a>Azure SQL Database Managed Instance en tant que source

Pour copier des données d’Azure SQL Database Managed Instance, définissez **SqlSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur : **SqlSource** | Oui |
| SqlReaderQuery |Utiliser la requête SQL personnalisée pour lire les données. Exemple : `select * from MyTable`. |Non  |
| sqlReaderStoredProcedureName |Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non  |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non  |

**Points à noter**

- Si **sqlReaderQuery** est spécifié comme SqlSource, l’activité de copie exécute cette requête sur la source Managed Instance pour obtenir les données. Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).
- Si vous ne spécifiez pas la propriété « sqlReaderQuery » ou « sqlReaderStoredProcedureName », les colonnes définies dans la section « structure » du jeu de données JSON sont utilisées pour construire une requête (`select column1, column2 from mytable`) à exécuter sur Managed Instance. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.

**Exemple : Utilisation d’une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemple : Utilisation d’une procédure stockée**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Définition de la procédure stockée**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-managed-instance-as-sink"></a>Azure SQL Database Managed Instance en tant que récepteur

Pour copier des données vers Azure SQL Database Managed Instance, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du récepteur d’activité de copie doit être définie sur : **SqlSink** | Oui |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (valeur par défaut : 10000) |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : “00:30:00” (30 minutes). |Non  |
| preCopyScript |Spécifiez une requête SQL que l’activité de copie doit exécuter avant l’écriture des données dans Managed Instance. Elle ne sera appelée qu’une seule fois par copie. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non  |
| sqlWriterStoredProcedureName |Nom de la procédure stockée qui définit comment appliquer les données sources dans la table cible, par exemple pour effectuer des upserts ou des transformations à l’aide de votre propre logique métier. <br/><br/>Notez que cette procédure stockée sera **appelée par lot**. Si vous souhaitez effectuer une opération qui ne s’exécute qu’une seule fois et n’a rien à faire avec les données sources, par exemple supprimer/tronquer, utilisez la propriété `preCopyScript`. |Non  |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Valeurs autorisées : paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non  |
| sqlWriterTableType |Spécifiez le nom du type de table à utiliser dans la procédure stockée. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non  |

> [!TIP]
> Lors de la copie de données vers Azure SQL Database Managed Instance, l’activité de copie ajoute des données à la table du récepteur par défaut. Pour effectuer un UPSERT ou une logique métier supplémentaire, utilisez la procédure stockée dans SqlSink. Pour en savoir plus, consultez [Appel d’une procédure stockée pour un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

**Exemple 1 : Ajout de données**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemple 2 : Appel d’une procédure stockée pendant la copie pour upsert**

Pour en savoir plus, consultez [Appel d’une procédure stockée pour un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Colonnes d’identité dans la base de données cible

Cette section fournit un exemple qui copie des données d’une table source sans colonne d’identité vers une table de destination avec une colonne d’identité.

**Table source**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Table de destination**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Notez que la table cible possède une colonne d’identité.

**Définition du jeu de données JSON source**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Définition de jeu de données JSON de destination**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Notez que vos tables source et cible ont des schémas différents (la cible possède une colonne supplémentaire avec identité). Dans ce scénario, vous devez spécifier la propriété **structure** dans la définition du jeu de données cible, qui n’inclut pas la colonne d’identité.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Appel d’une procédure stockée pour un récepteur SQL

Quand vous copiez des données vers Azure SQL Database Managed Instance, une procédure stockée spécifiée par l’utilisateur peut être configurée et appelée avec des paramètres supplémentaires.

Une procédure stockée peut être utilisée à la place des mécanismes de copie intégrée. C’est généralement le cas quand une opération upsert (insertion + mise à jour) ou un traitement supplémentaire (fusion de colonnes, recherche de valeurs supplémentaires, insertion dans plusieurs tables, etc.) doivent être effectués avant l’insertion finale des données sources dans la table de destination.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert simple dans une table de Managed Instance. En supposant que les données d’entrée et la table réceptrice « Marketing » ont trois colonnes : ProfileID, State et Category. On effectue une opération upsert basée sur la colonne « ProfileID » et on l’applique uniquement à une catégorie spécifique.

**Jeu de données de sortie**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Définissez la section SqlSink dans l’activité de copie comme suit.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Dans votre base de données, définissez la procédure stockée portant le même nom que SqlWriterStoredProcedureName. Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Le nom de paramètre du type de table dans la procédure stockée doit être le même que le « tableName » défini dans le jeu de données.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Dans votre base de données, définissez le type de table portant le même nom que SqlWriterTableType. Notez que le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La fonction de procédure stockée tire parti des [paramètres Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Si vous écrivez avec le type de données Money/Smallmoney en appelant la procédure stockée, les valeurs peuvent être arrondies. Pour éviter cela, définissez le type de données correspondant dans TVP sur le format Décimal au lieu de Money/Smallmoney. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mappage de type de données pour Azure SQL Database Managed Instance

Lors de la copie de données à partir d’Azure SQL Database Managed Instance, les mappages suivants sont utilisés entre les types de données Managed Instance et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Database Managed Instance | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binaire |Byte[] |
| bit |Booléen |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DatetimeOffset |
| Décimal |Décimal |
| Attribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Décimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérique |Décimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Décimal |
| sql_variant |Objet * |
| texte |String, Char[] |
| time |intervalle de temps |
|  timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
