---
title: Copier des données vers ou à partir d’Azure SQL Database à l’aide de Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de magasins de données sources pris en charge vers Azure SQL Database ou de SQL Database vers des magasins de données récepteurs pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 5287a1d1f09a7057590b455c14aa7f70128ad7fa
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053635"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copier des données vers ou à partir d’Azure SQL Database à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [Version actuelle](connector-azure-sql-database.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers ou à partir d’Azure SQL Database. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données entre Azure SQL Database et tout magasin de données récepteur pris en charge. Et vous pouvez copier des données à partir de tout magasin de données source pris en charge vers Azure SQL Database. Pour obtenir la liste des magasins de données pris en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure SQL Database prend en charge les fonctions suivantes :

- Copie de données à l’aide de l’authentification SQL et de l’authentification du jeton de l’application Azure Active Directory (Azure AD) avec un principal de service ou Managed Service Identity (MSI).
- En tant que source, récupération de données à l’aide d’une requête SQL ou d’une procédure stockée.
- En tant que récepteur, ajout de données à une table de destination ou appel d’une procédure stockée avec une logique personnalisée pendant la copie.

> [!IMPORTANT]
> Si vous copiez des données à l’aide du runtime d’intégration Azure Data Factory, configurez un [pare-feu de serveur Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) de façon à ce que les services Azure puissent accéder au serveur.
> Si vous copiez des données à l’aide d’un runtime d’intégration auto-hébergé, configurez le pare-feu du serveur Azure SQL pour autoriser la plage IP appropriée. Cette plage inclut l’adresse IP de l’ordinateur qui est utilisé pour se connecter à Azure SQL Database.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory propres à un connecteur Azure SQL Database.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour un service lié Azure SQL Database sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **AzureSqlDatabase**. | OUI |
| connectionString | Spécifiez les informations requises pour la connexion à l’instance Azure SQL Database pour la propriété **connectionString**. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | Oui, quand vous utilisez l’authentification Azure AD avec le principal de service. |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui, quand vous utilisez l’authentification Azure AD avec le principal de service. |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du Portail Azure. | Oui, quand vous utilisez l’authentification Azure AD avec le principal de service. |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter au magasin de données. Vous pouvez utiliser Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre magasin de données se trouve sur un réseau privé. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non  |

Pour en savoir plus sur les autres types d’authentification, consultez les sections suivantes sur les prérequis et les exemples JSON, respectivement :

