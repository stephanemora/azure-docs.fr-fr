---
title: Copier des données d’un serveur SFTP à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez le connecteur MySQL dans Azure Data Factory, qui permet de copier des données d’un serveur SFTP vers une banque de données réceptrice prise en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 1a62e9e8377705af1a70e356f554cfa549c58f20
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233472"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Copier des données d’un serveur SFTP à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Version actuelle](connector-sftp.md)

Cet article explique comment copier des données depuis un serveur SFTP. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SFTP est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [pris en charge de la matrice de source/récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)

Plus précisément, ce connecteur SFTP prend en charge ce qui suit :

- Copie de fichiers en utilisant une authentification **De base** ou **SshPublicKey**.
- Copie de fichiers en l'état ou analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de SFTP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SFTP sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Sftp**. |Oui |
| host | Nom ou adresse IP du serveur SFTP. |OUI |
| port | Port sur lequel le serveur SFTP écoute.<br/>Valeurs autorisées : integer, la valeur par défaut est **22**. |Non  |
| skipHostKeyValidation | Spécifiez s’il faut ignorer la validation de la clé hôte.<br/>Valeurs autorisées : **true**, **false** (par défaut).  | Non  |
| hostKeyFingerprint | Spécifiez l’empreinte de la clé hôte. | Oui, si la valeur de « skipHostKeyValidation » est définie sur false.  |
| authenticationType | Spécification du type d’authentification.<br/>Les valeurs autorisées sont les suivantes : **Basic**, **SshPublicKey**. Reportez-vous aux sections [Utilisation de l’authentification par clé publique SSH](#using-basic-authentication) et [Utilisation de l’authentification par clé publique SSH](#using-ssh-public-key-authentication) portant respectivement sur des propriétés supplémentaires et des exemples JSON. |OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

### <a name="using-basic-authentication"></a>Utilisation de l’authentification de base

Pour utiliser l’authentification de base, définissez la propriété « authenticationType » sur **De base** et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur SFTP présentées dans la dernière section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Utilisateur ayant accès au serveur SFTP. |OUI |
| password | Mot de passe de l’utilisateur (nom d’utilisateur). Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |

**Exemple :**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
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

### <a name="using-ssh-public-key-authentication"></a>Utilisation de l’authentification par clé publique SSH

Pour utiliser l’authentification par clé publique SSH, définissez la propriété « authenticationType » sur **De base** et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur SFTP présentées dans la dernière section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Utilisateur ayant accès au serveur SFTP |OUI |
| privateKeyPath | Spécifiez le chemin absolu au fichier de clé privée auquel le runtime d’intégration peut accéder. S’applique uniquement quand un type auto-hébergé du runtime d’intégration est spécifié dans « connectVia ». | Spécifiez soit la propriété `privateKeyPath`, soit la propriété `privateKeyContent`.  |
| privateKeyContent | Contenu de clé privée SSH encodé en Base64. La clé privée SSH doit être au format OpenSSH. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Spécifiez soit la propriété `privateKeyPath`, soit la propriété `privateKeyContent`. |
| passPhrase | Spécifiez la phrase secrète/le mot de passe pour déchiffrer la clé privée si le fichier de clé est protégé par une phrase secrète. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui, si le fichier de clé privée est protégé par une phrase secrète. |

> [!NOTE]
> Le connecteur SFTP prend en charge la clé OpenSSH RSA/DSA. Assurez-vous que le contenu de votre keyfile commence par «-----BEGIN [RSA/DSA] PRIVATE KEY-----». Si le fichier de clé privée est un fichier au format ppk, utilisez l’outil Putty pour effectuer la conversion du format .ppk vers le format OpenSSH. 

**Exemple 1 : Authentification SshPublicKey avec un chemin de fichier de clé privée**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Authentification SshPublicKey avec un contenu de clé privée**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

- Pour **Parquet et le format de texte délimité**, reportez-vous à [jeu de données de format Parquet et texte délimité](#parquet-and-delimited-text-format-dataset) section.
- Pour les autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autre jeu de données de format](#other-format-dataset) section.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet et le jeu de données de format texte délimité

Pour copier des données de SFTP dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) l’article sur le jeu de données en fonction du format et la prise en charge Paramètres. Les propriétés suivantes sont prises en charge pour SFTP sous `location` paramètres dans le jeu de données en fonction du format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **SftpLocation**. | Oui      |
| folderPath | Le chemin d’accès au dossier. Si vous souhaitez utiliser le caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez dans les paramètres de source d’activité. | Non        |
| fileName   | Le nom du fichier sous le paramètre folderPath donné. Si vous souhaitez utiliser le caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez dans les paramètres de source d’activité. | Non        |

> [!NOTE]
> **Partage de fichiers** type de jeu de données avec le format Parquet/texte mentionné dans la section suivante est toujours prise en charge-est pour l’activité de copie/recherche/obtention des métadonnées pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Autre jeu de données de format

Pour copier des données de SFTP dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **FileShare** |Oui |
| folderPath | Chemin d'accès au dossier. Le filtre de caractères génériques est pris en charge, et les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de fichier contient effectivement ce caractère d’échappement ou générique. <br/><br/>Exemples : dossier_racine/sous-dossier/ ; consultez d’autres exemples dans [Exemples de filtres de dossier et de fichier](#folder-and-file-filter-examples). |Oui |
| fileName |  **Filtre de nom ou de caractère générique** pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d'échappement si le nom réel de votre dossier contient des caractères génériques ou ce caractère d'échappement. |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> N’oubliez pas que les performances globales du déplacement des données seront affectés par l’activation de ce paramètre lorsque vous souhaitez des filtres de fichiers à partir de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL qui signifie qu'aucun filtre d’attribut de fichier ne sera appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> N’oubliez pas que les performances globales du déplacement des données seront affectés par l’activation de ce paramètre lorsque vous souhaitez des filtres de fichiers à partir de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL qui signifie qu'aucun filtre d’attribut de fichier ne sera appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous voulez analyser des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non  |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul fichier avec un nom donné, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le filtre de caractères génériques.

>[!NOTE]
>Si vous utilisez la propriété « fileFilter » pour le filtre de fichiers, il est toujours pris en charge tel quel, mais il est conseillé d’utiliser la nouvelle fonctionnalité de filtre ajoutée à « fileName » à l’avenir.

**Exemple :**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SFTP.

### <a name="sftp-as-source"></a>SFTP en tant que source

- Pour la copie depuis **Parquet et le format de texte délimité**, reportez-vous à [Parquet et source de format de texte délimité](#parquet-and-delimited-text-format-source) section.
- Pour copier à partir d’autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autre source de format](#other-format-source) section.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet et source de format de texte délimité

Pour copier des données de SFTP dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) article sur la source d’activité de copie basée sur le format et paramètres pris en charge. Les propriétés suivantes sont prises en charge pour SFTP sous `storeSettings` paramètres de source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propriété de type sous `storeSettings` doit être définie sur **SftpReadSetting**. | Oui                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                             |
| wildcardFolderPath       | Le chemin d’accès de dossier avec des caractères génériques pour filtrer les dossiers de code source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                             |
| wildcardFileName         | Le nom de fichier avec des caractères génériques sous folderPath/wildcardFolderPath donné pour filtrer les fichiers source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                             |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                             |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de stockage simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                            |

> [!NOTE]
> Pour Parquet/texte délimité par des **FileSystemSource** source d’activité de copie type mentionné dans la section suivante est toujours prise en charge-concerne la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSetting",
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

#### <a name="other-format-source"></a>Autre source de format

Pour copier des données de SFTP dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge dans l’activité de copie **source** section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **FileSystemSource** |Oui |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non  |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de stockage simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Exemples de filtres de dossier et de fichier

Cette section décrit le comportement résultant de l’utilisation de filtres de caractères génériques dans les noms de fichier et les chemins de dossier.

| folderPath | fileName | recursive | Structure du dossier source et résultat du filtrage (les fichiers en **gras** sont récupérés)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vide, utiliser la valeur par défaut) | false | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*` | (vide, utiliser la valeur par défaut) | true | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*` | `*.csv` | false | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*` | `*.csv` | true | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
