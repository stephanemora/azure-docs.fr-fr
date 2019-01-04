---
title: Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide de Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Azure Data Lake Store ou à partir d’Azure Data Lake Store vers des banques de données réceptrices prises en charge.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: jingwang
ms.openlocfilehash: 411223c2ef7b1400f2019ebc6704b3c4a5d41235
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387027"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Version actuelle](connector-azure-data-lake-store.md)

Cet article indique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers et depuis Azure Data Lake Storage Gen1 (anciennement Azure Data Lake Store). Il s’appuie sur la [vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de toute banque de données source prise en charge vers Azure Data Lake Store, ou d’Azure Data Lake Store vers toute banque de données réceptrice prise en charge. Consultez le tableau des [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure Data Lake Store prend en charge ce qui suit :

- Copie de fichiers à l’aide de l’une des méthodes d’authentification suivantes : **principal de service** ou **identités managées pour les ressources Azure**.
- Copie de fichiers en l’état, ou analyse ou génération de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Si vous copiez des données à l’aide du runtime d’intégration auto-hébergé, configurez le pare-feu d’entreprise pour autoriser le trafic sortant vers `<ADLS account name>.azuredatalakestore.net` et `login.microsoftonline.com/<tenant>/oauth2/token` sur le port 443. Ce dernier est le service d’émission de jeton de sécurité Azure avec lequel le runtime d’intégration a besoin de communiquer pour obtenir le jeton d’accès.

## <a name="get-started"></a>Prise en main

> [!TIP]
> Pour obtenir une procédure pas à pas d’utilisation du connecteur Azure Data Lake Store, consultez [Charger des données dans Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques d’Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Azure Data Lake Store :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété `type` doit être définie sur **AzureDataLakeStore**. | Oui |
| dataLakeStoreUri | Informations à propos du compte Azure Data Lake Store. Cette information prend un des formats suivants : `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | OUI |
| subscriptionId | ID de l’abonnement Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| nom_groupe_ressources | Nom du groupe de ressources Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d’intégration Azure ou le runtime d’intégration auto-hébergé (si votre magasin de données se trouve dans un réseau privé). Si cette propriété n’est pas spécifiée, elle utilise le runtime d’intégration Azure par défaut. |Non  |

### <a name="use-service-principal-authentication"></a>Utiliser une authentification de principal de service

Pour utiliser une authentification du principal de service, inscrivez une entité d’application dans Azure Active Directory et donnez-lui accès à Data Lake Store. Consultez la page [Authentification de service à service](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) pour des instructions détaillées. Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

- ID de l'application
- Clé de l'application
- ID client

>[!IMPORTANT]
> Veillez à accorder l’autorisation appropriée au principal de service dans Data Lake Store :
>- **En tant que source** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Lecture + Exécution** pour lister et copier les fichiers dans les dossiers et sous-dossiers. Ou, vous pouvez accorder l’autorisation **Lecture** pour copier un seul fichier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Il n’existe aucune exigence sur le contrôle d’accès au niveau du compte (IAM).
>- **En tant que récepteur** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Écriture + Exécution** pour créer des éléments enfants dans le dossier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez le runtime d’intégration Azure pour effectuer la copie (la source et le récepteur sont tous les deux dans le cloud), dans la page IAM, attribuez au moins le rôle **Lecteur** pour permettre à Data Factory de détecter la région de Data Lake Store. Pour éviter ce rôle IAM, vous devez explicitement [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) avec l’emplacement de Data Lake Store. Associez-les dans le service lié Data Lake Store comme dans l’exemple suivant.

>[!NOTE]
>Pour lister les dossiers à partir de la racine, vous devez définir l’autorisation accordée au principal de service **au niveau racine avec une autorisation « Exécution »**. Cette définition s’avère nécessaire quand vous utilisez :
>- L’**outil Copier des données** pour créer le pipeline de copie.
>- L’**interface utilisateur de Data Factory** pour tester la connexion et parcourir les dossiers lors de la création.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que `SecureString` afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | OUI |

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

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité de service pour l’authentification Data Lake Store, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données et de les copier depuis ou vers votre Data Lake Store.

Pour utiliser les identités managées afin d’authentifier les ressources Azure :

1. [Récupérez l’identité de service de la fabrique de données](data-factory-service-identity.md#retrieve-service-identity) en copiant la valeur « ID d’application de l’identité du service » générée en même temps que votre fabrique.
2. Accordez l’accès Data Lake Store au service d’identité comme vous le feriez pour un principal de service en suivant ces remarques.

>[!IMPORTANT]
> Veillez à accorder l’autorisation appropriée à l’identité de service de la fabrique de données dans Data Lake Store :
>- **En tant que source** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Lecture + Exécution** pour lister et copier les fichiers dans les dossiers et sous-dossiers. Ou, vous pouvez accorder l’autorisation **Lecture** pour copier un seul fichier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Il n’existe aucune exigence sur le contrôle d’accès au niveau du compte (IAM).
>- **En tant que récepteur** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Écriture + Exécution** pour créer des éléments enfants dans le dossier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez le runtime d’intégration Azure pour effectuer la copie (la source et le récepteur sont tous les deux dans le cloud), dans la page IAM, attribuez au moins le rôle **Lecteur** pour permettre à Data Factory de détecter la région de Data Lake Store. Pour éviter ce rôle IAM, vous devez explicitement [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) avec l’emplacement de Data Lake Store. Associez-les dans le service lié Data Lake Store comme dans l’exemple suivant.

>[!NOTE]
>Pour lister les dossiers à partir de la racine, vous devez définir l’autorisation accordée au principal de service **au niveau racine avec une autorisation « Exécution »**. Cette définition s’avère nécessaire quand vous utilisez :
>- L’**outil Copier des données** pour créer le pipeline de copie.
>- L’**interface utilisateur de Data Factory** pour tester la connexion et parcourir les dossiers lors de la création.

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

Pour copier des données depuis et vers Azure Data Lake Store, définissez la propriété `type` du jeu de données sur **AzureDataLakeStoreFile**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **AzureDataLakeStoreFile** |Oui |
| folderPath | Chemin vers le dossier dans Data Lake Store. Le filtre de caractères génériques n'est pas pris en charge. Si non spécifié, il pointe vers la racine. Exemple : dossierracine/sous-dossier / |Non  |
| fileName | **Filtre de nom ou de caractère générique** pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom de fichier suivant ce modèle : « *Data.[GUID d’exécution d’activité].[GUID si FlattenHierarchy].[format si configuré].[compression si configurée]*  ». Par exemple, « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». Si vous effectuez la copie à partir de sources tabulaires à l’aide d’un nom de table au lieu d’une requête, le modèle du nom est «  *[nom_table].[format].[compression si configurée]*  ». Par exemple, « MaTable.csv ». |Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de formats de fichiers suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non  |


>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul fichier portant un nom particulier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le filtre de caractères génériques. 

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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Data Lake Store en tant que source et récepteur.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store en tant que source

Pour copier des données depuis Data Lake Store, définissez **AzureDataLakeStoreSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété `type` de la source d’activité de copie doit être définie sur : **AzureDataLakeStoreSource**. |Oui |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option `recursive` est définie sur true et que le récepteur est un magasin basé sur un fichier, aucun dossier ou sous-dossier vide n’est copié ou créé au niveau du récepteur. Les valeurs autorisées sont : **true** (par défaut) et **false**. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

Pour copier des données vers Data Lake Store, définissez **AzureDataLakeStoreSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété `type` du récepteur d’activité de copie doit être définie sur : **AzureDataLakeStoreSink**. |Oui |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Le nom des fichiers cibles est généré automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom d’objet blob/de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemples de comportement de l’opération de copie

Cette section décrit le comportement obtenu de l’opération de copie avec différentes combinaisons de valeurs `recursive` et `copyBehavior`.

| recursive | copyBehavior | Structure du dossier source | Cible obtenue |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le Dossier1 cible est créé et structuré de la même manière que la source :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5. |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un seul fichier portant un nom généré automatiquement. |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec le nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez les [magasins de données pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
