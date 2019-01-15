---
title: Copier des données de sources OData à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de sources OData vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: ed861aa914da999bdb2922bc309f05d1234ef416
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018481"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Copier des données d’une source OData à l’aide d’Azure Data Factory

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [Version actuelle](connector-odata.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier les données d’une source OData. Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données d’une source OData dans tout magasin de données récepteur pris en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur OData prend en charge ce qui suit :

- OData version 3.0 et 4.0.
- Copie de données avec une des authentifications suivantes : **Anonyme**, **De base**, **Windows**, **Principal de service AAD** et **Identités managées pour ressources Azure**.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur OData.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié OData sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **OData**. |Oui |
| url | URL racine du service OData. |Oui |
| authenticationType | Type d’authentification utilisé pour se connecter à la source OData. Les valeurs autorisées sont **Anonyme**, **De base**, **Windows**, **AadServicePrincipal** et **Managed Service Identity**. L’authentification OAuth par utilisateur n’est pas prise en charge. | Oui |
| userName | Si vous utilisez l’authentification De base ou Windows, spécifiez un **nom d’utilisateur**. | Non  |
| password | Spécifiez le **mot de passe** associé au **nom d’utilisateur spécifié**. Vous pouvez marquer ce champ en tant que type **SecureString** pour le stocker de manière sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| servicePrincipalId | Spécifiez l’ID de l’application Azure Active Directory. | Non  |
| aadServicePrincipalCredentialType | Spécifiez le type d’informations d’identification à utiliser pour l’authentification de principal du service. Valeurs autorisées : `ServicePrincipalKey` ou `ServicePrincipalCert`. | Non  |
| servicePrincipalKey | Spécifiez la clé de l’application Azure Active Directory. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| servicePrincipalEmbeddedCert | Spécifiez le certificat codé en base64 de l’application inscrite dans Azure Active Directory. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| servicePrincipalEmbeddedCertPassword | Spécifiez le mot de passe de votre certificat si votre certificat est sécurisé par un mot de passe. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md).  | Non |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-le en pointant la souris dans le coin supérieur droit du Portail Azure. | Non  |
| aadResourceId | Spécifiez la ressource AAD pour laquelle vous demandez une autorisation.| Non  |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Vous pouvez choisir Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre magasin de données se trouve dans un réseau privé. À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non  |

**Exemple 1 : Utilisation de l’authentification anonyme**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : Utilisation de l’authentification de base**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Exemple 3 : Utilisation de l’authentification Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Exemple 4 : Utilisation de l’authentification de la clé du principal de service**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Exemple 5 : Utilisation de l’authentification du certificat du principal de service**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Cette section contient la liste des propriétés prises en charge par le jeu de données OData.

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). 

Pour copier des données à partir d’OData, définissez la propriété **type** du jeu de données sur **ODataResource**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du jeu de données doit être définie sur **ODataResource**. | Oui |
| chemin d’accès | Chemin de la ressource OData. | Oui |

**Exemple**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section contient la liste des propriétés prises en charge par la source OData.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData en tant que source

Pour copier des données à partir d’OData, définissez le type **source** de l’activité de copie sur **RelationalSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source d’activité de copie doit être définie sur **RelationalSource**. | Oui |
| query | Options de requête OData pour filtrer les données. Exemple : `"?$select=Name,Description&$top=5"`.<br/><br/>**Remarque**: Le connecteur OData copie des données à partir de l’URL combinée `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Pour plus d’informations, consultez [OData URL components](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Non  |

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "RelationalSource",
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Mappage de type de données pour OData

Lorsque vous copiez des données à partir d’OData, les mappages suivants sont utilisés entre les types de données OData et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données sources au récepteur, consultez [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de données OData | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Décimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Chaîne |
| Edm.Time | intervalle de temps |
| Edm.DateTimeOffset | DatetimeOffset |

> [!NOTE]
> Les types de données complexes OData (par exemple, **Object**), ne sont pas pris en charge.


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
