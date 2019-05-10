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
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 524842c81380079906c4f8e0a523dfd13f83509d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149724"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [Version actuelle](connector-azure-data-lake-store.md)

Cet article explique comment copier des données vers et depuis Azure Data Lake Storage Gen1 (Gen1 ADLS). Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Azure Data Lake Storage Gen1 est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [pris en charge de la matrice de source/récepteur](copy-activity-overview.md)
- [Flux de données de mappage](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)

Plus précisément, ce connecteur prend en charge ce qui suit :

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
| type | La propriété `type` doit être définie sur **AzureDataLakeStore**. | Oui |
| dataLakeStoreUri | Informations à propos du compte Azure Data Lake Store. Cette information prend un des formats suivants : `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | OUI |
| subscriptionId | ID de l’abonnement Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| resourceGroupName | Nom du groupe de ressources Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser le runtime d’intégration Azure ou le runtime d’intégration auto-hébergé (si votre magasin de données se trouve dans un réseau privé). Si cette propriété n’est pas spécifiée, elle utilise le runtime d’intégration Azure par défaut. |Non  |

### <a name="use-service-principal-authentication"></a>Utiliser une authentification de principal de service

Pour utiliser une authentification du principal de service, inscrivez une entité d’application dans Azure Active Directory et donnez-lui accès à Data Lake Store. Consultez la page [Authentification de service à service](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) pour des instructions détaillées. Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

- ID de l'application
- Clé de l'application
- ID client

>[!IMPORTANT]
> Veillez à accorder l’autorisation appropriée au principal de service dans Data Lake Store :
>- **En tant que source** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Lecture + Exécution** pour lister et copier les fichiers dans les dossiers et sous-dossiers. Ou, vous pouvez accorder l’autorisation **Lecture** pour copier un seul fichier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Il n’existe aucune exigence sur le contrôle d’accès au niveau du compte (IAM).
>- **En tant que récepteur** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Écriture + Exécution** pour créer des éléments enfants dans le dossier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez le runtime d’intégration Azure pour effectuer la copie (la source et le récepteur sont tous les deux dans le cloud), dans la page IAM, attribuez au moins le rôle **Lecteur** pour permettre à Data Factory de détecter la région de Data Lake Store. Pour éviter ce rôle IAM, vous devez explicitement [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) avec l’emplacement de Data Lake Store. Par exemple, si votre Store lac de données se trouve en Europe de l’ouest, vous pouvez créer un runtime d’intégration d’Azure avec l’emplacement défini pour « Europe de l’ouest ». Associez-les dans le service Data Lake Store lié comme dans l’exemple suivant.

>[!NOTE]
>Pour lister les dossiers à partir de la racine, vous devez définir l’autorisation accordée au principal de service **au niveau racine avec une autorisation « Exécution »**. Cette définition s’avère nécessaire quand vous utilisez :
>- L’**outil Copier des données** pour créer le pipeline de copie.
>- L’**interface utilisateur de Data Factory** pour tester la connexion et parcourir les dossiers lors de la création.
>Si vous avez le problème sur l’octroi d’autorisations au niveau racine, vous pouvez ignorer tester la connexion et le chemin d’accès d’entrée manuellement lors de la création. Activité de copie continue de fonctionner tant que le principal du service est accordée avec l’autorisation appropriée sur les fichiers à copier.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que `SecureString` afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
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

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité gérée pour l’authentification Data Lake Store, similaire à l’utilisation de votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données et de les copier depuis ou vers votre Data Lake Store.

Pour utiliser les identités managées afin d’authentifier les ressources Azure :

1. [Récupérer les informations d’identité de données managé factory](data-factory-service-identity.md#retrieve-managed-identity) en copiant la valeur de le « ID identité du Service Application » généré en même temps que votre fabrique.
2. Accorder l’accès de l’identité gérée à Data Lake Store, de la même façon que vous le feriez pour un principal de service, suivez ces notes.

>[!IMPORTANT]
> Veillez à qu'accorder l’autorisation appropriée identité data factory géré dans Data Lake Store :
>- **En tant que source** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Lecture + Exécution** pour lister et copier les fichiers dans les dossiers et sous-dossiers. Ou, vous pouvez accorder l’autorisation **Lecture** pour copier un seul fichier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Il n’existe aucune exigence sur le contrôle d’accès au niveau du compte (IAM).
>- **En tant que récepteur** : Dans **Explorateur de données** > **Accès**, accordez au moins l’autorisation **Écriture + Exécution** pour créer des éléments enfants dans le dossier. Vous pouvez choisir d’ajouter l’autorisation à **ce dossier et tous ses enfants** pour les récurrences et de l’ajouter en tant qu’**autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez le runtime d’intégration Azure pour effectuer la copie (la source et le récepteur sont tous les deux dans le cloud), dans la page IAM, attribuez au moins le rôle **Lecteur** pour permettre à Data Factory de détecter la région de Data Lake Store. Pour éviter ce rôle IAM, vous devez explicitement [créer un runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) avec l’emplacement de Data Lake Store. Associez-les dans le service lié Data Lake Store comme dans l’exemple suivant.

>[!NOTE]
>À la liste des dossiers à partir de la racine, vous devez définir l’autorisation de l’identité gérée accordée à **au niveau racine avec l’autorisation « Execute »**. Cette définition s’avère nécessaire quand vous utilisez :
>- L’**outil Copier des données** pour créer le pipeline de copie.
>- L’**interface utilisateur de Data Factory** pour tester la connexion et parcourir les dossiers lors de la création.
>Si vous avez le problème sur l’octroi d’autorisations au niveau racine, vous pouvez ignorer tester la connexion et le chemin d’accès d’entrée manuellement lors de la création. Activité de copie continue de fonctionner tant que l’identité gérée est accordée avec l’autorisation appropriée sur les fichiers à copier.

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

- Pour **Parquet et le format de texte délimité**, reportez-vous à [jeu de données de format Parquet et texte délimité](#parquet-and-delimited-text-format-dataset) section.
- Pour les autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autre jeu de données de format](#other-format-dataset) section.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet et le jeu de données de format texte délimité

Pour copier des données vers et à partir de la génération 1 ADLS dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) article sur le jeu de données en fonction du format et paramètres pris en charge. Les propriétés suivantes sont prises en charge pour la génération 1 ADLS sous `location` paramètres dans le jeu de données en fonction du format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **AzureDataLakeStoreLocation**. | Oui      |
| folderPath | Le chemin d’accès au dossier. Si vous souhaitez utiliser le caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez dans les paramètres de source d’activité. | Non        |
| fileName   | Le nom du fichier sous le paramètre folderPath donné. Si vous souhaitez utiliser le caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez dans les paramètres de source d’activité. | Non        |

> [!NOTE]
>
> **AzureDataLakeStoreFile** type de jeu de données avec le format Parquet/texte mentionné dans la section suivante est toujours prise en charge-concerne l’activité de copie/recherche/obtention des métadonnées pour la compatibilité descendante, mais il ne fonctionne pas avec mappage de flux de données. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

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

### <a name="other-format-dataset"></a>Autre jeu de données de format

Pour copier des données vers et à partir de la génération 1 ADLS dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **AzureDataLakeStoreFile** |Oui |
| folderPath | Chemin vers le dossier dans Data Lake Store. Si non spécifié, il pointe vers la racine. <br/><br/>Le filtre de caractères génériques est pris en charge, et les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` comme caractère d'échappement si le nom réel de votre dossier contient des caractères génériques ou ce caractère d'échappement. <br/><br/>Exemples : dossier_racine/sous-dossier/ ; consultez d’autres exemples dans [Exemples de filtres de dossier et de fichier](#folder-and-file-filter-examples). |Non  |
| fileName | **Filtre de nom ou de caractère générique** pour les fichiers sous le « folderPath » spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom de fichier suivant ce modèle : «*Données. [activité exécutée ID GUID]. [GUID si FlattenHierarchy]. [format si configuré]. [la compression si configuré]* ". Par exemple, « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». Si vous effectuez la copie à partir de sources tabulaires à l’aide d’un nom de table au lieu d’une requête, le modèle du nom est «  *[nom_table].[format].[compression si configurée]*  ». Par exemple, « MaTable.csv ». |Non  |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> N’oubliez pas que les performances globales du déplacement des données seront affectés par l’activation de ce paramètre lorsque vous souhaitez des filtres de fichiers à partir de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL qui signifie qu'aucun filtre d’attribut de fichier ne sera appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> N’oubliez pas que les performances globales du déplacement des données seront affectés par l’activation de ce paramètre lorsque vous souhaitez des filtres de fichiers à partir de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL qui signifie qu'aucun filtre d’attribut de fichier ne sera appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non  |


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

- Pour la copie depuis **Parquet et le format de texte délimité**, reportez-vous à [Parquet et source de format de texte délimité](#parquet-and-delimited-text-format-source) section.
- Pour copier à partir d’autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autre source de format](#other-format-source) section.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet et source de format de texte délimité

Pour copier des données à partir de la génération 1 ADLS dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) article sur la source d’activité de copie basée sur le format et paramètres pris en charge. Les propriétés suivantes sont prises en charge pour la génération 1 ADLS sous `storeSettings` paramètres de source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propriété de type sous `storeSettings` doit être définie sur **AzureDataLakeStoreReadSetting**. | Oui                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                             |
| wildcardFolderPath       | Le chemin d’accès de dossier avec des caractères génériques pour filtrer les dossiers de code source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                             |
| wildcardFileName         | Le nom de fichier avec des caractères génériques sous folderPath/wildcardFolderPath donné pour filtrer les fichiers source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                             |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                             |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de stockage simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                             |

> [!NOTE]
> Pour Parquet/texte délimité par des **AzureDataLakeStoreSource** source d’activité de copie type mentionné dans la section suivante est toujours prise en charge-concerne pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

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

#### <a name="other-format-source"></a>Autre source de format

Pour copier des données à partir de la génération 1 ADLS dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge dans l’activité de copie **source** section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété `type` de la source d’activité de copie doit être définie sur : **AzureDataLakeStoreSource**. |Oui |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option `recursive` est définie sur true et que le récepteur est un magasin basé sur un fichier, aucun dossier ou sous-dossier vide n’est copié ou créé au niveau du récepteur. Les valeurs autorisées sont : **true** (par défaut) et **false**. | Non  |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de données simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non  |

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

- Pour copier dans **Parquet et le format de texte délimité**, reportez-vous à [Parquet et le récepteur de format de texte délimité](#parquet-and-delimited-text-format-sink) section.
- Pour la copie dans d’autres formats tels que **format ORC/Avro/JSON/binaire**, reportez-vous à [autres récepteur format](#other-format-sink) section.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet et le récepteur de format de texte délimité

Pour copier des données à Gen1 ADLS dans **Parquet ou format de texte délimité**, reportez-vous à [format Parquet](format-parquet.md) et [format de texte délimité](format-delimited-text.md) article sur le récepteur d’activité de copie basée sur le format et paramètres pris en charge. Les propriétés suivantes sont prises en charge pour la génération 1 ADLS sous `storeSettings` paramètres dans le récepteur de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propriété de type sous `storeSettings` doit être définie sur **AzureDataLakeStoreWriteSetting**. | Oui      |
| copyBehavior             | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non        |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de données simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non        |

> [!NOTE]
> Pour Parquet/texte délimité par des **AzureDataLakeStoreSink** récepteur d’activité de copie type mentionné dans la section suivante est toujours prise en charge-concerne pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à l’avenir et ADF création de l’interface utilisateur est passée à la génération de ces nouveaux types.

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

#### <a name="other-format-sink"></a>Autres récepteur de format

Pour copier des données à Gen1 ADLS dans **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge dans les **récepteur** section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété `type` du récepteur d’activité de copie doit être définie sur : **AzureDataLakeStoreSink**. |Oui |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin relatif du fichier source vers le dossier source est identique au chemin relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Le nom des fichiers cibles est généré automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement. | Non  |
| maxConcurrentConnections | Nombre de connexions pour se connecter au magasin de données simultanément. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non  |

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
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le Dossier1 cible est créé et structuré de la même manière que la source :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5. |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un seul fichier portant un nom généré automatiquement. |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec le nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="mapping-data-flow-properties"></a>Mappage de propriétés de flux de données

Découvrez plus de détails à partir de [transformation source](data-flow-source.md) et [récepteur transformation](data-flow-sink.md) dans le mappage de flux de données.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez les [magasins de données pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
