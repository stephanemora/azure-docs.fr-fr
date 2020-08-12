---
title: Copier des données depuis et vers Snowflake
description: Découvrez comment copier des données depuis et vers Snowflake à l’aide d’Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/30/2020
ms.openlocfilehash: 48248b07b64278d5c8d4f297bf83df813aa486fe
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529498"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Copier des données depuis et vers Snowflake à l’aide d’Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Snowflake. Pour plus d’informations sur Data Factory, consultez [l’article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Snowflake est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec une table [Prise en charge de la matrice source/récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Pour l’activité de copie, ce connecteur Snowflake prend en charge les fonctions suivantes :

- Copiez des données à partir de Snowflake qui utilise la commande [COPY into [emplacement]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) de Snowflake pour obtenir les meilleures performances.
- Copiez des données dans Snowflake qui utilise la commande [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) de Snowflake pour obtenir les meilleures performances. Il prend en charge Snowflake sur Azure.

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes donnent des précisions sur les propriétés utilisées qui définissent des entités Data Factory propres à un connecteur Snowflake.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour un service lié Snowflake sont les suivantes.

| Propriété         | Description                                                  | Obligatoire |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | La propriété type doit être définie sur **Snowflake**.              | Oui      |
| connectionString | Spécifie les informations requises pour se connecter à l’instance de Snowflake. Vous pouvez choisir de placer le mot de passe ou la chaîne de connexion entière dans Azure Key Vault. Pour plus d'informations, reportez-vous aux exemples sous le tableau et à l'article [Stocker des informations d'identification dans Azure Key Vault](store-credentials-in-key-vault.md).<br><br>Paramètres standard :<br>- **Nom du compte :** [nom de compte complet](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) de votre compte Snowflake (incluant des segments supplémentaires identifiant la région et la plateforme cloud), par exemple, xy12345.east-us-2.azure.<br/>- **Nom d’utilisateur :** ID de connexion de l’utilisateur pour la connexion.<br>- **Mot de passe :** Mot de passe de l’utilisateur.<br>- **Base de données :** base de données par défaut à utiliser une fois connecté. Il doit s’agir d’une base de données existante pour laquelle le rôle spécifié dispose de privilèges.<br>- **Entrepôt :** entrepôt virtuel à utiliser une fois connecté. Il doit s’agir d’un entrepôt existant pour lequel le rôle spécifié dispose de privilèges.<br>- **Rôle :** rôle de contrôle d’accès par défaut à utiliser dans la session Snowflake. Le rôle spécifié doit être un rôle existant qui a déjà été affecté à l’utilisateur spécifié. Le rôle par défaut est PUBLIC. | Oui      |
| connectVia       | Le [runtime d’intégration](concepts-integration-runtime.md) utilisé pour la connexion à la banque de données. Vous pouvez utiliser le runtime d’intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non       |

**Exemple :**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Mot de passe dans Azure Key Vault :**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). 

Les propriétés suivantes sont prises en charge pour le jeu de données Snowflake.

| Propriété  | Description                                                  | Obligatoire                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | La propriété type du jeu de données doit être définie sur **SnowflakeTable**. | Oui                         |
| schéma | Nom du schéma. |Non pour Source, Oui pour Récepteur  |
| table | Nom de la table/vue. |Non pour Source, Oui pour Récepteur  |

**Exemple :**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Snowflake en tant que source et récepteur.

### <a name="snowflake-as-the-source"></a>Snowflake en tant que source

Un connecteur Snowflake utilise la commande [COPY into [emplacement]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) de Snowflake pour obtenir les meilleures performances.

