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
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338619"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copier des données vers et depuis Azure SQL Database Managed Instance à l'aide d'Azure Data Factory

Cet article explique comment utiliser l'activité de copie d'Azure Data Factory pour copier des données vers et depuis Azure SQL Database Managed Instance. Il s'appuie sur l'article [Vue d'ensemble de l'activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d'Azure SQL Database Managed Instance vers n'importe quel magasin de données récepteur pris en charge. Vous pouvez également copier des données de n'importe quel magasin de données source pris en charge vers l'instance gérée. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs par l'activité de copie, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Database Managed Instance prend en charge :

- Copie de données en utilisant une authentification SQL.
- En tant que source, la récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, l'ajout de données à une table de destination ou l'appel d'une procédure stockée avec une logique personnalisée pendant la copie

>[!NOTE]
>Azure SQL Database Managed Instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) n’est actuellement pas pris en charge par ce connecteur. Pour contourner ce problème, vous pouvez utiliser un [connecteur ODBC générique](connector-odbc.md) et un pilote SQL Server ODBC via un runtime d’intégration auto-hébergé. Suivez [ces instructions](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) relatives au téléchargement du pilote ODBC et à la configuration des chaînes de connexion.

>[!NOTE]
>Les authentifications d'identité principale et d'identité gérée du service ne sont actuellement pas prises en charge par ce connecteur. Pour contourner ce problème, choisissez un connecteur Azure SQL Database et spécifiez manuellement le serveur de votre instance gérée.

## <a name="prerequisites"></a>Prérequis

