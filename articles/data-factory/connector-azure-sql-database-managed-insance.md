---
title: Copier des données vers et depuis Azure SQL Database Managed Instance à l'aide d'Azure Data Factory | Microsoft Docs
description: Apprenez à déplacer des données vers et depuis Azure SQL Database Managed Instance à l'aide d'Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 9b54c35a5dcd495e7ed460f1fdbbe96ba3dee4fe
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663551"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copier des données vers et depuis Azure SQL Database Managed Instance à l'aide d'Azure Data Factory

Cet article explique comment utiliser l'activité de copie d'Azure Data Factory pour copier des données vers et depuis Azure SQL Database Managed Instance. Il s'appuie sur l'article [Vue d'ensemble de l'activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d'Azure SQL Database Managed Instance vers n'importe quel magasin de données récepteur pris en charge. Vous pouvez également copier des données de n'importe quel magasin de données source pris en charge vers l'instance gérée. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs par l'activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Database Managed Instance prend en charge :

- La copie des données à l'aide de l'authentification SQL ou Windows
- En tant que source, la récupération de données à l'aide d'une requête SQL ou d'une procédure stockée
- En tant que récepteur, l'ajout de données à une table de destination ou l'appel d'une procédure stockée avec une logique personnalisée pendant la copie

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) n'est actuellement pas pris en charge. 

## <a name="prerequisites"></a>Prérequis

Pour utiliser les données de copie à partir d'une instance d'Azure SQL Database Managed Instance située dans un réseau virtuel, vous devez configurer un runtime d'intégration auto-hébergé capable d'accéder à la base de données. Pour plus d'informations, consultez [Runtime d'intégration auto-hébergé](create-self-hosted-integration-runtime.md).

