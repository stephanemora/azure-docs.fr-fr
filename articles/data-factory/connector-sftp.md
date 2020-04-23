---
title: Copier des données depuis et vers un serveur SFTP
description: Découvrez comment copier des données depuis et vers un serveur SFTP à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: e6d29f73716b04699e0cd250396df7f7d744d4c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415244"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Copier des données depuis et vers un serveur SFTP à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Version actuelle](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment copier des données depuis et vers un serveur SFTP. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SFTP est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)

Plus précisément, ce connecteur SFTP prend en charge ce qui suit :

- Copie de fichiers depuis et vers SFTP en utilisant une authentification **De base** ou **SshPublicKey**.
- Copie de fichiers en l'état ou analyse/génération de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de SFTP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SFTP sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Sftp**. |Oui |
| host | Nom ou adresse IP du serveur SFTP. |Oui |
| port | Port sur lequel le serveur SFTP écoute.<br/>Valeurs autorisées : integer, la valeur par défaut est **22**. |Non |
| skipHostKeyValidation | Spécifiez s’il faut ignorer la validation de la clé hôte.<br/>Valeurs autorisées : **true**, **false** (par défaut).  | Non |
| hostKeyFingerprint | Spécifiez l’empreinte de la clé hôte. | Oui, si la valeur de « skipHostKeyValidation » est définie sur false.  |
| authenticationType | Spécification du type d’authentification.<br/>Les valeurs autorisées sont les suivantes : **Basic**, **SshPublicKey**. Reportez-vous aux sections [Utilisation de l’authentification par clé publique SSH](#using-basic-authentication) et [Utilisation de l’authentification par clé publique SSH](#using-ssh-public-key-authentication) portant respectivement sur des propriétés supplémentaires et des exemples JSON. |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

### <a name="using-basic-authentication"></a>Utilisation de l’authentification de base

Pour utiliser l’authentification de base, définissez la propriété « authenticationType » sur **De base** et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur SFTP présentées dans la dernière section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Utilisateur ayant accès au serveur SFTP. |Oui |
| mot de passe | Mot de passe de l’utilisateur (userName). Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |

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
| userName | Utilisateur ayant accès au serveur SFTP |Oui |
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour SFTP sous les paramètres `location` dans le jeu de données basé sur le format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **SFTPLocation**. | Oui      |
| folderPath | Chemin d’accès du dossier. Si vous souhaitez utiliser un caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Nom de fichier dans le chemin d’accès folderPath donné. Si vous souhaitez utiliser un caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |

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

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SFTP.

### <a name="sftp-as-source"></a>SFTP en tant que source

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour SFTP sous les paramètres `storeSettings` dans la source de la copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **SftpReadSettings**. | Oui                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                            |
| wildcardFolderPath       | Chemin d’accès du dossier avec des caractères génériques pour filtrer les dossiers sources. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                            |
| wildcardFileName         | Nom du fichier avec des caractères génériques situé dans le chemin d’accès folderPath/wildcardFolderPath donné pour filtrer les fichiers sources. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                            |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                            |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP en tant que récepteur

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour SFTP sous les paramètres `storeSettings` dans le récepteur de la copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **SftpWriteSettings**. | Oui      |
| copyBehavior             | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non       |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non       |
| useTempFileRename | Indiquez si vous souhaitez effectuer un chargement dans un ou plusieurs fichiers temporaires puis les renommer, ou si vous souhaitez écrire directement dans le dossier ou l’emplacement de fichier cible. Par défaut, ADF écrit d’abord les données dans les fichiers temporaires, puis renomme les fichiers une fois le chargement terminé. Il procède ainsi pour deux raisons : 1) éviter un conflit d’écriture qui endommagerait le fichier si d’autres processus d’écriture étaient en cours dans ce fichier, et 2) vérifier que la version d’origine du fichier est présente tout au long du processus de transfert. Si votre serveur SFTP ne prend pas en charge l’opération de renommage, désactivez cette option et vérifiez qu’il n’existe pas un autre processus d’écriture en cours dans le fichier cible. Lisez les conseils de dépannage situés sous le tableau. | Non. La valeur par défaut est true. |
| operationTimeout | Délai d’attente avant l’expiration de chaque demande d’écriture au serveur SFTP. La valeur par défaut est 60 minutes (01:00:00).|Non |

>[!TIP]
>Si vous avez rencontré l’erreur « UserErrorSftpPathNotFound », « UserErrorSftpPermissionDenied » ou « SftpOperationFail » lors de l’écriture de données dans SFTP alors que l’utilisateur SFTP dispose de l’autorisation appropriée, vérifiez que votre serveur SFTP prend en charge l’opération de renommage de fichier. Si ce n’est pas le cas, désactivez l’option permettant d’effectuer le chargement avec le fichier temporaire (`useTempFileRename`), puis réessayez. Découvrez la propriété du tableau ci-dessus. Si vous utilisez des runtime d’intégration auto-hébergés pour la copie, veillez à utiliser la version 4.6 ou une version ultérieure.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToSFTP",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
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
| type | La propriété type du jeu de données doit être définie sur : **FileShare** |Oui |
| folderPath | Chemin d'accès au dossier. Le filtre de caractères génériques est pris en charge, et les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de fichier contient effectivement ce caractère d’échappement ou générique. <br/><br/>Exemples : dossier_racine/sous-dossier/ ; consultez d’autres exemples dans [Exemples de filtres de dossier et de fichier](#folder-and-file-filter-examples). |Oui |
| fileName |  **Filtre de nom ou de caractère générique** pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d'échappement si le nom réel de votre dossier contient des caractères génériques ou ce caractère d'échappement. |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Sachez que les performances globales du déplacement des données sont affectées par l’activation de ce paramètre lorsque vous souhaitez filtrer des fichiers parmi de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Sachez que les performances globales du déplacement des données sont affectées par l’activation de ce paramètre lorsque vous souhaitez filtrer des fichiers parmi de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous voulez analyser des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [format Json](supported-file-formats-and-compression-codecs-legacy.md#json-format), [format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non |

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

### <a name="legacy-copy-activity-source-model"></a>Modèle hérité de la source d’activité de copie

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **FileSystemSource** |Oui |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

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

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