- [Authentification SQL](#sql-authentication)
- [Authentification du jeton d’application Azure AD : principal de service](#service-principal-authentication)
- [Authentification du jeton d’application Azure AD : Managed Service Identity](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>Authentification SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemple de service lié qui utilise l’authentification SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour utiliser l’authentification du jeton d’application Azure AD basée sur le principal de service, effectuez les étapes suivantes :

1. **[Créez une application Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)** dans le portail Azure. Prenez note du nom de l’application et des valeurs suivantes qui définissent le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. **[Provisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** pour votre serveur Azure SQL sur le portail Azure, si ce n’est pas déjà fait. L’administrateur Azure AD doit être un utilisateur Azure AD ou un groupe Azure AD, mais il ne doit pas s’agir d’un principal de service. Vous devez effectuer cette étape pour qu’à l’étape suivante vous puissiez utiliser une identité Azure AD pour créer un utilisateur de base de données autonome pour le principal de service.

3. **[Créez des utilisateurs de base de données autonome](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pour le principal de service. Connectez-vous à la base de données vers ou à partir de laquelle vous souhaitez copier des données à l’aide d’outils tels que SSMS, avec une identité Azure AD qui a au moins l’autorisation ALTER ANY USER. Exécutez le code T-SQL suivant : 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Accordez les autorisations requises par le principal de service** comme vous le feriez d’habitude pour des utilisateurs SQL ou autres. Exécutez le code suivant :

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Dans Azure Data Factory, **configurez un service lié Azure SQL Database.**


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemple de service lié qui utilise l’authentification du principal de service

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-service-identity-authentication"></a>Authentification Managed Service Identity

Une fabrique de données peut être associée à une [identité de service managé](data-factory-service-identity.md), qui représente la fabrique de données en question. Vous pouvez utiliser cette identité de service pour l’authentification Azure SQL Database. La fabrique désignée peut accéder et copier des données vers ou à partir de votre base de données à l’aide de cette identité.

Pour utiliser l’authentification du jeton d’application Azure AD basée sur une MSI, effectuez les étapes suivantes :

1. **Créez un groupe dans Azure AD.** Faites de la fabrique MSI un membre du groupe.

    a. Recherchez l’identité du service de la fabrique de données sur le portail Azure. Accédez aux **Propriétés** de votre fabrique de données. Copiez l’ID de l’identité du service.

    b. Installez le module [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Connectez-vous à l’aide de la commande `Connect-AzureAD`. Exécutez les commandes suivantes pour créer un groupe et ajouter la MSI de fabrique de données en tant que membre.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Provisionnez un administrateur Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** pour votre serveur Azure SQL sur le portail Azure, si ce n’est pas déjà fait. L’administrateur Azure AD peut être un utilisateur Azure AD ou un groupe Azure AD. Si vous accordez au groupe avec MSI un rôle d’administrateur, ignorez les étapes 3 et 4. L’administrateur aura un accès complet à la base de données.

3. **[Créez des utilisateurs de base de données autonome](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** pour le groupe Azure AD. Connectez-vous à la base de données vers ou à partir de laquelle vous souhaitez copier des données à l’aide d’outils tels que SSMS, avec une identité Azure AD qui a au moins l’autorisation ALTER ANY USER. Exécutez le code T-SQL suivant : 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Accordez les autorisations requises par le groupe Azure AD** comme vous le feriez d’habitude pour des utilisateurs SQL et autres. Par exemple, exécutez le code suivant :

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. Dans Azure Data Factory, **configurez un service lié Azure SQL Database.**

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Exemple de service lié qui utilise l’authentification MSI

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure SQL Database.

Pour copier des données vers ou à partir d’Azure SQL Database, affectez la valeur **AzureSqlTable** à la propriété **type** du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du jeu de données doit être définie sur **AzureSqlTable**. | OUI |
| TableName | Nom de la table ou de la vue dans l’instance Azure SQL Database à laquelle le service lié fait référence. | OUI |

#### <a name="dataset-properties-example"></a>Exemple de propriétés du jeu de données

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure SQL Database en tant que source et récepteur.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database en tant que source

Pour copier des données d’Azure SQL Database, affectez la valeur **SqlSource** à la propriété **type** dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source d’activité de copie doit être définie sur **SqlSource**. | OUI |
| SqlReaderQuery | Utiliser la requête SQL personnalisée pour lire les données. Exemple : `select * from MyTable`. | Non  |
| sqlReaderStoredProcedureName | Nom de la procédure stockée qui lit les données de la table source. La dernière instruction SQL doit être une instruction SELECT dans la procédure stockée. | Non  |
| storedProcedureParameters | Paramètres de la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms ou de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non  |

### <a name="points-to-note"></a>Points à noter

- Si **sqlReaderQuery** est spécifié pour **SqlSource**, l’activité de copie exécute cette requête sur la source Azure SQL Database pour obtenir les données. Vous pouvez aussi spécifier une procédure stockée. Spécifiez **sqlReaderStoredProcedureName** et **storedProcedureParameters** si la procédure stockée accepte des paramètres.
- Si vous ne spécifiez pas **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, les colonnes définies dans la section **structure** du JSON de jeu de données sont utilisées pour créer une requête. `select column1, column2 from mytable` est exécuté sur Azure SQL Database. Si la définition du jeu de données n’a pas de **structure**, toutes les colonnes de la table sont sélectionnées.
- Quand vous utilisez **sqlReaderStoredProcedureName**, vous devez tout de même spécifier une propriété **tableName** factice dans le jeu de données JSON.

#### <a name="sql-query-example"></a>Exemple de requête SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>Exemple de procédure stockée

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>Définition de la procédure stockée

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database en tant que récepteur

Pour copier des données vers Azure SQL Database, affectez la valeur **SqlSink** à la propriété **type** dans le récepteur d’activité de copie. Les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du récepteur d’activité de copie doit être définie sur **SqlSink**. | OUI |
| writeBatchSize | Insère des données dans la table SQL quand la taille de la mémoire tampon atteint **writeBatchSize**.<br/> La valeur autorisée est **integer** (nombre de lignes). | Non. La valeur par défaut est 10000. |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/> La valeur autorisée est **timespan**. Exemple : « 00:30:00 » (30 minutes). | Non  |
| preCopyScript | Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Azure SQL Database. Elle n’est appelée qu’une seule fois par copie. Utilisez cette propriété pour nettoyer les données préchargées. | Non  |
| sqlWriterStoredProcedureName | Nom de la procédure stockée qui définit comment appliquer des données sources dans une table cible. Un exemple consiste à faire des upserts ou des transformations en utilisant votre propre logique métier. <br/><br/>Cette procédure stockée est **appelée par lot**. Pour les opérations qui ne s’exécutent qu’une seule fois et n’ont rien à faire avec les données sources, utilisez la propriété `preCopyScript`. La suppression et la troncature sont des exemples d’opérations. | Non  |
| storedProcedureParameters |Paramètres de la procédure stockée.<br/>Les valeurs autorisées sont des paires de noms et de valeurs. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non  |
| sqlWriterTableType | Spécifiez le nom du type de table à utiliser dans la procédure stockée. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. | Non  |

> [!TIP]
> Quand vous copiez des données vers Azure SQL Database, l’activité de copie ajoute des données à la table du récepteur par défaut. Pour effectuer une upsert ou une logique métier supplémentaire, utilisez la procédure stockée dans **SqlSink**. Pour en savoir plus, consultez [Appel d’une procédure stockée à partir d’un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Exemple d’ajout de données

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Exemple d’appel d’une procédure stockée pendant la copie pour upsert

Pour en savoir plus, consultez [Appel d’une procédure stockée à partir d’un récepteur SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

Cette section montre comment copier des données d’une table source sans colonne d’identité vers une table de destination avec une colonne d’identité.

#### <a name="source-table"></a>Table source

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Table de destination

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> La table cible a une colonne d’identité.

#### <a name="source-dataset-json-definition"></a>Définition du jeu de données JSON source

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
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

#### <a name="destination-dataset-json-definition"></a>Définition de jeu de données JSON de destination

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
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

> [!NOTE]
> La table source et cible ont des schémas différents. 

La cible a une colonne supplémentaire avec une identité. Dans ce scénario, vous devez spécifier la propriété **structure** dans la définition du jeu de données cible, qui n’inclut pas la colonne d’identité.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Appel d’une procédure stockée pour un récepteur SQL

Quand vous copiez des données dans Azure SQL Database, vous pouvez également configurer et appeler une procédure stockée spécifiée par l’utilisateur avec des paramètres supplémentaires.

Vous pouvez utiliser une procédure stockée à la place des mécanismes de copie intégrée. Elles sont généralement utilisées quand une upsert, insertion plus mise à jour, ou traitement supplémentaire doivent être effectués avant l’insertion finale des données sources dans la table de destination. Fusionner des colonnes, rechercher des valeurs supplémentaires et effectuer des insertions dans plusieurs tables sont des exemples de traitement supplémentaire.

L’exemple suivant montre comment utiliser une procédure stockée pour effectuer une opération upsert dans une table d’Azure SQL Database. On part du principe que les données d’entrée et la table réceptrice **Marketing** ont trois colonnes : **ProfileID**, **State** et **Category**. Effectuez l’opération upsert basée sur la colonne **ProfileID** et appliquez-la uniquement à une catégorie spécifique.

#### <a name="output-dataset"></a>Jeu de données de sortie

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Définissez la section **SqlSink** dans l’activité de copie :

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

Dans votre base de données, définissez la procédure stockée portant le même nom que **SqlWriterStoredProcedureName**. Elle gère les données d’entrée à partir de la source que vous avez spécifiée et les fusionne dans la table de sortie. Le nom du paramètre de la procédure stockée doit être le même que le **tableName** défini dans le jeu de données.

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

Dans votre base de données, définissez le type de table portant le même nom que **sqlWriterTableType**. Le schéma du type de table doit être identique au schéma retourné par vos données d’entrée.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL,
)
```

La fonction de procédure stockée tire parti des [paramètres Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mappage de type de données pour Azure SQL Database

Quand vous copiez des données vers ou à partir d’Azure SQL Database, les mappages suivants sont utilisés entre les types de données Azure Data Factory et les types de données intermédiaires Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données sources au récepteur, consultez [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Azure SQL Database | Type de données intermédiaires d’Azure Data Factory |
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
| text |String, Char[] |
| time |intervalle de temps |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