Si vous configurez votre runtime d'intégration auto-hébergé dans le même réseau virtuel que votre instance gérée, assurez-vous que la machine qui exécute le runtime d'intégration ne se trouve pas dans le même sous-réseau que votre instance gérée. Si vous configurez votre runtime d'intégration auto-hébergé dans un réseau virtuel différent de celui de votre instance gérée, vous pouvez utiliser un appairage de réseau virtuel ou une connexion de réseau virtuel à réseau virtuel. Pour plus d'informations, consultez [Connecter votre application à Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques au connecteur Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure SQL Database Managed Instance sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **SqlServer**. | Oui. |
| connectionString |Cette propriété spécifie les informations connectionString nécessaires pour se connecter à l'instance gérée à l'aide de l'authentification SQL ou de l'authentification Windows. Pour plus d'informations, consultez les exemples suivants. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également définir un mot de passe dans Azure Key Vault et, en cas d'authentification SQL, extraire la configuration `password` de la chaîne de connexion. Pour plus d'informations, reportez-vous à l'exemple JSON décrit sous le tableau et à l'article [Stocker des informations d'identification dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui. |
| userName |Cette propriété spécifie un nom d'utilisateur si vous utilisez l'authentification Windows. Exemple : **domainname\\username**. | Non. |
| password |Cette propriété spécifie le mot de passe du compte d'utilisateur que vous avez spécifié pour le nom d'utilisateur. Sélectionnez **SecureString** pour stocker les informations connectionString en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non. |
| connectVia | Ce [runtime d'intégration](concepts-integration-runtime.md) permet de se connecter au magasin de données. Configurez le runtime d'intégration auto-hébergé dans le même réseau virtuel que votre instance gérée. |Oui. |

>[!TIP]
>Le code d'erreur « UserErrorFailedToConnectToSqlServer » peut s'afficher avec un message tel que « La limite de session XXX de la base de données a été atteinte ». Dans ce cas, ajoutez `Pooling=false` à votre chaîne de connexion, puis réessayez.

**Exemple 1 : Utilisation de l'authentification SQL**

```json
{
    "name": "AzureSqlMILinkedService",
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

**Exemple 2 : Utilisation de l'authentification SQL avec mot de passe dans Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 3 : Utilisation de l'authentification Windows**

```json
{
    "name": "AzureSqlMILinkedService",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l'article consacré aux jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database Managed Instance.

Pour copier des données vers et depuis Azure SQL Database Managed Instance, définissez la propriété type du jeu de données sur **SqlServerTable**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **SqlServerTable**. | Oui. |
| TableName |Cette propriété est le nom de la table ou de la vue dans l'instance de base de données à laquelle le service lié fait référence. | Non pour la source. Oui pour le récepteur. |

**Exemple**

```json
{
    "name": "AzureSqlMIDataset",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Azure SQL Database Managed Instance.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Managed Instance en tant que source

Pour copier des données d’Azure SQL Database Managed Instance, définissez **SqlSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section source de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source de l'activité de copie doit être définie sur **SqlSource**. | Oui. |
| SqlReaderQuery |Cette propriété utilise la requête SQL personnalisée pour lire les données. Par exemple `select * from MyTable`. | Non. |
| sqlReaderStoredProcedureName |Cette propriété est le nom de la procédure stockée qui lit les données dans la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. | Non. |
| storedProcedureParameters |Ces paramètres concernent la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non. |

Notez les points suivants :

- Si **sqlReaderQuery** est spécifié comme **SqlSource**, l'activité de copie exécute cette requête sur la source de l'instance gérée pour obtenir les données. Vous pouvez également spécifier une procédure stockée en spécifiant **sqlReaderStoredProcedureName** et **storedProcedureParameters** si la procédure stockée accepte des paramètres.
- Si vous ne spécifiez pas la propriété **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, les colonnes définies dans la section « structure » du JSON de jeu de données sont utilisées pour créer une requête. La requête `select column1, column2 from mytable` s'exécute sur l'instance gérée. Si la définition du jeu de données ne possède pas de « structure », toutes les colonnes de la table sont sélectionnées.

**Exemple : Utilisation d'une requête SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
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

**Exemple : Utilisation d'une procédure stockée**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Managed Instance en tant que récepteur

Pour copier des données vers Azure SQL Database Managed Instance, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section récepteur de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du récepteur de l'activité de copie doit être définie sur **SqlSink**. | Oui. |
| writeBatchSize |Cette propriété insère des données dans la table SQL lorsque la taille de la mémoire tampon atteint writeBatchSize.<br/>Les valeurs autorisées sont des entiers pour le nombre de lignes. |Non (valeur par défaut : 10 000). |
| writeBatchTimeout |Cette propriété spécifie le délai d'attente avant expiration de l'opération d'insertion de lot.<br/>Les valeurs autorisées sont celles qui expriment une durée. Par exemple, « 00:30:00 » (30 minutes). | Non. |
| preCopyScript |Cette propriété spécifie une requête SQL que l'activité de copie doit exécuter avant l'écriture des données dans l'instance gérée. Elle n'est appelée qu'une seule fois par copie. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. | Non. |
| sqlWriterStoredProcedureName |Ce nom est celui de la procédure stockée qui définit le mode d'application des données sources dans une table cible. Les opérations d'upsert ou de transformations effectuées à l'aide de votre propre logique métier sont des exemples de procédures. <br/><br/>Cette procédure stockée est *appelée par lot*. Pour effectuer une opération qui ne s'exécute qu'une seule fois et n'a rien à voir avec les données sources (par exemple, supprimer ou tronquer), utilisez la propriété `preCopyScript`. | Non. |
| storedProcedureParameters |Ces paramètres sont utilisés pour la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non. |
| sqlWriterTableType |Cette propriété spécifie le nom du type de table à utiliser dans la procédure stockée. L'activité de copie rend les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. | Non. |

> [!TIP]
> Par défaut, lors de la copie de données vers Azure SQL Database Managed Instance, l'activité de copie ajoute les données à la table du récepteur. Pour effectuer une opération d'upsert ou appliquer une logique métier supplémentaire, utilisez la procédure stockée dans SqlSink. Pour plus d'informations, consultez [Appel d'une procédure stockée à partir d'un récepteur SQL](#invoke-a-stored-procedure-from-a-sql-sink).

**Exemple 1 : Ajout de données**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

**Exemple 2 : Appel d'une procédure stockée pendant la copie pour une opération d'upsert**

Pour en savoir plus, consultez [Appel d'une procédure stockée à partir d'un récepteur SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

L'exemple suivant illustre la copie de données d'une table source sans colonne d'identité vers une table de destination dotée d'une colonne d'identité.

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

Notez que vos tables source et cible présentent des schémas différents. La table cible a une colonne d’identité. Dans ce scénario, vous devez spécifier la propriété « structure » dans la définition du jeu de données cible, qui n'inclut pas la colonne d'identité.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Appel d'une procédure stockée à partir d'un récepteur SQL

Lorsque des données sont copiées vers Azure SQL Database Managed Instance, une procédure stockée peut être configurée et appelée avec des paramètres supplémentaires que vous spécifiez.

Vous pouvez utiliser une procédure stockée à la place des mécanismes de copie intégrée. Elle est généralement utilisée lorsqu'une opération d'upsert (mise à jour + insertion) ou un traitement supplémentaire doit être effectué avant l'insertion finale des données sources dans la table de destination. Le traitement supplémentaire peut inclure des tâches telles que la fusion de colonnes, la recherche de valeurs supplémentaires et l'insertion dans plusieurs tables.

L'exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération d'upsert dans une table de l'instance gérée. L'exemple part du principe que les données d'entrée et la table réceptrice « Marketing » possèdent trois colonnes : ProfileID, State et Category. On effectue l'opération d'upsert à partir de la colonne « ProfileID » et on l'applique uniquement à une catégorie spécifique.

**Jeu de données de sortie**

```json
{
    "name": "AzureSqlMIDataset",
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

Définissez la section SqlSink d'une activité de copie comme suit :

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

Dans votre base de données, définissez la procédure stockée portant le même nom que SqlWriterStoredProcedureName. Celle-ci gère les données d'entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Le nom du paramètre du type de table de la procédure stockée doit être identique au « tableName » défini dans le jeu de données.

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

Dans votre base de données, définissez le type de table portant le même nom que SqlWriterTableType. Le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La fonction de procédure stockée tire parti des [paramètres table](https://msdn.microsoft.com/library/bb675163.aspx).

>[!NOTE]
>Si vous écrivez avec le type de données **Money/Smallmoney** en appelant une procédure stockée, les valeurs peuvent être arrondies. Pour y remédier, dans les paramètres table, définissez le type de données correspondant sur **Decimal** au lieu de **Money/Smallmoney**. 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mappage de type de données pour Azure SQL Database Managed Instance

Lors de la copie de données vers et depuis Azure SQL Database Managed Instance, les mappages suivants sont utilisés entre les types de données Azure SQL Database Managed Instance et les types de données intermédiaires Azure Data Factory. Pour savoir comment l'activité de copie mappe le schéma et le type de données entre la source et le récepteur, consultez [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Database Managed Instance | Type de données intermédiaires Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binaire |Byte[] |
| bit |Booléen |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Attribut FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérique |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| texte |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

>[!NOTE]
> Pour les types de données mappés avec le type intermédiaire Decimal, Azure Data Factory prend actuellement en charge une précision maximale de 28. Si vous disposez de données qui nécessitent une précision supérieure à 28, pensez à les convertir en chaîne dans une requête SQL.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l'activité de copie d'Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
