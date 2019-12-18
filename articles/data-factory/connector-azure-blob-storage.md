---
title: Copier et transformer des données dans Stockage Blob Azure
description: Découvrez comment copier des données vers et depuis un stockage Blob, et comment transformer les données dans un stockage Blob en utilisant Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: 3c6b6a403919c3545ec000a701ff31044343f73e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929948"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Copier et transformer des données dans un stockage Azure Blob à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Version actuelle](connector-azure-blob-storage.md)

Cet article indique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis et vers un stockage Azure Blob, et utiliser Data Flow pour transformer les données dans un stockage Azure Blob. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Blob Azure est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Mappage de flux de données](concepts-data-flow-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)
- [Supprimer l’activité](delete-activity.md)

Pour cette activité de copie, ce connecteur de stockage Blob prend en charge ce qui suit :

- La copie d’objets blob vers et depuis des comptes de stockage Azure à usage général et un stockage d’objets blob à chaud ou à froid. 
- La copie d’objets blob à l’aide d’une clé de compte, d’une signature d’accès partagé de service ou d’identités managées pour les authentifications de ressources Azure.
- La copie d’objets blob à partir d’objets blob de blocs, d’ajout ou de page, et la copie de données uniquement vers des objets blob de blocs.
- La copie d’objets blob en l’état ou l’analyse ou la génération d’objets blob avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

