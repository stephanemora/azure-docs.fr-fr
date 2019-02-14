---
title: Copier des données depuis/vers Azure Data Explorer à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment copier des données vers ou depuis Azure Data Explorer à l’aide d’une activité de copie dans un pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: orspod
ms.openlocfilehash: 8f2a7a953ce2964645c281d9454a73b0cf1a8ff6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747186"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copier des données depuis/vers Azure Data Explorer à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers ou depuis [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de tout magasin de données source pris en charge vers Azure Data Explorer. Vous pouvez également copier des données à partir d’Azure Data Explorer vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md).

>[!NOTE]
>Actuellement, la copie de données vers/depuis Azure Data Explorer depuis/vers un magasin de données local à l’aide d’un runtime d’intégration auto-hébergé n’est pas encore pris en charge.

Le connecteur Azure Data Explorer vous permet d’effectuer les opérations suivantes :

* Copier des données en utilisant une authentification de jeton d’application Azure Active Directory (Azure AD) avec un **principal du service**.
* En tant que source, récupérer des données à l’aide d’une requête KQL (Kusto).
* En tant que récepteur, ajouter des données à une table de destination.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques du connecteur Azure Data Explorer.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Azure Data Explorer :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **AzureDataExplorer**. | OUI |
| endpoint | URL de point de terminaison du cluster Azure Data Explorer, avec le format `https://<clusterName>.<regionName>.kusto.windows.net `. | OUI |
| database | Nom de base de données. | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Récupérez-les en pointant la souris dans l’angle supérieur droit du portail Azure. | OUI |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |

**Exemple de Propriétés du service lié :**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Data Explorer.

Pour copier des données vers Azure Data Explorer, définissez la propriété type du jeu de données sur **AzureDataExplorerTable**.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **AzureDataExplorerTable**. | OUI |
| table | Nom de la table dans la base à laquelle le service lié fait référence. | Oui pour le récepteur, Non pour la source |

**Exemple de propriétés du jeu de données**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Data Explorer en tant que source et récepteur.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer en tant que source

Pour copier des données à partir d’Azure Data Explorer, définissez la propriété **type** dans la source d’activité de copie sur **AzureDataExplorerSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source d’activité de copie doit être définie sur : **AzureDataExplorerSource** | OUI |
| query | Requête en lecture seule au [format KQL](/azure/kusto/query/). Utilisez la requête KQL personnalisée en tant que référence. | OUI |
| queryTimeout | Temps d’attente avant l’expiration de la demande de requête. La valeur par défaut est 10 minutes (00:10:00) et la valeur maximale autorisée 1 heure (01:00:00). | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer en tant que récepteur

Pour copier des données vers Azure Data Explorer, définissez la propriété type dans le récepteur d’activité de copie sur **AzureDataExplorerSink**. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du récepteur d’activité de copie doit être définie sur : **AzureDataExplorerSink** | OUI |
| ingestionMappingName | Nom d’un [mappage csv](/azure/kusto/management/mappings#csv-mapping) créé au préalable sur une table Kusto. Pour mapper les colonnes de la source vers Azure Data Explorer, vous pouvez également utiliser l’activité de copie [Mappage de colonnes](copy-activity-schema-and-type-mapping.md). | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional csv mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).