Pour accéder au [point de terminaison public](../sql-database/sql-database-managed-instance-public-endpoint-securely.md) Azure SQL Database Managed Instance, vous pouvez utiliser un runtime d’intégration Azure géré Azure Data Factory. Assurez-vous que vous activez le terminal public et que vous autorisez également le trafic du terminal public sur le groupe de sécurité réseau afin que Azure Data Factory puisse se connecter à votre base de données. Pour plus d’informations, [consultez cet article](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Pour accéder au point de terminaison privé Azure SQL Database Managed Instance, configurez un [runtime d'intégration auto-hébergé](create-self-hosted-integration-runtime.md) capable d'accéder à la base de données. Si vous configurez le runtime d'intégration auto-hébergé dans le même réseau virtuel que votre instance gérée, assurez-vous que la machine qui exécute le runtime d'intégration ne se trouve pas dans le même sous-réseau que votre instance gérée. Si vous configurez votre runtime d'intégration auto-hébergé dans un réseau virtuel différent de celui de votre instance gérée, vous pouvez utiliser un appairage de réseau virtuel ou une connexion de réseau virtuel à réseau virtuel. Pour plus d'informations, consultez [Connecter votre application à Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Azure Data Factory spécifiques au connecteur Azure SQL Database Managed Instance.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure SQL Database Managed Instance sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **SqlServer**. | OUI |
| connectionString |Cette propriété spécifie les informations **connectionString** nécessaires pour se connecter à l'instance gérée à l'aide de l'authentification SQL. Pour plus d'informations, consultez les exemples suivants. <br/>Marquez ce champ comme **SecureString** pour le stocker de manière sécurisée dans Azure Data Factory. Vous pouvez également placer un mot de passe dans Azure Key Vault. En cas d’authentification SQL, extrayez la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez l’exemple JSON figurant après le tableau et l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Ce [runtime d'intégration](concepts-integration-runtime.md) permet de se connecter au magasin de données. Vous pouvez utiliser un runtime d'intégration auto-hébergé ou un runtime d'intégration Azure si votre instance gérée possède un terminal public et autorise Azure Data Factory à y accéder. À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |OUI |

**Exemple 1 : Utilisation de l'authentification SQL**

Le port par défaut est 1433. Si vous utilisez Azure SQL Database Managed Instance avec un terminal public, spécifiez explicitement le port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Utiliser l’authentification SQL avec un mot de passe dans Azure Key Vault**

Le port par défaut est 1433. Si vous utilisez Azure SQL Database Managed Instance avec un terminal public, spécifiez explicitement le port 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l'article consacré aux jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database Managed Instance.

Les propriétés suivantes sont prises en charge pour copier des données vers et depuis Azure SQL Database Managed Instance :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **SqlServerTable**. | OUI |
| tableName |Cette propriété est le nom de la table ou de la vue dans l'instance de base de données à laquelle le service lié fait référence. | Non pour Source, Oui pour Récepteur |

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
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
| Type | La propriété type de la source de l'activité de copie doit être définie sur **SqlSource**. | OUI |
| sqlReaderQuery |Cette propriété utilise la requête SQL personnalisée pour lire les données. Par exemple `select * from MyTable`. |Non |
| sqlReaderStoredProcedureName |Cette propriété est le nom de la procédure stockée qui lit les données dans la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. |Non |
| storedProcedureParameters |Ces paramètres concernent la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non |

**Notez les points suivants :**

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

> [!TIP]
> Pour en savoir plus sur les meilleures pratiques, les configurations et les comportements d’écriture pris en charge, consultez l’article [Meilleures pratiques de chargement de données dans Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Pour copier des données vers Azure SQL Database Managed Instance, définissez **SqlSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section récepteur de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du récepteur de l'activité de copie doit être définie sur **SqlSink**. | OUI |
| writeBatchSize |Nombre de lignes à insérer dans la table SQL *par lot*.<br/>Les valeurs autorisées sont des entiers pour le nombre de lignes. Par défaut, Azure Data Factory détermine de façon dynamique la taille de lot appropriée en fonction de la taille de ligne.  |Non |
| writeBatchTimeout |Cette propriété spécifie le délai d'attente avant expiration de l'opération d'insertion de lot.<br/>Les valeurs autorisées sont celles qui expriment un intervalle de temps. Par exemple, « 00:30:00 » (30 minutes). |Non |
| preCopyScript |Cette propriété spécifie une requête SQL que l'activité de copie doit exécuter avant l'écriture des données dans l'instance gérée. Elle n'est appelée qu'une seule fois par copie. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non |
| sqlWriterStoredProcedureName |Ce nom est celui de la procédure stockée qui définit le mode d'application des données sources dans une table cible. <br/>Cette procédure stockée est *appelée par lot*. Pour effectuer une opération qui ne s'exécute qu'une seule fois et n'a rien à voir avec les données sources (par exemple, supprimer ou tronquer), utilisez la propriété `preCopyScript`. |Non |
| storedProcedureParameters |Ces paramètres sont utilisés pour la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. |Non |
| sqlWriterTableType |Cette propriété spécifie le nom du type de table à utiliser dans la procédure stockée. L'activité de copie rend les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. |Non |

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

**Exemple 2 : Appeler une procédure stockée pendant la copie**

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Meilleures pratiques de chargement de données dans Azure SQL Database Managed Instance

Lors de la copie de données dans Azure SQL Database Managed Instance, vous pourriez avoir besoin d’un comportement d’écriture différent :

- [Append](#append-data): Mes données sources contiennent uniquement de nouveaux enregistrements.
- [Upsert](#upsert-data) : Mes données sources contiennent à la fois des insertions et des mises à jour.
- [Remplacer](#overwrite-the-entire-table) : Je veux recharger la totalité de la table de dimension à chaque fois.
- [Écrire avec une logique personnalisée](#write-data-with-custom-logic) : J’ai besoin d’un traitement supplémentaire avant l’insertion finale dans la table de destination. 

Consultez les sections correspondantes pour savoir comment effectuer des configurations dans Azure Data Factory et connaître les bonnes pratiques associées.

### <a name="append-data"></a>Ajout de données

L’ajout de données est le comportement par défaut de ce connecteur de récepteur Azure SQL Database Managed Instance. Azure Data Factory effectue une insertion en bloc pour écrire efficacement dans votre table. Vous pouvez configurer la source et le récepteur en conséquence dans l’activité de copie.

### <a name="upsert-data"></a>Effectuer un upsert de données

**Option 1 :** Quand vous avez une grande quantité de données à copier, utilisez l’approche suivante pour effectuer un upsert : 

- Tout d’abord, utilisez une [table temporaire](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) pour charger en bloc tous les enregistrements par le biais de l’activité de copie. Étant donné que les opérations sur des tables temporaires ne sont pas journalisées, vous pouvez charger des millions d’enregistrements en quelques secondes.
- Exécutez une activité de procédure stockée dans Azure Data Factory pour appliquer une instruction [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERT/UPDATE. Utilisez la table temporaire comme source pour exécuter toutes les mises à jour ou insertions en tant que transaction unique. De cette façon, le nombre d’allers-retours et d’opérations de journalisation est réduit. À la fin de l’activité de procédure stockée, la table temporaire peut être tronquée pour être prête pour le prochain cycle d’upsert.

Par exemple, dans Azure Data Factory, vous pouvez créer un pipeline avec une **activité de copie** chaînée avec une **activité de procédure stockée**. La première activité copie des données à partir de votre magasin source dans une table temporaire, par exemple **##UpsertTempTable**, comme nom de table dans le jeu de données. La seconde activité appelle ensuite une procédure stockée pour fusionner les données sources de la table temporaire dans la table cible et nettoyer la table temporaire.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Dans votre base de données, définissez une procédure stockée avec la logique MERGE, comme dans l’exemple suivant, qui est pointée à partir de l’activité de procédure stockée précédente. Supposons que la cible est la table **Marketing** comportant trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération d’upsert sur la colonne **ProfileID**.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Option 2 :** Vous pouvez également choisir d’[appeler une procédure stockée dans une activité de copie](#invoke-a-stored-procedure-from-a-sql-sink). Cette approche exécute chaque ligne de la table source au lieu d’utiliser l’insertion en bloc comme approche par défaut dans l’activité de copie, ce qui n’est pas approprié pour les opérations d’upsert à grande échelle.

### <a name="overwrite-the-entire-table"></a>Remplacer l’intégralité de la table

Vous pouvez configurer la propriété **preCopyScript** dans un récepteur d’activité de copie. Dans le cas présent, pour chaque activité de copie qui s’exécute, Azure Data Factory exécute d’abord le script. Il exécute ensuite la copie pour insérer les données. Par exemple, pour remplacer l’intégralité de la table par les données les plus récentes, spécifiez un script pour d’abord supprimer tous les enregistrements avant de charger en bloc les nouvelles données à partir de la source.

### <a name="write-data-with-custom-logic"></a>Écrire des données avec une logique personnalisée

Les étapes permettant d’écrire des données à l’aide d’une logique personnalisée sont semblables à celles décrites dans la section [Effectuer un upsert de données](#upsert-data). Quand vous devez appliquer un traitement supplémentaire avant l’insertion finale des données sources dans la table de destination, à grande échelle, vous pouvez effectuer l’une de ces deux actions : 

- Charger les données dans une table temporaire, puis appeler une procédure stockée.
- Appeler une procédure stockée pendant la copie.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Appel d'une procédure stockée à partir d'un récepteur SQL

Quand vous copiez des données dans Azure SQL Database Managed Instance, vous pouvez également configurer et appeler une procédure stockée spécifiée par l’utilisateur avec des paramètres supplémentaires.

> [!TIP]
> Appeler une procédure stockée traite les données ligne par ligne, et non pas en bloc, ce qui n’est pas recommandé pour la copie à grande échelle. Pour en savoir plus, veuillez consultez l’article [Meilleures pratiques de chargement de données dans Azure SQL Database Managed Instance](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Vous pouvez utiliser une procédure stockée à la place des mécanismes de copie intégrée. Par exemple, quand vous souhaitez appliquer un traitement supplémentaire avant l’insertion finale de données sources dans la table de destination. Fusionner des colonnes, rechercher des valeurs supplémentaires et insérer des données dans plusieurs tables sont des exemples de traitement supplémentaire.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert simple dans une table de la base de données SQL Server. Supposons que les données d’entrée et la table réceptrice **Marketing** ont trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération upsert basée sur la colonne **ProfileID** et appliquez-la uniquement à une catégorie spécifique.

**Jeu de données de sortie :** « tableName » correspond au nom du paramètre de type de table dans votre procédure stockée, comme indiqué dans le script de la procédure stockée suivante :

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

Définissez la section **Récepteur SQL** de l’activité de copie comme suit :

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

Dans votre base de données, définissez la procédure stockée avec le même nom que **SqlWriterStoredProcedureName**. Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Le nom de paramètre du type de table de la procédure stockée doit être identique au **tableName** défini dans le jeu de données.

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

Dans votre base de données, définissez le type de table avec le même nom que **sqlWriterTableType**. Le schéma du type de table doit être identique au schéma retourné par vos données d'entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

La fonction de procédure stockée tire parti des [paramètres table](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mappage de type de données pour Azure SQL Database Managed Instance

Lors de la copie de données vers et depuis Azure SQL Database Managed Instance, les mappages suivants sont utilisés entre les types de données Azure SQL Database Managed Instance et les types de données intermédiaires Azure Data Factory. Pour savoir comment l'activité de copie mappe le schéma et le type de données entre la source et le récepteur, consultez [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Database Managed Instance | Type de données intermédiaires Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Pour les types de données mappés avec le type intermédiaire Decimal, Azure Data Factory prend actuellement en charge une précision maximale de 28. Si vous disposez de données qui nécessitent une précision supérieure à 28, pensez à les convertir en chaîne dans une requête SQL.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l'activité de copie d'Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
