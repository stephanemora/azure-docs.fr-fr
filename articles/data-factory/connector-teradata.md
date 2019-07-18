---
title: Copier des données à partir de Teradata à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez le connecteur Teradata du service Data Factory, qui vous permet de copier des données d’une base de données Teradata vers des magasins de données pris en charge par Data Factory en tant que récepteurs.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561425"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Copier des données à partir de Teradata à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Version actuelle](connector-teradata.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une base de données Teradata. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données Teradata vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Teradata prend en charge :

- Teradata **versions 14.10, 15.0, 15.10, 16.0, 16.10 et 16.20**.
- Copie des données avec l’authentification **De base** ou **Windows**.
- Copie en parallèle à partir de la source Teradata. Pour plus d'informations, consultez [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata).

> [!NOTE]
>
> Azure Data Factory a mis à niveau le connecteur Teradata depuis le runtime d'intégration auto-hébergé v3.18, géré par un pilote ODBC intégré, et offre des options de connexion flexibles ainsi qu’une copie en parallèle out-of-box pour optimiser les performances. Les charges de travail existantes utilisant le connecteur Teradata géré par le fournisseur de données .NET pour Teradata reste pris en charge tel quel, mais nous vous suggérons d’utiliser le nouveau à l’avenir. Notez que le nouveau chemin d’accès requiert un ensemble différent jeu différent de service lié/jeu de données/source de copie. Pour plus de détails de configuration, consultez la section correspondante.

## <a name="prerequisites"></a>Prérequis

Si votre base de données Teradata n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour plus d’informations sur le runtime d’intégration, consultez l’article relatif au [runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md). Le runtime d’intégration fournit un pilote Teradata intégré à partir de la version 3.18. Ainsi, vous n’avez pas besoin d’installer manuellement un pilote. Le pilote requiert « Visual C++ Redistributable 2012 Update 4 » sur la machine dotée du runtime d'intégration auto-hébergé. S'il n'est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

Pour une version du runtime d’intégration auto-hébergé antérieure à la version 3.18, vous devez installer le [fournisseur de données .NET pour Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) version 14 ou ultérieure sur la machine exécutant le runtime d'intégration. 

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Teradata.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Teradata :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Teradata** | OUI |
| connectionString | Spécifie les informations requises pour se connecter à l’instance de base de données Teradata. Consultez les exemples suivants.<br/>Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `password` de la chaîne de connexion. Pour plus d’informations, consultez l'article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| username | Spécifiez le nom d’utilisateur associé à la connexion à la base de données Teradata. S'applique lors de l'utilisation de l’authentification Windows. | Non |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Vous pouvez également choisir de [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). <br>S’applique lors de l’utilisation de l’authentification Windows, ou du référencement du mot de passe dans Key Vault pour l’authentification de base. | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |OUI |

**Exemple : utilisation de l’authentification de base**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : utilisation d’une authentification Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Si vous utilisiez le service lié Teradata géré par le fournisseur de données .NET pour Teradata avec la charge utile suivante, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser le nouveau à l’avenir.

**Charge utile précédente :**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit une liste des propriétés prises en charge par le jeu de données Teradata.

Pour copier des données à partir de Teradata, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **TeradataTable** | OUI |
| database | Nom de la base de données Teradata. | Non (si « query » dans la source de l’activité est spécifié) |
| table | Nom de la table dans la base de données Teradata. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple :**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Si vous utilisiez le jeu de données type « RelationalTable » comme suit, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser le nouveau à l’avenir.

**Charge utile précédente :**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source Teradata.

### <a name="teradata-as-source"></a>Teradata en tant que source

> [!TIP]
>
> Apprenez-en plus sur la façon de charger efficacement des données à partir de Teradata à l'aide du partitionnement des données dans la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata).

