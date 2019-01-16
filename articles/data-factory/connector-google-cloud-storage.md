---
title: Copier des données à partir de Google Cloud Storage avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de Google Cloud Storage vers des banques de données réceptrices prises en charge.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: b829528daeeb49be9b46206d8ae0add88c4441bd
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54068937"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copier des données à partir de Google Cloud Storage avec Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Google Cloud Storage. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données Google Cloud Storage vers n’importe quelle banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus spécifiquement, ce connecteur Google Cloud Storage prend en charge la copie de fichiers en l'état ou l’analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Copier des données à partir de Google Cloud Storage exploite le [connecteur Amazon S3](connector-amazon-simple-storage-service.md) avec le point de terminaison personnalisé S3 correspondant, car Google Cloud Storage offre une interopérabilité compatible S3.

## <a name="required-permissions"></a>Autorisations requises

Pour copier des données à partir de Google Cloud Storage, assurez-vous que vous disposez des autorisations suivantes :

- **Pour l’exécution de l’activité de copie :** : `s3:GetObject` et `s3:GetObjectVersion` pour les opérations d’objet.
- **Lorsque vous utilisez l’interface utilisateur graphique de Data Factory pour la création** : les autorisations `s3:ListAllMyBuckets` et `s3:ListBucket`/`s3:GetBucketLocation` relatives aux opérations de compartiment sont également requises pour les opérations telles que « tester la connexion » et « parcourir/accéder à des chemins de fichiers ». Si vous ne souhaitez pas accorder ces autorisations, ignorez « tester la connexion » dans la page de création de service lié et spécifiez directement le chemin d’accès dans les paramètres du jeu de données.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Google Cloud Storage sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AmazonS3**. | OUI |
| accessKeyId | ID de la clé d’accès secrète. Pour trouver la clé d’accès et le secret, accédez à **Google Cloud Storage** > **Paramètres** > **Interopérabilité**. |Oui |
| secretAccessKey | La clé d’accès secrète elle-même. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| serviceUrl | Spécifiez le point de terminaison personnalisé S3 en tant que **`https://storage.googleapis.com`**. | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

Voici un exemple : 

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données de Google Cloud Storage.

Pour copier des données de Google Cloud Storage, affectez la valeur **AmazonS3Object** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **AmazonS3Object** |Oui |
| bucketName | Le nom de compartiment S3. Le filtre de caractères génériques n'est pas pris en charge. |Oui pour la copie/Activité Lookup/Non pour l’activité GetMetadata |
| key | Le **filtre de nom ou de caractères génériques** de la clé d’objet S3 sous le compartiment spécifié. S’applique uniquement lorsque la propriété « prefix » n’est pas spécifiée. <br/><br/>Le filtre de caractères génériques est uniquement pris en charge pour la partie du nom de fichier et non pour le chemin de dossier. Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"key": "rootfolder/subfolder/*.csv"`<br/>- Exemple 2 : `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement. |Non  |
| prefix | Préfixe de la clé d’objet S3. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la propriété « key » n’est pas spécifiée. |Non  |
| version | La version de l’objet S3 si le contrôle de version S3 est activé. |Non  |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non  |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **bucketName** pour le compartiment et **prefix** pour la partie dossier.<br>Pour copier un seul fichier avec un nom donné, spécifiez **bucketName** pour le compartiment et **key** pour la partie dossier plus le nom de fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **bucketName** pour le compartiment et **key** pour la partie dossier plus le filtre de caractères génériques.

**Exemple : utilisation de préfixe**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage comme source

Pour copier des données depuis Google Cloud Storage, définissez **FileSystemSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur : **FileSystemSource** |Oui |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
