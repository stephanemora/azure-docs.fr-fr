---
title: Copier des données vers ou depuis Azure Data Lake Storage Gen1
description: Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Azure Data Lake Store ou à partir d’Azure Data Lake Store vers des banques de données réceptrices prises en charge.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 17f7c62600bcc2aa21fbcea8ecd96810be412a26
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930526"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Version actuelle](connector-azure-data-lake-store.md)

Cet article explique comment copier des données vers et depuis Azure Data Lake Storage Gen1. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Data Lake Storage Gen1 est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md) 
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)

Concrètement, avec ce connecteur, vous pouvez effectuer les opérations suivantes :

- Copier des fichiers à l’aide de l’une des méthodes d’authentification suivantes : principal de service ou identités managées pour les ressources Azure.
- Copier des fichiers tels quels, ou analyser ou générer des fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Si vous copiez des données à l’aide du runtime d’intégration auto-hébergé, configurez le pare-feu d’entreprise pour autoriser le trafic sortant vers `<ADLS account name>.azuredatalakestore.net` et `login.microsoftonline.com/<tenant>/oauth2/token` sur le port 443. Ce dernier est le service d’émission de jeton de sécurité Azure avec lequel le runtime d’intégration a besoin de communiquer pour obtenir le jeton d’accès.

## <a name="get-started"></a>Prise en main

