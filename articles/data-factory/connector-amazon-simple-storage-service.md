---
title: Copier des données depuis Amazon Simple Storage Service (S3)
description: Découvrez comment utiliser Azure Data Factory pour copier des données d’Amazon Simple Storage Service (S3) vers des banques de données réceptrices prises en charge.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 6975c876bb31e36b7b6268d284f747fd51803d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415467"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copie de données d’Amazon Simple Storage Service à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Version actuelle](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment copier des données depuis Amazon Simple Storage Service (Amazon S3). Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

>[!TIP]
>Pour le scénario de migration de données d’Amazon S3 vers le stockage Azure, consultez [Utiliser Azure Data Factory pour migrer des données d’Amazon S3 vers le stockage Azure](data-migration-guidance-s3-azure-storage.md) pour en savoir plus.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Amazon S3 est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)

Plus spécifiquement, ce connecteur Amazon S3 prend en charge la copie de fichiers en l'état ou l’analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md). Vous pouvez également choisir de [conserver les métadonnées de fichier lors de la copie](#preserve-metadata-during-copy). Le connecteur utilise [AWS Signature version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) pour authentifier les demandes auprès d’Amazon S3.

>[!TIP]
>Vous pouvez utiliser ce connecteur Amazon S3 pour copier des données à partir de **n’importe quel fournisseur de stockage compatible S3**, par exemple [Google Cloud Storage](connector-google-cloud-storage.md). Spécifiez l’URL du service correspondant dans la configuration de service lié.

## <a name="required-permissions"></a>Autorisations requises

Pour copier des données à partir d’Amazon S3, assurez-vous que vous disposez des autorisations suivantes :

- **Pour l’exécution de l’activité de copie :** : `s3:GetObject` et `s3:GetObjectVersion` pour les opérations d’objet Amazon S3.
- **Lorsque vous utilisez l’interface utilisateur graphique de Data Factory pour la création** : les autorisations `s3:ListAllMyBuckets` et `s3:ListBucket`/`s3:GetBucketLocation` relatives aux opérations de compartiment Amazon S3 sont également requises pour les opérations telles que « tester la connexion » et « parcourir/accéder à des chemins de fichiers ». Si vous ne souhaitez pas accorder ces autorisations, ignorez « tester la connexion » sur la page de création de service lié et spécifiez directement le chemin d'accès dans les paramètres du jeu de données.