Si le magasin de données récepteur et le format sont pris en charge en mode natif par la commande COPY de Snowflake, vous pouvez utiliser l’activité de copie pour copier directement de Snowflake vers un récepteur. Pour plus d’informations, consultez [Copie directe à l’aide de Snowflake](#direct-copy-from-snowflake). Dans le cas contraire, utilisez la [copie intermédiaire intégrée à Snowflake](#staged-copy-from-snowflake).

Pour copier des données à partir de Snowflake, les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes.

| Propriété                     | Description                                                  | Obligatoire |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | La propriété type de la source de l’activité de copie doit être définie sur **SnowflakeSource**. | Oui      |
| query          | Spécifie la requête SQL pour lire les données de Snowflake.<br>L’exécution de la procédure stockée n'est pas prise en charge. | Non       |
| exportSettings | Paramètres avancés utilisés pour récupérer des données de Snowflake. Vous pouvez configurer pris en charge par la commande COPY into que Data Factory empruntera lorsque vous appelez l’instruction. | Non       |
| ***Sous `exportSettings`:*** |  |  |
| type | Type de commande d’exportation, défini sur **SnowflakeExportCopyCommand**. | Oui |
| additionalCopyOptions | Options de copie supplémentaires, fournies sous la forme d’un dictionnaire de paires clé-valeur. Exemples : MAX_FILE_SIZE, OVERWRITE. Pour plus d'informations, consultez [Options de copie de Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Non |
| additionalFormatOptions | Options de format de fichier supplémentaires, fournies à la commande COPY sous la forme d’un dictionnaire de paires clé-valeur. Exemples : DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Pour plus d’informations, consultez [Options de type de format de Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Non |

#### <a name="direct-copy-from-snowflake"></a>Copie directe à partir de Snowflake

Si le magasin de données récepteur et le format remplissent les critères décrits dans cette section, vous pouvez utiliser l’activité de copie pour effectuer une copie directe de Snowflake vers un récepteur. Data Factory vérifie les paramètres et fait échouer l’exécution de l’activité de copie si les critères suivants ne sont pas satisfaits :

- Le **service lié au récepteur** est le [**stockage d’objets blob Azure**](connector-azure-blob-storage.md) avec l’authentification par **signature d’accès partagé**.

- Le **format de données du récepteur** est **Parquet**, **Texte délimité** ou **JSON** avec les configurations suivantes :

    - Pour le format **Parquet**, le codec de compression est **Aucun**, **Snappy**ou **Lzo**.
    - Pour le format **texte délimité** :
        - `rowDelimiter` est **\r\n**, ou n’importe quel caractère unique.
        - `compression` peut être **aucune compression**, **gzip**, **bzip2**, ou **deflate**.
        - `encodingName` conserve sa valeur par défaut ou est défini sur **utf-8**.
        - `quoteChar` est un **guillemet double**, un **guillemet simple** ou une **chaîne vide** (sans guillemets).
    - Pour le format **JSON**, la copie directe ne prend en charge que le cas où la table Snowflake de la source ou le résultat de la requête ne possède qu’une seule colonne, dont les données sont de type **VARIANT**, **OBJET** ou **TABLEAU**.
        - `compression` peut être **aucune compression**, **gzip**, **bzip2**, ou **deflate**.
        - `encodingName` conserve sa valeur par défaut ou est défini sur **utf-8**.
        - `filePattern` dans le récepteur de l’activité de copie conserve sa valeur par défaut ou est défini sur **setOfObjects**.

- Dans la source de l’activité de copie, `additionalColumns` n’est pas spécifié.
- Le mappage de colonnes n’est pas spécifié.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Copie intermédiaire à partir de Snowflake

Lorsque le magasin de données récepteur ou le format n’est pas compatible en mode natif avec la commande COPY de Snowflake, comme indiqué dans la dernière section, activez la copie intermédiaire intégrée à l’aide d’une instance de stockage intermédiaire d’objets blob Azure. La fonctionnalité de copie intermédiaire offre également un meilleur débit. Data Factory exporte des données à partir de Snowflake dans le stockage intermédiaire, puis copie les données sur le récepteur et nettoie vos données temporaires sur le stockage intermédiaire. Pour plus d’informations sur la copie de données à l’aide de la mise en lots, consultez [Copie intermédiaire](copy-activity-performance-features.md#staged-copy).

Pour utiliser cette fonctionnalité, créez un [service lié au Stockage Blob Azure](connector-azure-blob-storage.md#linked-service-properties) qui fait référence au compte de stockage Azure en tant qu’intermédiaire. Spécifiez ensuite les propriétés `enableStaging` et `stagingSettings` dans l’activité de copie.

> [!NOTE]
> Le service lié au Stockage Blob Azure intermédiaire doit utiliser l’authentification par signature d’accès partagé, comme exigé par la commande COPY de Snowflake. 

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Snowflake en tant que récepteur

Un connecteur Snowflake utilise la commande [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) de Snowflake pour obtenir les meilleures performances. Il prend en charge l’écriture de données vers Snowflake sur Azure.

Si le magasin de données source et le format sont pris en charge en mode natif par la commande COPY de Snowflake, vous pouvez utiliser l’activité de copie pour copier directement d’une source vers Snowflake. Pour plus d’informations, consultez [Copie directe vers Snowflake](#direct-copy-to-snowflake). Dans le cas contraire, utilisez la [copie intermédiaire intégrée vers Snowflake](#staged-copy-to-snowflake).

Pour copier des données dans Snowflake, les propriétés suivantes sont prises en charge dans la section **récepteur** de l’activité de copie.

| Propriété          | Description                                                  | Obligatoire                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | La propriété type du récepteur de l'activité de copie, définie sur **SnowflakeSink**. | Oui                                           |
| preCopyScript     | Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans Snowflake à chaque exécution. Utilisez cette propriété pour nettoyer les données préchargées. | Non                                            |
| importSettings | Paramètres avancés utilisés pour écrire des données dans Snowflake. Vous pouvez configurer pris en charge par la commande COPY into que Data Factory empruntera lorsque vous appelez l’instruction. | Non |
| ***Sous `importSettings`:*** |                                                              |  |
| type | Type de commande d’importation, défini sur **SnowflakeImportCopyCommand**. | Oui |
| additionalCopyOptions | Options de copie supplémentaires, fournies sous la forme d’un dictionnaire de paires clé-valeur. Exemples : ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Pour plus d'informations, consultez [Options de copie de Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Non |
| additionalFormatOptions | Options de format de fichier supplémentaires, fournies à la commande COPY sous la forme d’un dictionnaire de paires clé-valeur. Exemples : DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Pour plus d’informations, consultez [Options de type de format de Snowflake](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Non |

#### <a name="direct-copy-to-snowflake"></a>Copie directe vers Snowflake

Si le magasin de données source et le format remplissent les critères décrits dans cette section, vous pouvez utiliser l’activité de copie pour effectuer une copie directe depuis une source vers Snowflake. Azure Data Factory vérifie les paramètres et fait échouer l’exécution de l’activité de copie si les critères suivants ne sont pas satisfaits :

- Le **service lié à la source** est le [**stockage d’objets blob Azure**](connector-azure-blob-storage.md) avec l’authentification par **signature d’accès partagé**.

- Le **format de données de la source** est **Parquet**, **Texte délimité** ou **JSON** avec les configurations suivantes :

    - Pour le format **Parquet**, le codec de compression est **Aucun** ou **Snappy**.

    - Pour le format **texte délimité** :
        - `rowDelimiter` est **\r\n**, ou n’importe quel caractère unique. Si le délimiteur de ligne n’est pas « \r\n », `firstRowAsHeader` doit être **false**et `skipLineCount` n’est pas spécifié.
        - `compression` peut être **aucune compression**, **gzip**, **bzip2**, ou **deflate**.
        - `encodingName` est conservé comme valeur par défaut ou défini sur "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255".
        - `quoteChar` est un **guillemet double**, un **guillemet simple** ou une **chaîne vide** (sans guillemets).
    - Pour le format **JSON**, la copie directe ne prend en charge que le cas où la table Snowflake du récepteur ne possède qu’une seule colonne, dont les données sont de type **VARIANT**, **OBJET** ou **TABLEAU**.
        - `compression` peut être **aucune compression**, **gzip**, **bzip2**, ou **deflate**.
        - `encodingName` conserve sa valeur par défaut ou est défini sur **utf-8**.
        - Le mappage de colonnes n’est pas spécifié.

- Dans la source de l’activité de copie : 

   -  `additionalColumns` n'est pas spécifié.
   - Si votre source est un dossier, `recursive` a la valeur true.
   - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` ne sont pas spécifiés.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Copie intermédiaire vers Snowflake

Lorsque le magasin de données récepteur ou le format n’est pas compatible en mode natif avec la commande COPY de Snowflake, comme indiqué dans la dernière section, activez la copie intermédiaire intégrée à l’aide d’une instance de stockage intermédiaire d’objets blob Azure. La fonctionnalité de copie intermédiaire offre également un meilleur débit. Data Factory convertit automatiquement les données pour répondre aux exigences de format de données de Snowflake. Il appelle ensuite la commande COPY pour charger les données dans Snowflake. Pour finir, il nettoie vos données temporaires du stockage Blob. Pour plus d’informations sur la copie de données à l’aide de la mise en lots, consultez [Copie intermédiaire](copy-activity-performance-features.md#staged-copy).

Pour utiliser cette fonctionnalité, créez un [service lié au Stockage Blob Azure](connector-azure-blob-storage.md#linked-service-properties) qui fait référence au compte de stockage Azure en tant qu’intermédiaire. Spécifiez ensuite les propriétés `enableStaging` et `stagingSettings` dans l’activité de copie.

> [!NOTE]
> Le service lié au Stockage Blob Azure intermédiaire doit utiliser l’authentification par signature d’accès partagé, comme exigé par la commande COPY de Snowflake.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour plus d’informations sur les propriétés, consultez [Activité de recherche](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
