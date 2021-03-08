---
title: Activité d’obtention des métadonnées dans Azure Data Factory
description: Découvrez comment utiliser l’activité d’obtention des métadonnées dans un pipeline Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: jingwang
ms.openlocfilehash: 91cb10d601f0a44cf9895fffe558c03fdbe06eef
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710224"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Activité d’obtention des métadonnées dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez utiliser l’activité d’obtention des métadonnées pour récupérer les métadonnées de n’importe quelle donnée dans Azure Data Factory. Vous pouvez utiliser la sortie de l’activité d’obtention des métadonnées dans des expressions conditionnelles pour effectuer la validation, ou consommer les métadonnées dans des activités subséquentes.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

L’activité d’obtention des métadonnées sélectionne un jeu de données en tant qu’entrée et retourne les informations de métadonnées en tant que sortie. Actuellement, les connecteurs suivants et les métadonnées récupérables correspondantes sont pris en charge. La taille maximale des métadonnées retournées est de **4 Mo**.

### <a name="supported-connectors"></a>Connecteurs pris en charge

**Stockage Fichier**

| Connecteur/Métadonnées | itemName<br>(fichier/dossier) | itemType<br>(fichier/dossier) | taille<br>(fichier) | created<br>(fichier/dossier) | lastModified<sup>1</sup><br>(fichier/dossier) |childItems<br>(dossier) |contentMD5<br>(fichier) | structure<sup>2</sup><br/>(fichier) | columnCount<sup>2</sup><br>(fichier) | exists<sup>3</sup><br>(fichier/dossier) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [stockage d’objets blob Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Système de fichiers](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> Métadonnées `lastModified` :
- Pour Amazon S3 et Google Cloud Storage, `lastModified` s’applique au compartiment et à la clé, mais pas au dossier virtuel, et `exists` s’applique au compartiment et à la clé, mais pas au préfixe ou au dossier virtuel. 
- Pour le stockage Blob Azure, `lastModified` s’applique au conteneur et au blob, mais pas au dossier virtuel.

<sup>2</sup> Les métadonnées `structure` et `columnCount` ne sont pas pris en charge lors de l’obtention de métadonnées à partir de fichiers binaires, JSON ou XML.

<sup>3</sup> Métadonnées `exists` : pour Amazon S3 et Google Cloud Storage, `exists` s’applique au compartiment et à la clé, mais pas au préfixe ou au dossier virtuel.

Notez les points suivants :

- Lorsque vous utilisez l’activité d’obtention des métadonnées sur un dossier, vérifiez que vous disposez de l’autorisation LIST/EXECUTE sur le dossier donné.
- Le filtre de caractères génériques sur des dossiers/fichiers n’est pas pris en charge pour une activité Obtenir des métadonnées.
- Filtres `modifiedDatetimeStart` et `modifiedDatetimeEnd` définis sur le connecteur :

    - Ces deux propriétés sont utilisées pour filtrer les éléments enfants lors de l’obtention de métadonnées à partir d’un dossier. Cela ne s’applique pas lors de l’obtention de métadonnées à partir d’un fichier.
    - Quand un tel filtre est utilisé, les `childItems` dans la sortie comprennent uniquement les fichiers modifiés dans la plage spécifiée, pas dans les dossiers.
    - Pour appliquer un tel filtre, l’activité GetMetadata énumère tous les fichiers dans le dossier spécifié, et vérifie l’heure de modification. Évitez de pointer vers un dossier contenant un grand nombre de fichiers, même si le nombre de fichiers qualifiés attendu est faible. 

**Base de données relationnelle**

| Connecteur/Métadonnées | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Options de métadonnées

Vous pouvez spécifier les types de métadonnées suivants dans la liste de champs de l’activité d’obtention des métadonnées pour récupérer les informations correspondantes :

| Type de métadonnées | Description |
|:--- |:--- |
| itemName | Nom du fichier ou dossier. |
| itemType | Type du fichier ou dossier. La valeur retournée est `File` ou `Folder`. |
| taille | Taille du fichier en octets. S’applique aux fichiers uniquement. |
| created | Date/heure de création du fichier ou du dossier. |
| lastModified | Date/heure de dernière modification du fichier ou du dossier. |
| childItems | Liste des sous-dossiers et fichiers dans le dossier concerné. S’applique aux dossiers uniquement. La valeur retournée est une liste contenant le nom et le type de chaque élément enfant. |
| contentMD5 | MD5 du fichier. S’applique aux fichiers uniquement. |
| structure | Structure de données du fichier ou de la table de base de données relationnelle. La valeur retournée est une liste de noms et de types de colonnes. |
| columnCount | Nombre de colonnes dans le fichier ou la table relationnelle. |
| exists| Indique si un fichier, un dossier ou une table existe ou non. Si `exists` est spécifié dans la liste de champs de l’activité d’obtention des métadonnées, l’activité n’échouera pas, même si le fichier, le dossier ou la table n’existe pas. `exists: false` sera alors retourné dans la sortie. |

>[!TIP]
>Si vous souhaitez vérifier qu’un fichier, un dossier ou une table existe, spécifiez `exists` dans la liste de champs de l’activité d’obtention des métadonnées. Vous pouvez ensuite vérifier le résultat de `exists: true/false` dans la sortie de l’activité. Si `exists` n’est pas spécifié dans la liste de champs, l’activité d’obtention des métadonnées échouera si l’objet est introuvable.

## <a name="syntax"></a>Syntaxe

**Activité d’obtention des métadonnées**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Dataset**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriétés type

Actuellement, l’activité d’obtention des métadonnées peut retourner les types d’informations de métadonnées suivants :

Propriété | Description | Obligatoire
-------- | ----------- | --------
fieldList | Les types d’informations de métadonnées requis. Pour plus d’informations sur les métadonnées prises en charge, consultez la section [Options de métadonnées](#metadata-options) de cet article. | Oui 
dataset | Le jeu de données de référence à partir duquel les métadonnées doivent être récupérées par l’activité d’obtention des métadonnées. Pour plus d’informations sur les connecteurs pris en charge, consultez la section [Fonctionnalités](#capabilities). Reportez-vous aux rubriques spécifiques aux connecteurs pour plus d’informations sur la syntaxe de jeu de données. | Oui
formatSettings | S’applique lors de l’utilisation du type de format Jeu de données. | Non
storeSettings | S’applique lors de l’utilisation du type de format Jeu de données. | Non

## <a name="sample-output"></a>Exemple de sortie

Les résultats de l’activité d’obtention des métadonnées sont indiqués dans la sortie de l’activité. Voici deux exemples illustrant les nombreuses options de métadonnées. Pour utiliser le résultat dans une activité suivante, utilisez le modèle `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Obtenir les métadonnées d’un fichier

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obtenir les métadonnées d’un dossier

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les ressources suivantes pour en savoir plus sur les autres activités de flux de contrôle prises en charge par Data Factory :

- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Activité ForEach](control-flow-for-each-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
