---
title: Copier des données depuis Azure Database for MariaDB
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’Azure Database for MariaDB vers des banques de données réceptrices prises en charge.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 00fdeb71cec5995a229c5408429a0369d4317b66
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931666"
---
# <a name="copy-data-from-azure-database-for-mariadb-using-azure-data-factory"></a>Copier des données à partir d’Azure Database for MariaDB avec Azure Data Factory 

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’Azure Database for MariaDB. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Database for MariaDB est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
 
Vous pouvez copier des données à partir d’Azure Database for MariaDB vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques au connecteur Azure Database for MariaDB.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge par le service lié Azure Database for MariaDB :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **AzureMariaDB** | OUI |
| connectionString | Chaîne de connexion permettant de se connecter à Azure Database for MariaDB. Vous la trouverez dans le portail Azure -> votre base de données Azure Database for MariaDB -> Chaînes de connexion -> ADO.NET. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `pwd` de la chaîne de connexion. Pour plus d’informations, reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "AzureDatabaseForMariaDBLinkedService",
    "properties": {
        "type": "AzureMariaDB",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server={your_server}.mariadb.database.azure.com; Port=3306; Database={your_database}; Uid={your_user}@{your_server}; Pwd={your_password}; SslMode=Preferred;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage du mot de passe dans Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMariaDBLinkedService",
    "properties": {
        "type": "AzureMariaDB",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server={your_server}.mariadb.database.azure.com; Port=3306; Database={your_database}; Uid={your_user}@{your_server}; SslMode=Preferred;"
            },
            "pwd": { 
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Database for MariaDB.

Pour copier des données à partir d’Azure Database for MariaDB, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **AzureMariaDBTable** | OUI |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "AzureDatabaseForMariaDBDataset",
    "properties": {
        "type": "AzureMariaDBTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Database for MariaDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Azure Database for MariaDB.

### <a name="azure-database-for-mariadb-as-source"></a>Azure Database for MariaDB comme source

Pour copier des données à partir d’Azure Database for MariaDB, les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **AzureMariaDBSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMariaDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Database for MariaDB input dataset name>",
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
                "type": "AzureMariaDBSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
