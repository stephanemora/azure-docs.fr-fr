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
ms.date: 04/16/2019
ms.author: orspod
ms.openlocfilehash: 756ede9cc90655163d6d53aa3ca920d2a15fb43d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682489"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Copier des données depuis/vers Azure Data Explorer à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données vers ou depuis [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de tout magasin de données source pris en charge vers Azure Data Explorer. Vous pouvez également copier des données à partir d’Azure Data Explorer vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md).

>[!NOTE]
>Copie des données vers/depuis Azure Explorateur de données depuis/vers sur le magasin de données de site à l’aide du Runtime d’intégration auto-hébergé est pris en charge depuis la version 3,14.

Le connecteur Azure Data Explorer vous permet d’effectuer les opérations suivantes :

* Copier des données en utilisant une authentification de jeton d’application Azure Active Directory (Azure AD) avec un **principal du service**.
* En tant que source, récupérer des données à l’aide d’une requête KQL (Kusto).
* En tant que récepteur, ajouter des données à une table de destination.

## <a name="getting-started"></a>Prise en main

>[!TIP]
>Pour obtenir une description de l’utilisation du connecteur de l’Explorateur de données Azure, consultez [copier des données vers/à partir de l’Explorateur de données Azure à l’aide d’Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir des entités Data Factory spécifiques du connecteur Azure Data Explorer.

## <a name="linked-service-properties"></a>Propriétés du service lié

Le connecteur de l’Explorateur de données Azure utilise l’authentification de principal du service. Suivez ces étapes pour obtenir un principal de service et accorder des autorisations :

1. Inscrivez une entité d’application dans Azure Active Directory (Azure AD) en suivant les instructions de la section [Inscrire votre application à un locataire Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

    - ID de l'application
    - Clé de l'application
    - ID client

2. Accorder l’autorisation appropriée au service principal dans l’Explorateur de données Azure. Reportez-vous à [autorisations de base de données de gérer Azure Data Explorer](../data-explorer/manage-database-permissions.md) avec des informations détaillées sur les rôles et autorisations mais aussi procédure pas à pas sur la gestion des autorisations. En règle générale, vous devez

    - **En tant que source**, accordez au moins **Observateur de base de données** rôle à votre base de données.
    - **En tant que récepteur**, accordez au moins **évolutif de base de données** rôle à votre base de données.

>[!NOTE]
>Lorsque vous utilisez ADF UI pour créer, les opérations d’énumération des bases de données sur le service lié ou répertoriant les tables sur le jeu de données peuvent nécessiter plus élevée privilégié d’une autorisation pour le principal du service. Vous pouvez également entrer manuellement le nom de la base de données et le nom de la table. Copier l’activité s’exécute tant que le principal du service est accordé avec l’autorisation appropriée pour lire/écrire des données.

Les propriétés suivantes sont prises en charge pour le service lié Azure Data Explorer :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **AzureDataExplorer**. | Oui |
| endpoint | URL de point de terminaison du cluster Azure Data Explorer, avec le format `https://<clusterName>.<regionName>.kusto.windows.net`. | Oui |
| database | Nom de base de données. | Oui |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. C’est ce que vous savez normalement en tant que «**ID d’autorité**» dans [chaîne de connexion Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Récupérez-les en pointant la souris dans l’angle supérieur droit du portail Azure. | Oui |
| servicePrincipalId | Spécifiez l’ID client de l’application. C’est ce que vous savez normalement en tant que «**ID de client d’application AAD**» dans [chaîne de connexion Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Oui |
| servicePrincipalKey | Spécifiez la clé de l’application. C’est ce que vous savez normalement en tant que «**clé d’application AAD**» dans [chaîne de connexion Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marquez ce champ en tant que **SecureString** afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |

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
| Type | La propriété **type** doit être définie sur **AzureDataExplorerTable**. | Oui |
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
| Type | La propriété **type** de la source d’activité de copie doit être définie sur : **AzureDataExplorerSource** | Oui |
| query | Requête en lecture seule au [format KQL](/azure/kusto/query/). Utilisez la requête KQL personnalisée en tant que référence. | Oui |
| queryTimeout | Temps d’attente avant l’expiration de la demande de requête. La valeur par défaut est 10 minutes (00:10:00) et la valeur maximale autorisée 1 heure (01:00:00). | Non  |

>[!NOTE]
>Source de l’Explorateur de données Azure par défaut a une taille limite de 500 000 enregistrements ou 64 Mo. Pour récupérer tous les enregistrements sans troncation, vous pouvez spécifier `set notruncation;` au début de votre requête. Reportez-vous à [limites de requête](https://docs.microsoft.com/azure/kusto/concepts/querylimits) sur plus de détails.

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
| Type | La propriété **type** du récepteur d’activité de copie doit être définie sur : **AzureDataExplorerSink** | Oui |
| ingestionMappingName | Nom d’un créé au préalable **[mappage](/azure/kusto/management/mappings#csv-mapping)** sur une table Kusto. Pour mapper les colonnes de la source à l’Explorateur de données Azure - qui s’applique aux **[tous pris en charge magasins/formats de source de](copy-activity-overview.md#supported-data-stores-and-formats)** notamment CSV/JSON/Avro met en forme etc., vous pouvez utiliser l’activité de copie [colonne mappage](copy-activity-schema-and-type-mapping.md) (implicitement par nom ou explicitement configurés) et/ou les mappages de l’Explorateur de données Azure. | Non  |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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