>[!IMPORTANT]
>Si vous activez l’option **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** dans les paramètres du pare-feu Stockage Azure et que vous souhaitez utiliser le runtime d’intégration Azure pour vous connecter à votre stockage d’objets blob, vous devez utiliser une [authentification d’identité managée](#managed-identity).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Azure Data Factory spécifiques au stockage Blob.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le connecteur d’objets Blob Azure prend en charge les types d’authentification ci-dessous. Pour en savoir plus, reportez-vous à la section correspondante :

- [Authentification par clé de compte](#account-key-authentication)
- [Authentification avec une signature d’accès partagé](#shared-access-signature-authentication)
- [Authentification d’un principal du service](#service-principal-authentication)
- [Identités managées pour authentifier les ressources Azure](#managed-identity)

>[!NOTE]
>Lorsque vous utilisez PolyBase pour charger des données dans SQL Data Warehouse, si votre source ou votre stockage Blob intermédiaire sont configurée avec le point de terminaison de réseau virtuel, vous devez utiliser l’authentification par identité managée comme requis par PolyBase, et utiliser le runtime d’intégration auto-hébergé version 3.18 ou supérieure. Pour en savoir plus sur la configuration requise, voir la section sur l’[authentification par identité managée](#managed-identity).

>[!NOTE]
>Les activités HDInsights et Azure Machine Learning prennent en charge uniquement l’authentification par clé de compte de stockage Blob Azure.

### <a name="account-key-authentication"></a>Authentification par clé de compte

Pour l’authentification par clé de compte de stockage, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureBlobStorage** (recommandé) ou **AzureStorage** (voir les remarques ci-dessous). |OUI |
| connectionString | Spécifiez les informations requises pour la connexion au stockage pour la propriété connectionString. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également définir une clé de compte dans Azure Key Vault et extraire la configuration `accountKey` de la chaîne de connexion. Reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md) pour plus de détails. |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

>[!NOTE]
>Le point de terminaison de service d’objet blob secondaire n’est pas pris en charge lors de l’utilisation de l’authentification de clé du compte. Vous pouvez utiliser d’autres types d’authentification.

>[!NOTE]
>Si vous utilisiez déjà le service lié de type « AzureStorage », celui-ci est toujours pris en charge en l’état. Toutefois, vous êtes encouragé à passer au nouveau service lié de type « AzureBlobStorage ».

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage de la clé de compte dans Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Authentification avec une signature d’accès partagé

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Vous pouvez utiliser une signature d’accès partagé pour octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée. Vous n’êtes pas obligé de partager vos clés d’accès de compte. La signature d’accès partagé est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour obtenir un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d’accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié. Pour plus d’informations sur les signatures d’accès partagé, consultez [Signatures d’accès partagé : Comprendre le modèle de signature d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Azure Data Factory prend désormais en charge les **signatures d’accès partagé de service** et les **signatures d’accès partagé de compte**. Pour plus d’informations sur les signatures d’accès partagé, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../storage/common/storage-sas-overview.md).
>- Dans une configuration ultérieure de jeu de données, le chemin du dossier est le chemin absolu commençant au niveau conteneur. Vous devez en configurer un qui soit aligné avec le chemin dans votre URI SAS.

> [!TIP]
> Pour générer une signature d’accès partagé de service pour votre compte de stockage, vous pouvez exécuter les commandes PowerShell suivantes. Remplacez les espaces réservés et octroyez l’autorisation nécessaire.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Pour l’authentification par signature d’accès partagé, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureBlobStorage** (recommandé) ou **AzureStorage** (voir les remarques ci-dessous). |OUI |
| sasUri | Spécifiez l’URI de signature d’accès partagé des ressources de stockage, telles qu’un objet blob/conteneur. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également placer un jeton SAS dans Azure Key Vault pour activer la rotation automatique et supprimer la partie du jeton. Pour plus d’informations, reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

>[!NOTE]
>Si vous utilisiez déjà le service lié de type « AzureStorage », celui-ci est toujours pris en charge en l’état. Toutefois, vous êtes encouragé à passer au nouveau service lié de type « AzureBlobStorage ».

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage de la clé de compte dans Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lorsque vous créez un URI de signature d’accès partagé, prenez en compte les points suivants :

- Définissez des autorisations de lecture/écriture appropriées sur les objets en fonction de l’utilisation du service lié (lecture, écriture, lecture/écriture) dans votre fabrique de données.
- Définissez le paramètre **Heure d’expiration** correctement. Assurez-vous que l’accès aux objets du stockage n’expire pas pendant la période active du pipeline.
- L’URI doit être créé au niveau objet blob/conteneur approprié en fonction des besoins. Un URI de signature d’accès partagé à un objet blob permet à Azure Data Factory d’accéder à cet objet blob particulier. Un URI de signature d’accès partagé à un conteneur de stockage Blob permet à Azure Data Factory d’itérer via des objets blob dans ce conteneur. Pour fournir l’accès à plus ou moins d’objets ultérieurement ou mettre à jour l’URI de signature d’accès partagé, rappelez-vous de mettre à jour le service lié avec le nouvel URI.

### <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour des informations générales sur l’authentification de principal de service du stockage Azure, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory](../storage/common/storage-auth-aad.md).

Pour l’authentification de principal de service, effectuez les étapes suivantes :

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accordez l’autorisation appropriée au principal de service dans le Stockage Blob Azure : Pour plus d’informations sur les rôles, consultez [Gérer les droits d’accès aux données du Stockage Azure avec RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Lecteur des données Blob du stockage** .
    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Contributeur aux données Blob du stockage**.

Les propriétés prises en charge pour un service lié de Stockage Blob Azure sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureBlobStorage**. |OUI |
| serviceEndpoint | Spécifiez le point de terminaison du service Stockage Blob Azure à l’aide du modèle suivant : `https://<accountName>.blob.core.windows.net/`. |OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du Portail Azure. | OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

>[!NOTE]
>L’authentification du principal du service est uniquement prise en charge par le service lié de type « AzureBlobStorage », et non par l’ancien service lié de type « AzureStorage ».

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

Une fabrique de données peut être associée à une [identité managée pour les ressources Azure](data-factory-service-identity.md), laquelle représente cette même fabrique de données. Vous pouvez utiliser directement cette identité managée pour l’authentification Stockage Blob, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données, et de les copier à partir de votre stockage Blob ou dans celui-ci.

Pour obtenir des informations générales sur l’authentification du Stockage Azure, consultez [Authentifier l’accès au Stockage Azure à l’aide d’Azure Active Directory](../storage/common/storage-auth-aad.md). Pour utiliser les identités managées afin d’authentifier les ressources Azure, procédez comme suit :

1. [Récupérez les informations d’identité managée de la fabrique de données](data-factory-service-identity.md#retrieve-managed-identity) en copiant la valeur « SERVICE IDENTITY APPLICATION ID » générée en même temps que votre fabrique.

2. Accordez l’autorisation nécessaire à l’identité managée dans le Stockage Blob Azure. Pour plus d’informations sur les rôles, consultez [Gérer les droits d’accès aux données du Stockage Azure avec RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Lecteur des données Blob du stockage** .
    - **Comme source**, dans le contrôle d’accès (IAM), accordez au moins un rôle **Contributeur aux données Blob du stockage**.

>[!IMPORTANT]
>Si vous utilisez PolyBase pour charger des données à partir de Blob (en tant que source ou stockage intermédiaire) dans SQL Data Warehouse, lors de l’utilisation de l’authentification d’identité managée pour Blob, veillez à suivre également les étapes 1 et 2 de [ce guide](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) pour 1) inscrire votre serveur SQL Database auprès d’Azure Active Directory (Azure AD) et pour 2) attribuer le rôle de contributeur aux données Blob Storage à votre serveur SQL Database. Le reste est géré par Data Factory. Si votre instance stockage Blob est configuré avec un point de terminaison de réseau virtuel Azure, pour utiliser PolyBase à partir de cet emplacement, vous devez utiliser l’authentification par identité managée comme requis par PolyBase.

Les propriétés prises en charge pour un service lié de Stockage Blob Azure sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureBlobStorage**. |OUI |
| serviceEndpoint | Spécifiez le point de terminaison du service Stockage Blob Azure à l’aide du modèle suivant : `https://<accountName>.blob.core.windows.net/`. |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

> [!NOTE]
> Les identités managées pour l’authentification des ressources Azure sont uniquement prises en charge par le service lié de type « AzureBlobStorage », et non par l’ancien service lié de type « AzureStorage ». 

**Exemple :**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

Les propriétés suivantes sont prises en charge pour les objets Blob Azure sous les paramètres `location` dans le jeu de données basé sur le format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propriété type de l’emplacement du jeu de données doit être définie sur **AzureBlobStorageLocation**. | OUI      |
| conteneur  | Le conteneur d’objets blob.                                          | OUI      |
| folderPath | Le chemin d’accès au dossier sous le conteneur donné. Si vous souhaitez utiliser un caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Le nom de fichier sous le conteneur donné + folderPath. Si vous souhaitez utiliser un caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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
| type | La propriété de type du jeu de données doit être définie sur **AzureBlob**. |OUI |
| folderPath | Chemin d'accès au conteneur et au dossier dans le stockage des objets Blobs. <br/><br/>Le filtre de caractères génériques est pris en charge pour le chemin à l’exclusion du nom du conteneur. Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br/><br/>Exemples : conteneur_blob/dossier_blob/ ; consultez d’autres exemples dans [Exemples de filtres de dossier et de fichier](#folder-and-file-filter-examples). |Oui pour la copie/Activité Lookup/Non pour l’activité GetMetadata |
| fileName | **Filtre de nom ou de caractères génériques** pour les objets blob sous le "folderPath" spécifié. Si vous ne spécifiez pas de valeur pour cette propriété, le jeu de données pointe vers tous les objets blob du dossier. <br/><br/>Dans le filtre, les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plus) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"fileName": "*.csv"`<br/>- Exemple 2 : `"fileName": "???20180427.txt"`<br/>Utilisez `^` comme caractère d’échappement si votre nom de fichier réel contient des caractères génériques ou ce caractère d’échappement.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom d’objet blob selon le modèle suivant : « *Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* », par exemple « Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz ». Si c’est le nom de la table et non la requête qui est utilisé pour la copie à partir d’une source tabulaire, le modèle de nom est le suivant : « *[nom de la table].[format].[compression si configurée]* », par exemple « MyTable.csv ». |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Sachez que les performances globales du déplacement des données sont affectées par l’activation de ce paramètre lorsque vous souhaitez filtrer des fichiers parmi de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Sachez que les performances globales du déplacement des données sont affectées par l’activation de ce paramètre lorsque vous souhaitez filtrer des fichiers parmi de grandes quantités de fichiers. <br/><br/> Les propriétés peuvent être NULL, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| format | Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous **Format** sur l’une de ces valeurs. Pour en savoir plus, voir les sections [Format Text](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [Format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non |

>[!TIP]
>Pour copier tous les objets blob d’un dossier, spécifiez **folderPath** uniquement.<br>Pour copier un seul objet blob avec un nom donné, spécifiez **folderPath** avec la partie dossier et **fileName** avec le nom du fichier.<br>Pour copier un sous-ensemble d’objets blob d’un dossier, spécifiez **folderPath** avec la partie dossier et **fileName** avec le filtre de caractères génériques. 

**Exemple :**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur du stockage Blob.

### <a name="blob-storage-as-a-source-type"></a>Stockage Blob en tant que type de source

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour les objets Blob Azure sous les paramètres `storeSettings` dans la source de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **AzureBlobStorageReadSetting**. | OUI                                           |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                            |
| wildcardFolderPath       | Le chemin d’accès du dossier avec des caractères génériques sous le conteneur donné configuré dans le jeu de données pour filtrer les dossiers de source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                            |
| wildcardFileName         | Le nom du fichier avec des caractères génériques sous le conteneur donné + folderPath/wildcardFolderPath pour filtrer les dossiers de source. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` n’est pas spécifié dans le jeu de données |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                            |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                            |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                            |

> [!NOTE]
> Pour les formats Parquet et de texte délimité, la source de l’activité de copie de type **BlobSource** mentionnée dans la section suivante est toujours prise en charge pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle partir de maintenant, et l’IU de création ADF peut désormais générer ces nouveaux types.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSetting",
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
| type | La propriété de type de la source d’activité de copie doit être définie sur **BlobSource**. |OUI |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur.<br/>Les valeurs autorisées sont **true** (par défaut) et **false**. | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Stockage Blob en tant que type de récepteur

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Les propriétés suivantes sont prises en charge pour les objets Blob Azure sous les paramètres `storeSettings` dans le récepteur de copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propriété type sous `storeSettings` doit être définie sur **AzureBlobStorageWriteSetting**. | OUI      |
| copyBehavior             | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom d’objet blob ou de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non       |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non       |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSetting",
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
| type | La propriété de type du récepteur d’activité de copie doit être définie sur **BlobSink**. |OUI |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Les noms des fichiers cibles sont générés automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom d’objet blob ou de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. | Non |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage. Spécifiez-le uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
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
| `container/Folder*` | (vide, utiliser la valeur par défaut) | false | conteneur<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `container/Folder*` | (vide, utiliser la valeur par défaut) | true | conteneur<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `container/Folder*` | `*.csv` | false | conteneur<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| `container/Folder*` | `*.csv` | true | conteneur<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |

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

## <a name="mapping-data-flow-properties"></a>Propriétés du mappage de flux de données

Découvrez plus de détails sur la [transformation de la source](data-flow-source.md) et la [transformation du récepteur](data-flow-sink.md) dans la section sur le mappage de flux de données.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriétés de l’activité GetMetadata

Pour en savoir plus sur les propriétés, consultez [Activité GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propriétés de l’activité Delete

Pour en savoir plus sur les propriétés, consultez [Activité Delete](delete-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