Pour copier des données à partir de Teradata, les propriétés prises en charge dans la section **source** de l'activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur : **TeradataSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`.<br>Lorsque vous activez la charge partitionnée, vous devez utiliser le(s) paramètre(s) de partition correspondant(s) dans votre requête. Consultez des exemples dans la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non (si la table du jeu de données est spécifiée) |
| partitionOptions | Spécifie les options de partitionnement des données utilisées pour charger des données à partir de Teradata. <br>Les valeurs autorisées sont les suivantes : **Aucun** (par défaut), **Hachage** et **DynamicRange**.<br>Lorsque l’option de partition est activée (valeur autre que « Aucun »), configurez également le paramètre **[`parallelCopies`](copy-activity-performance.md#parallel-copy)** sur l’activité de copie (4, par exemple), ce qui détermine le degré de parallélisme pour charger simultanément des données à partir de la base de données Teradata. | Non |
| partitionSettings | Spécifiez le groupe de paramètres pour le partitionnement des données. <br>S'applique lorsque l'option de partition est autre que `None`. | Non |
| partitionColumnName | Spécifiez le nom de la colonne source **dans type entier** qu'utilisera le partitionnement par plages de valeurs pour la copie en parallèle. S'il n'est pas spécifié, la clé primaire de la table sera automatiquement détectée et utilisée en tant que colonne de partition. <br>S'applique lorsque l'option de partition est `Hash` ou `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, connectez `?AdfHashPartitionCondition` ou `?AdfRangePartitionColumnName` dans la clause WHERE. Consultez l’exemple de la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non |
| partitionUpperBound | Valeur maximale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque de l’option de partition est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionUpbound` dans la clause WHERE. Consultez l’exemple de la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non |
| PartitionLowerBound | Valeur minimale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque de l’option de partition est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionLowbound` dans la clause WHERE. Consultez l’exemple de la section [Copie en parallèle à partir de Teradata](#parallel-copy-from-teradata). | Non |

> [!NOTE]
>
> Si vous utilisiez une source de copie de type « RelationalSource », elle est toujours prise en charge telle que, mais ne prend pas en charge la nouvelle charge en parallèle intégrée à partir de Teradata (options de partition). Nous vous suggérons d’utiliser cette nouvelle source à l’avenir.

**Exemple : copie de données à l’aide de la requête de base sans partition**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Copie en parallèle à partir de Teradata

Le connecteur de fabrique de données Teradata propose un partitionnement de données intégré pour copier des données à partir de Teradata en parallèle avec de hautes performances. Vous trouverez des options de partitionnement de données sur l’activité de copie -> source Teradata :

![Options de partition](./media/connector-teradata/connector-teradata-partition-options.png)

Lorsque vous activez la copie partitionnée, la fabrique de données exécute des requêtes en parallèle sur votre source Teradata pour charger des données par partitions. Le degré de parallélisme est configuré et contrôlé via le paramètre **[`parallelCopies`](copy-activity-performance.md#parallel-copy)** sur l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur quatre, la fabrique de données génère et exécute simultanément quatre requêtes basées l'option de partition et les paramètres que vous avez spécifiés, chacune récupérant des données à partir de votre base de données Teradata.

Il vous est recommandé d’activer la copie en parallèle avec partitionnement des données notamment lorsque vous chargez une grande quantité de données à partir de la base de données Teradata. Voici quelques suggestions de configurations pour différents scénarios :

| Scénario                                                     | Paramètres suggérés                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Chargement complet à partir d'une table volumineuse                                   | **Option de partition** : Hachage. <br><br/>Lors de l'exécution, la fabrique de données détecte automatiquement la colonne PK, y applique un hachage et copie les données par partitions. |
| Charger une grande quantité de données à l’aide d'une requête personnalisée                 | **Option de partition** : Hachage.<br>**Requête** : `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour appliquer la partition par hachage. Si cette colonne n'est pas spécifiée, ADF détecte automatiquement la colonne PK de la table que vous avez spécifiée dans le jeu de données Teradata.<br><br>Lors de l’exécution, la fabrique de données remplace `?AdfHashPartitionCondition` par la logique de partition de hachage et l'envoie à Teradata. |
| Charger une grande quantité de données à l’aide d’une requête personnalisée, disposant d'une colonne d’entiers avec valeur uniformément distribuée pour le partitionnement par plages de valeurs | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Requête** : `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Vous pouvez procéder au partitionnement par rapport à la colonne avec type de données Integer.<br>**Limite supérieure de partition** et **limite inférieure de partition** : Spécifiez si vous souhaitez filtrer par rapport à la colonne de partition pour récupérer uniquement les données entre les plages inférieure et supérieure.<br><br>Lors de l’exécution, la fabrique de données remplace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` et `?AdfRangePartitionLowbound` par le nom réel de la colonne et les plages de valeurs de chaque partition, et les envoie à Teradata. <br>Par exemple, si « l'ID » de la colonne de partition « ID » est défini sur une limite inférieure de 1 et une limite supérieure de 80, avec une copie en parallèle définie sur 4, ADF récupère les données par 4 partitions avec l’ID entre [1,20], [21, 40], [41, 60] et [61, 80]. |

**Exemple : requête avec partition par hachage**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemple : requête avec partition dynamique par spécification de plages de valeurs**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mappage de type de données pour Teradata

Lors de la copie des données à partir de Teradata, les mappages suivants sont utilisés entre les types de données Teradata et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données Teradata | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Chaîne |
| Clob |Chaîne |
| Date |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Entier |Int32 |
| Interval Day |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Day To Hour |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Day To Minute |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Day To Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Hour |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Hour To Minute |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Hour To Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Minute |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Minute To Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Month |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Second |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Year |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Interval Year To Month |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Number |Double |
| Période (date) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (heure) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (heure avec fuseau horaire) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (timestamp) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Période (timestamp avec fuseau horaire) |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| SmallInt |Int16 |
| Temps |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |Datetime |
| Timestamp With Time Zone |Datetime |
| VarByte |Byte[] |
| VarChar |Chaîne |
| VarGraphic |Non pris en charge. Appliquer un cast explicite dans la requête source. |
| Xml |Non pris en charge. Appliquer un cast explicite dans la requête source. |


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
