---
title: Copier des données depuis ou vers MongoDB
description: Découvrez comment copier des données de MongoDB vers des banques de données réceptrices prises en charge, ou depuis des banques de données réceptrices prises en charge vers MongoDB, à l’aide de l’activité de copie disponible dans le pipeline Azure Data Factory.
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 06/01/2021
ms.openlocfilehash: c566b3d13a58b1ad6095f9e2443e10d6db33dedb
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749528"
---
# <a name="copy-data-from-or-to-mongodb-by-using-azure-data-factory"></a>Copier des données depuis ou vers MongoDB à l’aide d’Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers une base de données MongoDB. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

>[!IMPORTANT]
>Cette nouvelle version du connecteur MongoDB dans ADF fournit une meilleure prise en charge native de MongoDB. Si vous utilisez le connecteur MongoDB précédent, qui est pris en charge tel quel à des fins de compatibilité descendante, dans votre solution, consultez l’article [Connecteur MongoDB (hérité)](connector-mongodb-legacy.md).


## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données MongoDB vers toute banque de données réceptrice prise en charge, ou de toute banque de données source prise en charge vers une base de données MongoDB. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur MongoDB prend en charge les **versions jusqu’à 4.2**.


## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur MongoDB.


## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié MongoDB sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type |La propriété type doit être définie sur : **MongoDbV2** |Oui |
| connectionString |Spécifiez la chaîne de connexion MongoDB, par exemple `mongodb://[username:password@]host[:port][/[database][?options]]`. Pour plus d’informations, consultez le [manuel MongoDB sur la chaîne de connexion](https://docs.mongodb.com/manual/reference/connection-string/). <br/><br /> Vous pouvez également stocker une chaîne de connexion dans Azure Key Vault. Pour plus d’informations, consultez la section [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| database | Nom de la base de données à laquelle vous souhaitez accéder. | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). Les propriétés suivantes sont prises en charge pour le jeu de données MongoDB :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **MongoDbV2Collection** | Oui |
| collectionName |Nom de la collection dans la base de données MongoDB. |Oui |

**Exemple :**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur MongoDB.

### <a name="mongodb-as-source"></a>MongoDB en tant que source

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **MongoDbV2Source** | Oui |
| filter | Spécifie le filtre de sélection à l’aide d’opérateurs de requête. Pour retourner tous les documents dans une collection, omettez ce paramètre ou passez un document vide ({}). | Non |
| cursorMethods.project | Spécifie les champs à retourner dans les documents pour la projection. Pour retourner tous les champs dans les documents correspondants, omettez ce paramètre. | Non |
| cursorMethods.sort | Spécifie l’ordre dans lequel la requête retourne les documents correspondants. Voir [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Non |
| cursorMethods.limit | Spécifie le nombre maximal de documents retournés par le serveur. Voir [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Non |
| cursorMethods.skip | Spécifie le nombre de documents à ignorer, et à partir de quel endroit MongoDB commence à retourner des résultats. Voir [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Non |
| batchSize | Spécifie le nombre de documents à retourner dans chaque lot de la réponse renvoyée par l’instance MongoDB. Dans la plupart des cas, la modification de la taille de lot n’affectera pas l’utilisateur ou l’application. Cosmos DB limite la taille de chaque lot à 40 Mo, qui est la somme de la taille batchSize du nombre de documents. Par conséquent, diminuez cette valeur si la taille de votre document est trop grande. | Non<br/>(la valeur par défaut est **100**) |

>[!TIP]
>ADF prend en charge la consommation de document BSON en **mode Strict**. Vérifiez que votre requête de filtre est en mode Strict plutôt qu’en mode Shell. Vous trouverez une description plus détaillée dans le [manuel MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="mongodb-as-sink"></a>MongoDB en tant que récepteur

Les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** du récepteur de l’activité de copie doit être définie sur **MongoDbV2Sink**. |Oui |
| writeBehavior |Décrit comment écrire des données dans MongoDB. Les valeurs autorisées sont **insert** et **upsert**.<br/><br/>Le comportement de la valeur **upsert** consiste à remplacer le document si un document portant le même `_id` existe déjà ; sinon, le document est inséré.<br /><br />**Remarque** : Azure Data Factory génère automatiquement un `_id` pour un document si `_id` n’est pas spécifié dans le document d’origine ni par le mappage de colonnes. Cela signifie que vous devez vérifier que votre document comporte un ID afin qu’**upsert** fonctionne comme prévu. |Non<br />(la valeur par défaut est **insert**) |
| writeBatchSize | La propriété **writeBatchSize** contrôle la taille des documents à écrire dans chaque lot. Vous pouvez essayer d’augmenter la valeur de **writeBatchSize** pour améliorer les performances et diminuer la valeur si la taille de votre document est grande. |Non<br />(la valeur par défaut est **10 000**) |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion par lot soit terminée avant d’expirer. La valeur autorisée est timespan. | Non<br/>(la valeur par défaut est **00:30:00** – 30 minutes) |

>[!TIP]
>Pour importer des documents JSON en l’état, voir la section [Importer ou exporter des documents JSON](#import-and-export-json-documents). Pour copier à partir de données sous forme tabulaire, voir [Mappage de schéma](#schema-mapping).

**Exemple**

```json
"activities":[
    {
        "name": "CopyToMongoDB",
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
                "type": "MongoDbV2Sink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importer et exporter des documents JSON

À l’aide de ce connecteur Azure Cosmos MongoDB, vous pouvez facilement :

* Copier des documents entre deux collections MongoDB en l’état.
* Importer des documents JSON de différentes sources dans MongoDB, notamment depuis Azure Cosmos DB, le stockage Blob Azure, Azure Data Lake Store et d’autres magasins basés sur des fichiers pris en charge par Azure Data Factory.
* Exporter des documents JSON d’une collection MongoDB vers différentes banques basées sur des fichiers.

Pour obtenir une telle copie indépendante du schéma, ignorez la section « structure » (également appelée *schéma*) dans le mappage de schéma et de jeu de données dans l’activité de copie.


## <a name="schema-mapping"></a>Mappage de schéma

Pour copier des données de MongoDB vers un récepteur tabulaire ou inversé, consultez [Mappage de schéma](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
