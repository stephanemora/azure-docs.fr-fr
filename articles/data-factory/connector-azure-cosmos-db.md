---
title: Copier des données depuis ou vers Azure Cosmos DB (API SQL) à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Azure Cosmos DB (API SQL), ou d’Azure Cosmos DB vers des banques de données réceptrices prises en charge.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259515"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copier des données depuis ou vers Azure Cosmos DB (API SQL) à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Version actuelle](connector-azure-cosmos-db.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers Azure Cosmos DB (API SQL). Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

>[!NOTE]
>Ce connecteur prend uniquement en charge la copie des données vers/depuis l’API SQL Cosmos DB. Pour l'API MongoDB, reportez-vous à [Connecteur de l'API Azure Cosmos DB pour MongoDB](connector-azure-cosmos-db-mongodb-api.md). Les autres types d’API ne sont pas pris en charge actuellement.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’Azure Cosmos DB (API SQL) vers toute banque de données réceptrice prise en charge, ou copier des données de toute banque de données source prise en charge vers Azure Cosmos DB (API SQL). Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Vous pouvez utiliser le connecteur Azure Cosmos DB (API SQL) pour :

- Copier des données depuis et vers l’[API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) Azure Cosmos DB.
- Écrire dans Azure Cosmos DB comme **insert** ou **upsert**.
- Importer et exporter des documents JSON en l’état, ou copier des données depuis ou vers un jeu de données tabulaire. Les exemples incluent une base de données SQL et un fichier CSV. Pour copier des documents en l'état depuis ou vers des fichiers JSON ou une autre collection Azure Cosmos DB, consultez Importer ou exporter des documents JSON.

Data Factory s’intègre à la [bibliothèque d’exécuteur en bloc Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) pour offrir les meilleures performances quand vous écrivez dans Azure Cosmos DB.

> [!TIP]
> La [vidéo de la migration des données](https://youtu.be/5-SRNiC_qOU) vous guide tout au long des étapes de la copie des données depuis le stockage Blob Azure vers Azure Cosmos DB. La vidéo décrit également les considérations de réglage des performances pour l’ingestion des données dans Azure Cosmos DB en général.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres à Azure Cosmos DB (API SQL).

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Azure Cosmos DB (API SQL) sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** doit être définie sur **CosmosDb**. | Oui |
| connectionString |Spécifiez les informations requises pour se connecter à la base de données Azure Cosmos DB.<br />**Remarque**: Vous devez spécifier les informations de base de données dans la chaîne de connexion, comme indiqué dans les exemples ci-dessous. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également définir une clé de compte dans Azure Key Vault et extraire la configuration `accountKey` de la chaîne de connexion. Reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md) pour plus de détails. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Vous pouvez utiliser Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. Si cette propriété n’est pas spécifiée, Azure Integration Runtime par défaut est utilisé. |Non  |

**Exemple**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage de la clé de compte dans Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
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

Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Cosmos DB (API SQL). 

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). 

Pour copier des données à partir de ou vers Azure Cosmos DB (API SQL), affectez la valeur **DocumentDbCollection** à la propriété **type** du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du jeu de données doit être définie sur **DocumentDbCollection**. |Oui |
| collectionName |Nom de la collection de documents Azure Cosmos DB. |Oui |

**Exemple**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma par Data Factory

Pour les banques de données sans schéma telles qu’Azure Cosmos DB, l’activité de copie déduit le schéma de l’une des manières décrites dans la liste suivante. Sauf si vous souhaitez [importer ou exporter des documents JSON en l’état](#import-or-export-json-documents), la bonne pratique consiste à spécifier la structure des données dans la section **structure**.

* Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, Azure Data Factory respecte cette structure en tant que schéma. 

    Si une ligne ne contient pas de valeur pour une colonne, une valeur null est fournie pour celle-ci.
* Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory déduit le schéma à l’aide de la première ligne dans les données. 

    Si la première ligne ne contient pas le schéma complet, certaines colonnes ne seront pas incluses dans le résultat de l’opération de copie.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section fournit la liste des propriétés prises en charge par Azure Cosmos DB (API SQL) en tant que source et récepteur.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API SQL) en tant que source

