---
title: Copie des données à partir d’Amazon Simple Storage Service (S3) Compatible Storage
description: Découvrez comment copier des données d’Amazon Simple Storage Service (S3) vers des magasins de données récepteurs pris en charge avec Azure Data Factory.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/11/2021
ms.openlocfilehash: 96b8ac29fadc03ca6e5b374acc269d5e19210b03
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103927"
---
# <a name="copy-data-from-amazon-s3-compatible-storage-by-using-azure-data-factory"></a>Copie de données à partir d’Amazon S3 Compatible Storage avec Azure Data Factory


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment copier des données à partir d’Amazon Simple Storage Service (Amazon S3) Compatible Storage. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).



## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Amazon S3 Compatible Storage est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)

Plus spécifiquement, ce connecteur Amazon S3 Compatible Storage prend en charge la copie de fichiers en l’état ou l’analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md). Le connecteur utilise [AWS Signature version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) pour authentifier les demandes auprès d’Amazon S3. Vous pouvez utiliser ce connecteur Amazon S3 Compatible Storage pour copier des données à partir de n’importe quel fournisseur de stockage compatible S3. Spécifiez l’URL du service correspondant dans la configuration de service lié.



## <a name="required-permissions"></a>Autorisations requises

Pour copier des données à partir d’Amazon S3 Compatible Storage, vérifiez que vous disposez des autorisations `s3:GetObject` et `s3:GetObjectVersion` pour les opérations d’objet Amazon S3.

Si vous utilisez l’interface utilisateur de Data Factory pour créer, sachez que des autorisations `s3:ListAllMyBuckets` et `s3:ListBucket`/`s3:GetBucketLocation` supplémentaires sont nécessaires pour les opérations telles que le test de connexion au service lié et la navigation à partir de la racine. Si vous ne souhaitez pas accorder ces autorisations, vous pouvez choisir les options « Test connection to file path » (« Tester la connexion au chemin du fichier ») ou « Browse from specified path » («Parcourir à partir du chemin spécifié ») dans interface utilisateur.

Pour obtenir la liste complète des autorisations Amazon S3, consultez l’article [Spécification des autorisations d’une stratégie](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) sur le site AWS.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)] 

Les sections suivantes donnent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques d’Amazon S3 Compatible Storage.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour un service lié Amazon S3 Compatible prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** doit être définie sur **AmazonS3Compatible**. | Oui |
| accessKeyId | ID de la clé d’accès secrète. |Oui |
| secretAccessKey | La clé d’accès secrète elle-même. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| serviceUrl | Spécifiez le point de terminaison S3 personnalisé `https://<service url>`. | Non |
| forcePathStyle | Indique s’il faut utiliser un [accès de type chemin d’accès](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) S3 au lieu de l’accès de type hébergement virtuel. Les valeurs autorisées sont : **false** (par défaut), **true**.<br> Consultez la documentation de chaque magasin de données pour vérifier si l’accès de type chemin d’accès est nécessaire ou non. |Non |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve sur un réseau privé). Si cette propriété n’est pas spécifiée, le service utilise le runtime d’intégration Azure par défaut. |Non |



**Exemple :**

