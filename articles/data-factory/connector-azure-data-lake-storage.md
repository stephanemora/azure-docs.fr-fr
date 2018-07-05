---
title: Copier des données vers ou depuis Azure Data Lake Storage Gen2 Preview avec Data Factory (Preview) | Microsoft Docs
description: Découvrez comment copier des données vers et depuis Azure Data Lake Storage Gen2 Preview avec Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: jingwang
ms.openlocfilehash: 110bfe4b98045149bb52af2ad6f1156ea6d4018d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034818"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-preview-using-azure-data-factory-preview"></a>Copier des données vers ou depuis Azure Data Lake Storage Gen2 Preview avec Azure Data Factory (Preview)

[Azure Data Lake Storage Gen2 Preview](../storage/data-lake-storage/introduction.md) est le service de stockage hyperscale de Microsoft conçu pour les charges de travail analytiques du Big Data. Il vous permet d’interagir avec vos données selon les deux paradigmes que sont le système de fichiers et le stockage d’objets. Azure Data Lake Storage est donc le seul service de stockage multimodal cloud qui vous permet d’extraire de précieuses données d’analytique de toutes vos données. Vous pouvez vous [inscrire](https://aka.ms/adlsgen2signup) à la préversion publique d’Azure Data Lake Storage Gen2.

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis Data Lake Storage Gen2. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données depuis n’importe quelle banque de données sources prise en charge vers Data Lake Storage Gen2. Vous pouvez également copier des données depuis Data Lake Storage Gen2 vers n’importe quelle banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md).

Plus précisément, ce connecteur prend en charge ce qui suit :

- Copie des données en utilisant une clé de compte.
- Copie de fichiers tels quels, ou analyse ou génération de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Prise en main

>[!TIP]
>Pour une procédure pas à pas d’utilisation du connecteur Data Lake Storage Gen2, consultez [Charger des données dans Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques à Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Data Lake Storage Gen2 :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **AzureBlobFS**. |OUI |
| url | Point de terminaison pour Data Lake Storage Gen2 avec le modèle de `https://<accountname>.dfs.core.windows.net`. | OUI | 
| accountKey | Clé de compte pour le service Data Lake Storage Gen2. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

**Exemple :**

```json
{
    "name": "AzureDataLakeStorageLinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Les propriétés suivantes sont prises en charge pour le jeu de données Azure Data Lake Storage :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **AzureBlobFSFile**. |OUI |
| folderPath | Chemin vers le dossier dans Data Lake Storage Gen2. Le filtre de caractères génériques n'est pas pris en charge. Exemple : dossier_racine/sous-dossier/. |OUI |
| fileName | **Filtre de nom ou de caractère générique** pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement.<br/><br/>Quand fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur de l’activité, l’activité de copie génère automatiquement le nom de fichier avec le modèle suivant : « *Data.[GUID d’id d’exécution d’activité].[GUID si FlattenHierarchy].[format si configuré].[compression si configurée]* ». Par exemple, « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». |Non  |
| format | Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers d’un format spécifique, les types de formats de fichier pris en charge sont les suivants : **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous **Format** sur l’une de ces valeurs. Pour en savoir plus, voir les sections [Format Text](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non  |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul fichier avec un nom donné, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le filtre de caractères génériques. 

**Exemple :**

```json
{
    "name": "AzureDataLakeStorageDataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez les articles [Configurations des activités de copie](copy-activity-overview.md#configuration) et [Pipelines et activités](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 comme type de source

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **AzureBlobFSSource**. |OUI |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur.<br/>Les valeurs autorisées sont **true** (par défaut) et **false**. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAzureDataLakeStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 comme type de récepteur

Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du récepteur d’activité de copie doit être définie sur **AzureBlobFSSink**. |OUI |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un même fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToAzureDataLakeStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>Quelques exemples de valeurs recursive et copyBehavior

Cette section décrit le comportement résultant de l’opération de copie pour différentes combinaisons de valeurs recursive et copyBehavior.

| recursive | copyBehavior | Structure du dossier source | Cible obtenue |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré de la même manière que la source :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un fichier avec un nom de fichier généré automatiquement. |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec un nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