Pour obtenir la liste complète des autorisations Amazon S3, consultez l’article [Spécification des autorisations d’une stratégie](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques d’Amazon S3.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Amazon S3 prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AmazonS3**. | Oui |
| accessKeyId | ID de la clé d’accès secrète. |Oui |
| secretAccessKey | La clé d’accès secrète elle-même. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| serviceUrl | Spécifiez le point de terminaison S3 personnalisé si vous copiez des données à partir d’un fournisseur de stockage compatible S3 autre que le service Amazon S3 officiel. Par exemple, pour copier des données à partir de Google Cloud Storage, spécifiez `https://storage.googleapis.com`. | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

>[!TIP]
>Spécifiez l’URL du service S3 personnalisé si vous copiez des données à partir d’un fournisseur de stockage compatible S3 autre que le service Amazon S3 officiel.

>[!NOTE]
>Ce connecteur nécessite des clés d’accès pour le compte IAM pour copier des données à partir d’Amazon S3. Les [informations d’identification de sécurité temporaires](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) ne sont pas prises en charge.
>

Voici un exemple :

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour les objets Amazon S3 sous les paramètres `location` dans le jeu de données basé sur le format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **AmazonS3Location**. | Oui      |
| bucketName | Le nom de compartiment S3.                                          | Oui      |
| folderPath | Le chemin d’accès au dossier sous le compartiment donné. Si vous souhaitez utiliser un caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Le nom de fichier sous le compartiment et le chemin d’accès folderPath donnés. Si vous souhaitez utiliser un caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| version | La version de l’objet S3 si le contrôle de version S3 est activé. La version la plus récente sera extraite si vous n’avez rien spécifié. |Non |

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 en tant que source

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour les objets Amazon S3 sous les paramètres `storeSettings` dans la source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **AmazonS3ReadSettings**. | Oui                                                         |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                                          |
| prefix                   | Le préfixe pour la clé d’objet S3 sous le compartiment donné configuré dans le jeu de données pour filtrer les objets sources. Les objets dont les clés commencent par ce préfixe sont sélectionnés. <br>S’applique uniquement lorsque les propriétés `wildcardFolderPath` et `wildcardFileName` ne sont pas spécifiées. | Non                                                          |
| wildcardFolderPath       | Le chemin d’accès du dossier avec des caractères génériques sous le compartiment donné configuré dans le jeu de données pour filtrer les dossiers sources. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                                          |
| wildcardFileName         | Le nom du fichier avec des caractères génériques situé sous le compartiment et le chemin d’accès folderPath/wildcardFolderPath donnés pour filtrer les fichiers sources. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` dans le jeu de données et `prefix` ne sont pas spécifiés |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                                          |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                                          |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                                          |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemples de filtres de dossier et de fichier

Cette section décrit le comportement résultant de l’utilisation de filtres de caractères génériques dans les noms de fichier et les chemins de dossier.

| Compartiment | key | recursive | Structure du dossier source et résultat du filtrage (les fichiers en gras sont récupérés)|
|:--- |:--- |:--- |:--- |
| Compartiment | `Folder*/*` | false | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| Compartiment | `Folder*/*` | true | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| Compartiment | `Folder*/*.csv` | false | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| Compartiment | `Folder*/*.csv` | true | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |

## <a name="preserve-metadata-during-copy"></a>Conserver les métadonnées lors de la copie

Lorsque vous copiez des fichiers depuis Amazon S3 vers Azure Data Lake Storage Gen2/Azure Blob, vous pouvez choisir de conserver les métadonnées des fichiers avec les données. Pour plus d’informations, consultez [Conserver les métadonnées](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriétés de l’activité GetMetadata

Pour en savoir plus sur les propriétés, consultez [Activité GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propriétés de l’activité Delete

Pour en savoir plus sur les propriétés, consultez [Activité Delete](delete-activity.md).

## <a name="legacy-models"></a>Modèles hérités

>[!NOTE]
>Les Modèles suivants sont toujours pris en charge tels quels à des fins de compatibilité descendante. Il est recommandé d’utiliser le nouveau Modèle mentionné dans les sections ci-dessus à partir de maintenant. L’interface utilisateur de création ADF peut désormais générer ce nouveau Modèle.

### <a name="legacy-dataset-model"></a>Modèle de jeu de données hérité

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **AmazonS3Object** |Oui |
| bucketName | Le nom de compartiment S3. Le filtre de caractères génériques n'est pas pris en charge. |Oui pour la copie/Activité Lookup/Non pour l’activité GetMetadata |
| key | Le **filtre de nom ou de caractères génériques** de la clé d’objet S3 sous le compartiment spécifié. S’applique uniquement lorsque la propriété « prefix » n’est pas spécifiée. <br/><br/>Le filtre de caractères génériques est pris en charge pour la partie du dossier et la partie du nom de fichier. Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"key": "rootfolder/subfolder/*.csv"`<br/>- Exemple 2 : `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). Utilisez `^` comme caractère d'échappement si votre nom de dossier/fichier réel contient des caractères génériques ou ce caractère d'échappement. |Non |
| prefix | Préfixe de la clé d’objet S3. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la propriété « key » n’est pas spécifiée. |Non |
| version | La version de l’objet S3 si le contrôle de version S3 est activé. La version la plus récente sera extraite si vous n’avez rien spécifié. |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Sachez que les performances globales du déplacement des données sont affectées par l’activation de ce paramètre lorsque vous souhaitez filtrer des fichiers parmi de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Sachez que les performances globales du déplacement des données sont affectées par l’activation de ce paramètre lorsque vous souhaitez filtrer des fichiers parmi de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [format Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **bucketName** pour le compartiment et **prefix** pour la partie dossier.<br>Pour copier un seul fichier avec un nom donné, spécifiez **bucketName** pour le compartiment et **key** pour la partie dossier plus le nom de fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **bucketName** pour le compartiment et **key** pour la partie dossier plus le filtre de caractères génériques.

**Exemple : utilisation de préfixe**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
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

**Exemple : utilisation de clé et version (facultative)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-copy-activity-source-model"></a>Modèle hérité de la source d’activité de copie

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **FileSystemSource** |Oui |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