```json
{
    "name": "AmazonS3CompatibleLinkedService",
    "properties": {
        "type": "AmazonS3Compatible",
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

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Les propriétés prises en charge pour Amazon S3 Compatible Storage sous les paramètres `location` dans un jeu de données basé sur le format sont les suivantes :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété **type** indiquée sous `location` dans un jeu de données doit être définie sur **AmazonS3CompatibleLocation**. | Oui      |
| bucketName | Nom du compartiment S3 Compatible Storage.                                          | Oui      |
| folderPath | Chemin d’accès au dossier sous le compartiment donné. Si vous souhaitez utiliser un caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Nom de fichier sous le compartiment et le chemin d’accès du dossier donnés. Si vous souhaitez utiliser un caractère générique pour filtrer des dossiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| version | Version de l’objet S3 Compatible Storage, si le contrôle de version S3 Compatible Storage est activé. À défaut de spécification, la version la plus récente est extraite. |Non |

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 Compatible Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3CompatibleLocation",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par la source Amazon S3 Compatible Storage.

### <a name="amazon-s3-compatible-storage-as-a-source-type"></a>Amazon S3 Compatible Storage comme type source

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Les propriétés prises en charge pour Amazon S3 Compatible Storage sous les paramètres `storeSettings` dans une source de copie basée sur le format sont les suivantes :

| Propriété                 | Description                                                  | Obligatoire                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | La propriété **type** indiquée sous `storeSettings` doit être définie sur **AmazonS3CompatibleReadSettings**. | Oui                                                         |
| ***Recherchez les fichiers à copier :*** |  |  |
| OPTION 1 : chemin d’accès statique<br> | Copie à partir du compartiment donné ou du chemin d’accès au dossier/fichier spécifié dans le jeu de données. Si vous souhaitez copier tous les fichiers d’un compartiment ou dossier, spécifiez en plus `wildcardFileName` comme `*`. |  |
| OPTION 2 : préfixe S3 Compatible Storage<br>- prefix | Préfixe du nom de la clé S3 Compatible Storage sous le compartiment donné configuré dans un jeu de données pour filtrer les fichiers S3 Compatible Storage sources. Les clés sélectionnées sont les clés S3 Compatible Storage dont le nom commence par `bucket_in_dataset/this_prefix`. Elles utilisent le filtre côté service de S3 Compatible Storage, qui offre un meilleur niveau de performance qu’un filtre par caractères génériques.<br/><br/>Quand vous utilisez le préfixe et que vous choisissez de copier le récepteur basé sur un fichier avec conservation de la hiérarchie, notez que le sous-chemin après le dernier signe « / » dans le préfixe est conservé. Par exemple, si vous avez la source `bucket/folder/subfolder/file.txt` et que vous configurez le préfixe sous la forme `folder/sub`, le chemin du fichier conservé est `subfolder/file.txt`. | Non |
| OPTION 3 : caractère générique<br>- wildcardFolderPath | Chemin d’accès du dossier avec des caractères génériques sous le compartiment donné configuré dans le jeu de données pour filtrer les dossiers sources. <br>Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom de votre dossier contient un caractère générique ou ce caractère d’échappement. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                            |
| OPTION 3 : caractère générique<br>- wildcardFileName | Nom de fichier avec caractères génériques sous le compartiment et le chemin d’accès du dossier donnés (ou chemin d’accès du dossier en caractères génériques) pour filtrer les fichiers sources. <br>Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom de votre fichier contient un caractère générique ou ce caractère d’échappement.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui |
| OPTION 4 : liste de fichiers<br>- fileListPath | Indique de copier un ensemble de fichiers donné. Pointez vers un fichier texte contenant la liste des fichiers que vous voulez copier, un fichier par ligne indiquant le chemin d’accès relatif configuré dans le jeu de données.<br/>Lorsque vous utilisez cette option, ne spécifiez pas de nom de fichier dans le jeu de données. Pour plus d’exemples, consultez [Exemples de listes de fichiers](#file-list-examples). |Non |
| ***Paramètres supplémentaires :*** |  | |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option **recursive** est définie sur **true** et que le récepteur est un magasin basé sur un fichier, un dossier ou un sous-dossier vide n’est pas copié ou créé sur le récepteur. <br>Les valeurs autorisées sont **true** (par défaut) et **false**.<br>Cette propriété ne s’applique pas lorsque vous configurez `fileListPath`. |Non |
| deleteFilesAfterCompletion | Indique si les fichiers binaires seront supprimés du magasin source après leur déplacement vers le magasin de destination. La suppression se faisant par fichier, lorsque l’activité de copie échoue, vous pouvez constater que certains fichiers ont déjà été copiés vers la destination et supprimés de la source, tandis que d’autres restent dans le magasin source. <br/>Cette propriété est valide uniquement dans un scénario de copie de fichiers binaires. La valeur par défaut est false. |Non |
| modifiedDatetimeStart    | Les fichiers sont filtrés en fonction de l’attribut de dernière modification. <br>Les fichiers seront sélectionnés si l’heure de leur dernière modification d’inscrit dans l’intervalle de temps compris entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée à un fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent avoir la valeur **NULL**, ce qui a pour effet qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Quand `modifiedDatetimeStart` a une valeur de DateHeure, mais que la valeur de `modifiedDatetimeEnd` est **NULL**, les fichiers dont l’attribut de dernière modification a une valeur supérieure ou égale à la valeur de DateHeure sont sélectionnés.  Quand `modifiedDatetimeEnd` a une valeur de DateHeure, mais que la valeur de `modifiedDatetimeStart` est **NULL**, les fichiers dont l’attribut de dernière modification a une valeur inférieure à la valeur de DateHeure sont sélectionnés.<br/>Cette propriété ne s’applique pas lorsque vous configurez `fileListPath`. | Non                                            |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                                          |
| enablePartitionDiscovery | Pour les fichiers partitionnés, spécifiez s’il faut analyser les partitions à partir du chemin d’accès au fichier et les ajouter en tant que colonnes sources supplémentaires.<br/>Les valeurs autorisées sont **false** (par défaut) et **true**. | Non                                            |
| partitionRootPath | Lorsque la découverte de partition est activée, spécifiez le chemin d’accès racine absolu pour pouvoir lire les dossiers partitionnés en tant que colonnes de données.<br/><br/>S’il n’est pas spécifié, par défaut :<br/>– Quand vous utilisez le chemin d’accès du fichier dans le jeu de données ou la liste des fichiers sur la source, le chemin racine de la partition est le chemin d’accès configuré dans le jeu de données.<br/>– Quand vous utilisez le filtre de dossiers de caractères génériques, le chemin d’accès racine de la partition est le sous-chemin d’accès avant le premier caractère générique.<br/>– Quand vous utilisez le préfixe, le chemin d’accès racine de la partition est le sous-chemin d’accès avant le dernier « / ». <br/><br/>Par exemple, en supposant que vous configurez le chemin d’accès dans le jeu de données en tant que « root/folder/year=2020/month=08/day=27 » :<br/>– Si vous spécifiez le chemin d’accès racine de la partition en tant que « root/folder/year=2020 », l’activité de copie génère deux colonnes supplémentaires, `month` et `day`, ayant respectivement la valeur « 08 » et « 27 », en plus des colonnes contenues dans les fichiers.<br/>– Si le chemin d’accès racine de la partition n’est pas spécifié, aucune colonne supplémentaire n’est générée. | Non                                            |
| maxConcurrentConnections |La limite supérieure de connexions simultanées établies au magasin de données pendant l’exécution de l’activité. Spécifiez une valeur uniquement lorsque vous souhaitez limiter les connexions simultanées.| Non                                                          |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3CompatibleStorage",
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
                    "type": "AmazonS3CompatibleReadSettings",
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

### <a name="file-list-examples"></a>Exemples de liste de fichiers

Cette section décrit le comportement résultant de l’utilisation d’un chemin d’accès de liste de fichiers dans la source de l’activité Copy.

Supposons que vous disposez de la structure de dossiers sources suivante et que vous souhaitez copier les fichiers en gras :

| Exemple de structure source                                      | Contenu de FileListToCopy.txt                             | Configuration de Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Métadonnées<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Dans le jeu de données :**<br>- compartiment : `bucket`<br>- chemin d’accès du dossier : `FolderA`<br><br>**Dans la source de l’activité Copy :**<br>- chemin d’accès à la liste de fichiers : `bucket/Metadata/FileListToCopy.txt` <br><br>Le chemin d’accès de la liste de fichiers pointe vers un fichier texte dans le même magasin de données, qui contient la liste de fichiers que vous voulez copier, un fichier par ligne indiquant le chemin d’accès relatif configuré dans le jeu de données. |


## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriétés de l’activité GetMetadata

Pour en savoir plus sur les propriétés, consultez [Activité GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propriétés de l’activité Delete

Pour en savoir plus sur les propriétés, consultez [Activité Delete](delete-activity.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge par l’activité Copy dans Azure Data Factory en tant que sources et récepteurs, consultez [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
