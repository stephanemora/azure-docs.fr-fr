---
title: Copier et transformer des données dans Azure Data Lake Storage Gen2
description: Découvrez comment copier des données vers et depuis Azure Data Lake Storage Gen2 et y transformer des données avec Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: accf7aef7d810d9d898945adb7835ee1a7ed9404
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929967"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copier et transformer des données dans Data Lake Storage Gen2 avec Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) est un ensemble de fonctionnalités dédiées à l'analytique du Big Data et intégrées au service [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md). Il vous permet d’interagir avec vos données selon les deux paradigmes que sont le système de fichiers et le stockage d’objets.

Cet article indique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers Azure Data Lake Storage Gen2 et utiliser Data Flow pour transformer les données dans Azure Data Lake Storage Gen2. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Data Lake Storage Gen2 est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)

Pour l’activité de copie, avec ce connecteur, vous pouvez effectuer les opérations suivantes :

- Copier des données depuis/vers Azure Data Lake Storage Gen2 avec une clé de compte, un principal de service ou des identités managées pour les authentifications de ressources Azure.
- Copier des fichiers tels quels, ou analyser ou générer des fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

>[!IMPORTANT]
>Si vous activez l’option **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** dans les paramètres du pare-feu Stockage Azure et que vous souhaitez utiliser le runtime d’intégration Azure pour vous connecter à Data Lake Storage Gen2, vous devez utiliser une [authentification d’identité managée](#managed-identity) pour ADLS Gen2.

>[!TIP]
>Si vous activez l’espace de noms hiérarchique, il n’existe actuellement aucune interopérabilité des opérations entre les API Blob et Azure Data Lake Storage Gen2. Si vous recevez l’erreur « ErrorCode=FilesystemNotFound » avec le message « Le système de fichiers spécifié n’existe pas », celle-ci vient du fait que le système de fichiers du récepteur spécifié a été créé via l’API Blob et non par le biais de l’API Azure Data Lake Storage Gen2 comme partout ailleurs. Pour résoudre ce problème, spécifiez un nouveau système de fichiers avec un nom qui n’existe pas en tant que nom d’un conteneur d’objets Blob. Ensuite, Data Factory crée automatiquement ce système de fichiers lors de la copie des données.

## <a name="get-started"></a>Prise en main

>[!TIP]
>Pour une procédure pas à pas d’utilisation du connecteur Data Lake Storage Gen2, consultez [Charger des données dans Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le connecteur Azure Data Lake Storage Gen2 prend en charge les types d’authentification suivants. Consultez les sections correspondantes pour plus d’informations :

- [Authentification par clé de compte](#account-key-authentication)
- [Authentification d’un principal du service](#service-principal-authentication)
- [Identités managées pour authentifier les ressources Azure](#managed-identity)

>[!NOTE]
>Lorsque vous utilisez PolyBase pour charger des données dans SQL Data Warehouse, si votre instance Data Lake Storage Gen2 source est configurée avec le point de terminaison de réseau virtuel, vous devez utiliser l’authentification par identité managée comme requis par PolyBase. Consultez la section sur l’[authentification par identité managée](#managed-identity) pour en savoir plus sur la configuration requise.

### <a name="account-key-authentication"></a>Authentification par clé de compte

Pour l’authentification par clé de compte de stockage, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **AzureBlobFS**. |OUI |
| url | Point de terminaison pour Data Lake Storage Gen2 avec le modèle de `https://<accountname>.dfs.core.windows.net`. | OUI |
| accountKey | Clé de compte pour Data Lake Storage Gen2. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d'intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. Si cette propriété n’est pas spécifiée, le runtime d'intégration Azure par défaut est utilisé. |Non |

>[!NOTE]
>Le point de terminaison du système de fichiers ADLS secondaire n’est pas pris en charge lors de l’utilisation de l’authentification de clé du compte. Vous pouvez utiliser d’autres types d’authentification.

**Exemple :**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
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

### <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour l’authentification de principal de service, effectuez les étapes suivantes.

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez l’autorisation nécessaire au principal de service. Pour obtenir des exemples sur le fonctionnement des autorisations dans Data Lake Storage Gen2, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **En tant que source** : Dans l’Explorateur Stockage, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont et au système de fichiers et l’autorisation **Lecture** pour les fichiers à copier. Dans le contrôle d’accès (IAM), vous pouvez également accorder au moins un rôle **Lecteur des données Blob du stockage** .
    - **En tant que récepteur** : Dans l’Explorateur Stockage, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont et au système de fichiers et l’autorisation **Écriture** pour le dossier récepteur. Dans le contrôle d’accès (IAM), vous pouvez également accorder au moins un rôle **Contributeur aux données Blob du stockage**.

>[!NOTE]
>Si vous utilisez l’IU Data Factory pour la création et que le principal de service n’est pas défini avec le rôle « Lecteur des données Blob du stockage/Contributeur aux données Blob du stockage » dans IAM, quand vous effectuez une connexion de test ou accédez à des dossiers, choisissez « Tester la connexion au chemin du fichier » ou « Parcourir à partir du chemin spécifié », puis spécifiez un système de fichiers ou un chemin avec l’autorisation Exécution pour continuer.

Ces propriétés sont prises en charge pour le service lié :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **AzureBlobFS**. |OUI |
| url | Point de terminaison pour Data Lake Storage Gen2 avec le modèle de `https://<accountname>.dfs.core.windows.net`. | OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ comme `SecureString` pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du portail Azure. | OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d'intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, le runtime d'intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identités managées pour authentifier les ressources Azure

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité managée pour l’authentification Azure Data Lake Storage Gen2, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données et de les copier depuis ou vers votre Data Lake Storage Gen2.

Pour utiliser les identités managées afin d’authentifier les ressources Azure, procédez comme suit.

1. [Récupérez les informations d’identité managée de Data Factory](data-factory-service-identity.md#retrieve-managed-identity) en copiant la valeur **ID d’application de l’identité du service** générée en même temps que votre fabrique.

2. Accordez l’autorisation nécessaire à l’identité managée. Pour obtenir des exemples sur le fonctionnement des autorisations dans Data Lake Storage Gen2, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **En tant que source** : Dans l’Explorateur Stockage, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont et au système de fichiers et l’autorisation **Lecture** pour les fichiers à copier. Dans le contrôle d’accès (IAM), vous pouvez également accorder au moins un rôle **Lecteur des données Blob du stockage** .
    - **En tant que récepteur** : Dans l’Explorateur Stockage, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont et au système de fichiers et l’autorisation **Écriture** pour le dossier récepteur. Dans le contrôle d’accès (IAM), vous pouvez également accorder au moins un rôle **Contributeur aux données Blob du stockage**.

>[!NOTE]
>Si vous utilisez l’IU Data Factory pour la création et que l’identité managée n’est pas définie avec le rôle « Lecteur des données Blob du stockage/Contributeur aux données Blob du stockage » dans IAM, quand vous effectuez une connexion de test ou accédez à des dossiers, choisissez « Tester la connexion au chemin du fichier » ou « Parcourir à partir du chemin spécifié », puis spécifiez un système de fichiers ou un chemin avec l’autorisation Exécution pour continuer.

>[!IMPORTANT]
>Si vous utilisez PolyBase pour charger des données à partir de Data Lake Storage Gen2 dans SQL Data Warehouse, lors de l’utilisation de l’authentification d’identité managée pour Data Lake Storage Gen2, veillez à suivre également les étapes 1 et 2 de [ce guide](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) pour 1) inscrire votre serveur SQL Database auprès d’Azure Active Directory (Azure AD) et pour 2) attribuer le rôle de contributeur aux données Blob Storage à votre serveur SQL Database. Le reste est géré par Data Factory. Si votre instance Data Lake Storage Gen2 source est configurée avec un point de terminaison de réseau virtuel Azure, pour utiliser PolyBase depuis cet emplacement, vous devez utiliser l’authentification par identité managée comme requis par PolyBase.

Ces propriétés sont prises en charge pour le service lié :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **AzureBlobFS**. |OUI |
| url | Point de terminaison pour Data Lake Storage Gen2 avec le modèle de `https://<accountname>.dfs.core.windows.net`. | OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d'intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. À défaut de spécification, le runtime d'intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour Data Lake Storage Gen2 dans les paramètres `location` du jeu de données basé sur le format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **AzureBlobFSLocation**. | OUI      |
| fileSystem | Nom du système de fichiers Data Lake Storage Gen2.                              | Non       |
| folderPath | Chemin d’accès à un dossier sous le système de fichiers donné. Si vous souhaitez utiliser un caractère générique pour filtrer les dossiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Nom de fichier dans le chemin d’accès fileSystem +folderPath donné. Si vous souhaitez utiliser un caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

### <a name="legacy-dataset-model"></a>Modèle de jeu de données hérité

>[!NOTE]
>Le modèle de jeu de données suivant est toujours pris en charge tel quel à des fins de compatibilité descendante. Il est recommandé d’utiliser le nouveau modèle mentionné dans la section ci-dessus à partir de maintenant. L’interface utilisateur de création ADF peut désormais générer ce nouveau modèle.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzureBlobFSFile**. |OUI |
| folderPath | Chemin vers le dossier dans Data Lake Storage Gen2. Si non spécifié, il pointe vers la racine. <br/><br/>Le filtre de caractères génériques est pris en charge. Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d'échappement si le nom réel de votre dossier contient des caractères génériques ou ce caractère d'échappement. <br/><br/>Exemples : système de fichiers/dossier/. Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). |Non |
| fileName | Filtre de nom ou de caractère générique pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d'échappement si le nom réel de votre fichier contient des caractères génériques ou ce caractère d'échappement.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom de fichier suivant ce modèle : « *Data.[ID GUID d’exécution d’activité].[GUID si FlattenHierarchy].[format si configuré].[compression si configurée]*  », par exemple, « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». Si vous effectuez la copie à partir d’une source tabulaire à l’aide d’un nom de table au lieu d’une requête, le modèle du nom est «  *[nom_table].[format].[compression si configurée]*  », par exemple « MyTable.csv ». |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut Dernière modification. Les fichiers sont sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les performances globales du déplacement des données sont influencées par l’activation de ce paramètre lorsque vous souhaitez appliquer un filtre sur de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données. Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés. Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut Dernière modification. Les fichiers sont sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les performances globales du déplacement des données sont influencées par l’activation de ce paramètre lorsque vous souhaitez appliquer un filtre sur de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données. Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés. Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| format | Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous **Format** sur l’une de ces valeurs. Pour en savoir plus, voir les sections [Format Text](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format ORC](supported-file-formats-and-compression-codecs.md#orc-format) et [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul fichier avec un nom donné, spécifiez **folderPath** avec la partie dossier et **fileName** avec un nom de fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec un filtre de caractères génériques. 

**Exemple :**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez les articles [Configurations des activités de copie](copy-activity-overview.md#configuration) et [Pipelines et activités](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Data Lake Storage Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 comme type de source

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour Data Lake Storage Gen2 dans les paramètres `storeSettings` de la source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **AzureBlobFSReadSetting**. | OUI                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                            |
| wildcardFolderPath       | Chemin d’accès du dossier avec des caractères génériques sous le système de fichiers configuré dans le jeu de données pour filtrer les dossiers sources. <br>Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom réel de votre dossier contient un caractère générique ou ce caractère d’échappement. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                            |
| wildcardFileName         | Nom du fichier avec des caractères génériques situé dans le chemin d’accès système de fichiers + folderPath/wildcardFolderPath donné pour filtrer les fichiers sources. <br>Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom réel de votre dossier contient un caractère générique ou ce caractère d’échappement. Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données. |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut Dernière modification. Les fichiers sont sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données. Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés. Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                            |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                            |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                            |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSetting",
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

#### <a name="legacy-source-model"></a>Modèle source hérité

>[!NOTE]
>Le modèle source de copie suivant est toujours pris en charge tel quel à des fins de compatibilité descendante. Il est recommandé d’utiliser le nouveau modèle mentionné plus haut à partir de maintenant. L’interface utilisateur de création ADF peut désormais générer ce nouveau modèle.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **AzureBlobFSSource**. |OUI |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur.<br/>Les valeurs autorisées sont **true** (par défaut) et **false**. | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour Data Lake Storage Gen2 dans les paramètres `storeSettings` du récepteur de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **AzureBlobFSWriteSetting**. | OUI      |
| copyBehavior             | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non       |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non       |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Modèle récepteur hérité

>[!NOTE]
>Le modèle récepteur de copie suivant est toujours pris en charge tel quel à des fins de compatibilité descendante. Il est recommandé d’utiliser le nouveau modèle mentionné plus haut à partir de maintenant. L’interface utilisateur de création ADF peut désormais générer ce nouveau modèle.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du récepteur d’activité de copie doit être définie sur **AzureBlobFSSink**. |OUI |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
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

### <a name="folder-and-file-filter-examples"></a>Exemples de filtres de dossier et de fichier

Cette section décrit le comportement résultant de l’utilisation de filtres de caractères génériques dans les noms de fichier et les chemins de dossier.

| folderPath | fileName | recursive | Structure du dossier source et résultat du filtrage (les fichiers en **gras** sont récupérés)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (vide, utiliser la valeur par défaut) | false | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*` | (vide, utiliser la valeur par défaut) | true | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*` | `*.csv` | false | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `Folder*` | `*.csv` | true | DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Quelques exemples de valeurs recursive et copyBehavior

Cette section décrit le comportement résultant de l’opération de copie pour différentes combinaisons de valeurs recursive et copyBehavior.

| recursive | copyBehavior | Structure du dossier source | Cible obtenue |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le Dossier1 cible est créé et structuré de la même manière que la source :<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un fichier avec un nom de fichier généré automatiquement. |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec un nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Conserver les ACL de Data Lake Storage Gen1

>[!TIP]
>Pour copier des données à partir d’Azure Data Lake Storage Gen1 dans Gen2 en général, consultez l’article [Copier les données depuis Azure Data Lake Storage Gen1 vers Gen2 avec Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) pour obtenir une procédure pas à pas et découvrir les meilleures pratiques.

Lorsque vous copiez des fichiers à partir d’Azure Data Lake Storage Gen1 vers Gen2, vous pouvez choisir de conserver les listes de contrôle d’accès (ACL) POSIX, ainsi que des données. Pour plus d’informations sur le contrôle d'accès, voir [Contrôle d’accès dans Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) et [Contrôle d’accès dans Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Les types suivants d’ACL peuvent être conservés à l’aide de l’activité de copie Azure Data Factory. Vous pouvez sélectionner un ou plusieurs types :

- **ACL** : copiez et conservez les listes de contrôle d’accès POSIX sur les fichiers et les répertoires. Cette option copie les ACL existantes complètes de la source vers le récepteur. 
- **Propriétaire** : copiez et conservez l’utilisateur propriétaire des fichiers et des répertoires. Un accès de super utilisateur pour le récepteur Data Lake Storage Gen2 est requis.
- **Groupe** : copiez et conservez le groupe propriétaire des fichiers et des répertoires. Un accès de super utilisateur pour le récepteur Data Lake Storage Gen2 ou l’utilisateur propriétaire (si ce dernier est également membre du groupe cible) est requis.

Si vous spécifiez une copie à partir d’un dossier, Data Factory réplique les ACL de ce dossier, ainsi que les fichiers et les répertoires qu’il contient, si `recursive` est défini sur true. Si vous spécifiez une copie à partir d’un seul fichier, les ACL sur ce fichier sont copiés.

>[!IMPORTANT]
>Lorsque vous choisissez de conserver les ACL, veillez à accorder des autorisations suffisantes à Data Factory pour qu’il fonctionne avec votre compte Data Lake Storage Gen2 récepteur. Par exemple, utilisez l’authentification par clé de compte ou attribuez le rôle de propriétaire des données Blob de stockage au principal de service ou à l’identité managée.

Lorsque vous configurez la source en tant que Data Lake Storage Gen1 avec l’option de copie binaire ou le format binaire, et le récepteur en tant que Data Lake Storage Gen2 avec l’option de copie binaire ou le format binaire, vous disposez de l’option **Conserver** sur la page **Paramètres de l’outil de données de copie** ou sur l’onglet **Activité de copie** > **Paramètres** pour la création de l’activité.

![Conserver les ACL de Data Lake Storage Gen1 vers Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Voici un exemple de configuration JSON (voir `preserve`) : 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriétés de flux de données de mappage

Découvrez plus de détails sur la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans la fonctionnalité de flux de données de mappage.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriétés de l’activité GetMetadata

Pour en savoir plus sur les propriétés, consultez [Activité GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propriétés de l’activité Delete

Pour en savoir plus sur les propriétés, consultez [Activité Delete](delete-activity.md).
## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