Pour copier des données à partir d’Azure Cosmos DB (SQL API), affectez la valeur **DocumentDbCollectionSource** au type **source** dans l’activité de copie. 

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** de la source de l’activité de copie doit être définie sur **DocumentDbCollectionSource**. |Oui |
| query |Spécifiez la requête Azure Cosmos DB pour lire les données.<br/><br/>Exemple :<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Non  <br/><br/>À défaut de spécification, cette instruction SQL est exécutée : `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caractère spécial qui indique que le document est imbriqué et comment aplatir le jeu de résultats.<br/><br/>Par exemple, si une requête Azure Cosmos DB retourne le résultat imbriqué `"Name": {"First": "John"}`, l’activité de copie identifie le nom de colonne comme `Name.First` avec la valeur « John » quand la valeur de **nestedSeparator** est **.** (point). |Non <br />(la valeur par défaut est **.** (point)) |

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API SQL) en tant que récepteur

Pour copier des données vers Azure Cosmos DB (SQL API), affectez la valeur **DocumentDbCollectionSink** au type **récepteur** dans l’activité de copie. 

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du récepteur de l’activité de copie doit être définie sur **DocumentDbCollectionSink**. |Oui |
| writeBehavior |Décrit comment écrire des données dans Azure Cosmos DB. Les valeurs autorisées sont **insert** et **upsert**.<br/><br/>Le comportement de la valeur **upsert** consiste à remplacer le document si un document portant le même identificateur existe déjà ; sinon, le document est inséré.<br /><br />**Remarque**: Azure Data Factory génère automatiquement un ID pour un document s’il n’est pas spécifié dans le document d’origine ni par le mappage de colonnes. Cela signifie que vous devez vérifier que votre document comporte un ID afin qu’**upsert** fonctionne comme prévu. |Non <br />(la valeur par défaut est **insert**) |
| writeBatchSize | Data Factory utilise la [bibliothèque d’exécuteur en bloc Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) pour écrire les données dans Azure Cosmos DB. La propriété **writeBatchSize** contrôle la taille des documents fournis par ADF à la bibliothèque. Vous pouvez essayer d’augmenter la valeur de **writeBatchSize** pour améliorer les performances et diminuer la valeur si la taille de votre document est grande (voir les conseils ci-dessous). |Non <br />(la valeur par défaut est **10 000**) |
| nestingSeparator |Caractère spécial dans le nom de colonne **source** qui indique qu’un document imbriqué est nécessaire. <br/><br/>Par exemple, `Name.First` dans la structure du jeu de données de sortie génère la structure JSON suivante dans le document Azure Cosmos DB quand **nestedSeparator** a la valeur **.** (point) : `"Name": {"First": "[value maps to this column from source]"}`  |Non <br />(la valeur par défaut est **.** (point)) |

>[!TIP]
>Cosmos DB limite la taille des demandes uniques à 2 Mo. La formule est la suivante : taille de la demande = taille de document unique * taille d’écriture Batch. Si vous rencontrez le message d’erreur **« La taille de la demande est trop grande »**, **réduisez la valeur de `writeBatchSize`** dans la configuration du récepteur de copie.

**Exemple**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>Importer ou exporter des documents JSON

À l’aide de ce connecteur Azure Cosmos DB (SQL API), vous pouvez facilement :

* Importer des documents JSON de différentes sources dans Azure Cosmos DB, notamment depuis le stockage Blob Azure, Azure Data Lake Store et d’autres banques de données basées sur des fichiers prises en charge par Azure Data Factory.
* Exporter des documents JSON d’une collection Azure Cosmos DB vers différentes banques basées sur des fichiers.
* Copier des documents entre deux collections Azure Cosmos DB en l’état.

Pour obtenir une copie indépendante du schéma :

* Lors de l’utilisation de l’outil de copie de données, sélectionnez l’option **Exporter en l’état dans des fichiers JSON ou une collection Cosmos DB**.
* Lors de l’utilisation de la création d’activité, ne spécifiez pas la section **structure** (autrement appelée *schema*) dans le jeu de données Azure Cosmos DB. Ne spécifiez pas non plus la propriété **nestingSeparator** dans la source ou le récepteur Azure Cosmos DB de l’activité de copie. Lors de l’importation depuis ou de l’exportation vers des fichiers JSON, dans le jeu de données du magasin de fichiers correspondant, spécifiez le type **format** **JsonFormat** et configurez **filePattern** comme décrit dans la section [Format JSON](supported-file-formats-and-compression-codecs.md#json-format). Ne spécifiez pas la section **structure** et ignorez le reste des paramètres de format.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