> [!TIP]
> Pour obtenir une procédure pas à pas d’utilisation du connecteur Azure Data Lake Store, consultez [Charger des données dans Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres à Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Azure Data Lake Store :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété `type` doit être définie sur **AzureDataLakeStore**. | OUI |
| dataLakeStoreUri | Informations à propos du compte Azure Data Lake Store. Cette information prend un des formats suivants : `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | OUI |
| subscriptionId | ID de l’abonnement Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| resourceGroupName | Nom du groupe de ressources Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d’intégration Azure ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. Si cette propriété n’est pas spécifiée, le runtime d’intégration Azure par défaut est utilisé. |Non |

### <a name="use-service-principal-authentication"></a>Utiliser une authentification de principal de service

Pour l’authentification de principal de service, effectuez les étapes suivantes.

1. Inscrivez une entité d’application dans Azure Active Directory et donnez-lui accès à Data Lake Store. Consultez la page [Authentification de service à service](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) pour des instructions détaillées. Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez l’autorisation nécessaire au principal de service. Pour obtenir des exemples sur le fonctionnement des autorisations dans Data Lake Storage Gen1, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **En tant que source** : Dans l’**Explorateur de données** > **Accès**, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont (y compris le dossier racine) et l’autorisation **Lecture** pour les fichiers à copier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Il n’existe aucune exigence sur le contrôle d’accès au niveau du compte (IAM).
    - **En tant que récepteur** : Dans l’**Explorateur de données** > **Accès**, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont (y compris le dossier racine) et l’autorisation **Écriture** pour le dossier récepteur. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez le runtime d’intégration Azure pour effectuer la copie (la source et le récepteur sont tous les deux dans le cloud), dans IAM, attribuez au moins le rôle **Lecteur** pour permettre à Data Factory de détecter la région pour Data Lake Store. Pour éviter ce rôle IAM, vous devez explicitement [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) avec l’emplacement de Data Lake Store. Par exemple, si votre Data Lake Store se trouve dans la région Europe Ouest, créez un runtime d’intégration Azure dont l’emplacement est défini sur « Europe Ouest ». Associez-les dans le service lié Data Lake Store comme indiqué dans l’exemple suivant.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que `SecureString` afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| tenant | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | OUI |

**Exemple :**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Utiliser des identités managées afin d’authentifier les ressources Azure

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité managée pour l’authentification Data Lake Store, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données et de les copier depuis ou vers votre Data Lake Store.

Pour utiliser les identités managées afin d’authentifier les ressources Azure, effectuez les étapes suivantes.

1. [Récupérez les informations d’identité managée de Data Factory](data-factory-service-identity.md#retrieve-managed-identity) en copiant la valeur ID d’application de l’identité du service générée en même temps que votre fabrique.

2. Accordez à l’identité managée l’accès à Data Lake Store. Pour obtenir des exemples sur le fonctionnement des autorisations dans Data Lake Storage Gen1, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **En tant que source** : Dans l’**Explorateur de données** > **Accès**, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont (y compris le dossier racine) et l’autorisation **Lecture** pour les fichiers à copier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Il n’existe aucune exigence sur le contrôle d’accès au niveau du compte (IAM).
    - **En tant que récepteur** : Dans l’**Explorateur de données** > **Accès**, accordez au moins l’autorisation **Exécution** à l’ensemble des dossiers en amont (y compris le dossier racine) et l’autorisation **Écriture** pour le dossier récepteur. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez le runtime d’intégration Azure pour effectuer la copie (la source et le récepteur sont tous les deux dans le cloud), dans IAM, attribuez au moins le rôle **Lecteur** pour permettre à Data Factory de détecter la région pour Data Lake Store. Pour éviter ce rôle IAM, vous devez explicitement [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) avec l’emplacement de Data Lake Store. Associez-les dans le service lié Data Lake Store comme indiqué dans l’exemple suivant.

Dans Azure Data Factory, vous n’avez pas besoin de spécifier des propriétés en plus des informations générales Data Lake Store du service lié.

**Exemple :**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

Les propriétés suivantes sont prises en charge pour Azure Data Lake Store Gen1 dans les paramètres `location` du jeu de données basé sur le format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **AzureDataLakeStoreLocation**. | OUI      |
| folderPath | Chemin d’accès du dossier. Si vous souhaitez utiliser un caractère générique pour filtrer les dossiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Nom de fichier dans le chemin d’accès folderPath donné. Si vous souhaitez utiliser un caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

### <a name="legacy-dataset-model"></a>Modèle de jeu de données hérité

>[!NOTE]
>Le modèle de jeu de données suivant est toujours pris en charge tel quel à des fins de compatibilité descendante. Il est recommandé d’utiliser le nouveau modèle mentionné dans la section ci-dessus à partir de maintenant. L’interface utilisateur de création ADF peut désormais générer ce nouveau modèle.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type du jeu de données doit être définie sur **AzureDataLakeStoreFile**. |OUI |
| folderPath | Chemin vers le dossier dans Data Lake Store. Si non spécifié, il pointe vers la racine. <br/><br/>Le filtre de caractères génériques est pris en charge. Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom réel de votre dossier contient un caractère générique ou ce caractère d’échappement. <br/><br/>Par exemple : dossierracine/sous-dossier/. Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). |Non |
| fileName | Filtre de nom ou de caractère générique pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom de fichier suivant ce modèle : « *Data.[ID GUID d’exécution d’activité].[GUID si FlattenHierarchy].[format si configuré].[compression si configurée]*  », par exemple, « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». Si vous effectuez la copie à partir d’une source tabulaire à l’aide d’un nom de tableau au lieu d’une requête, le modèle du nom est «  *[nom_tableau].[format].[compression si configurée]*  », par exemple « MyTable.csv ». |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut Dernière modification. Les fichiers sont sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les performances globales du déplacement des données sont influencées par l’activation de ce paramètre lorsque vous souhaitez appliquer un filtre sur de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données. Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés. Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut Dernière modification. Les fichiers sont sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les performances globales du déplacement des données sont influencées par l’activation de ce paramètre lorsque vous souhaitez appliquer un filtre sur de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données. Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés. Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| format | Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous **Format** sur l’une de ces valeurs. Pour en savoir plus, voir les sections [Format Text](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul fichier portant un nom particulier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec un filtre de caractères génériques. 

**Exemple :**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Data Lake Store en tant que source et récepteur.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store en tant que source

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour Azure Data Lake Store Gen1 dans les paramètres `storeSettings` de la source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propriété de type sous `storeSettings` doit être définie sur **AzureDataLakeStoreReadSetting**. | OUI                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                            |
| wildcardFolderPath       | Chemin d’accès du dossier avec des caractères génériques pour filtrer les dossiers sources. <br>Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom réel de votre dossier contient un caractère générique ou ce caractère d’échappement. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                            |
| wildcardFileName         | Nom du fichier avec des caractères génériques situé dans le chemin d’accès folderPath/wildcardFolderPath donné pour filtrer les fichiers sources. <br>Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère). Utilisez `^` comme caractère d’échappement si le nom réel de votre dossier contient un caractère générique ou ce caractère d’échappement. Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données. |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut Dernière modification. Les fichiers sont sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données. Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés. Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                            |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                            |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                            |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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
| type | La propriété `type` de la source d’activité de copie doit être définie sur **AzureDataLakeStoreSource**. |OUI |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Lorsque l’option `recursive` est définie sur true et que le récepteur est un magasin basé sur un fichier, aucun dossier ou sous-dossier vide n’est copié ou créé au niveau du récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store en tant que récepteur

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour Azure Data Lake Store Gen1 dans les paramètres `storeSettings` du récepteur de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propriété de type sous `storeSettings` doit être définie sur **AzureDataLakeStoreWriteSetting**. | OUI      |
| copyBehavior             | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non       |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non       |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
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
| type | La propriété `type` du récepteur d’activité de copie doit être définie sur **AzureDataLakeStoreSink**. |OUI |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement. | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de données. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemples de comportement de l’opération de copie

Cette section décrit le comportement obtenu de l’opération de copie avec différentes combinaisons de valeurs `recursive` et `copyBehavior`.

| recursive | copyBehavior | Structure du dossier source | Cible obtenue |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le Dossier1 cible est créé et structuré de la même manière que la source :<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5. |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un fichier avec un nom de fichier généré automatiquement. |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec un nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Conserver les listes de contrôle d’accès (ACL) dans Data Lake Storage Gen2

Si vous souhaitez répliquer les listes de contrôle d’accès (ACL) ainsi que les fichiers de données lorsque vous migrez de Data Lake Storage Gen1 vers Data Lake Storage Gen2, consultez [Conserver les listes de contrôle d’accès à partir de Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Propriétés de flux de données de mappage

Découvrez plus de détails sur la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans la fonctionnalité de flux de données de mappage.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriétés de l’activité GetMetadata

Pour en savoir plus sur les propriétés, consultez [Activité GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propriétés de l’activité Delete

Pour en savoir plus sur les propriétés, consultez [Activité Delete](delete-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
