---
title: Copier des données depuis Sybase
description: Découvrez comment utiliser l'activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données de Sybase vers des banques de données réceptrices prises en charge.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: d487a862c6079272c400db964d2a8e898d3121a6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820109"
---
# <a name="copy-data-from-sybase-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de Sybase à l'aide d'Azure Data Factory ou Synapse Analytics
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-onprem-sybase-connector.md)
> * [Version actuelle](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l'activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données à partir d'une base de données Sybase. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Sybase est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données d’une base de données Sybase vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Sybase prend en charge :

- SAP Sybase SQL Anywhere (ASA) **version 16 et ultérieure**.
- Copie des données avec l’authentification **De base** ou **Windows**.

Sybase IQ et ASE ne sont pas pris en charge. Vous pouvez utiliser le connecteur ODBC générique avec le pilote Sybase à la place.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur Sybase, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer le [fournisseur de données pour Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 ou version ultérieure sur l’ordinateur Runtime d’intégration.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sybase-using-ui"></a>Créer un service lié à Sybase à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à Sybase dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez Sybase et sélectionnez le connecteur Sybase.

   :::image type="content" source="media/connector-sybase/sybase-connector.png" alt-text="Sélectionnez le connecteur Sybase.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-sybase/configure-sybase-linked-service.png" alt-text="Configurez un service lié à Sybase.":::

## <a name="connector-configuration-details"></a>Informations de configuration des connecteurs

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Sybase.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Sybase :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Sybase** | Oui |
| server | Nom du serveur Sybase. |Oui |
| database | Nom de la base de données Sybase. |Oui |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données Sybase.<br/>Les valeurs autorisées sont les suivantes : **Basic** et **Windows**. |Oui |
| username | Spécifiez le nom d’utilisateur associé à la connexion à la base de données Sybase. |Oui |
| mot de passe | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |Oui |

**Exemple :**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
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

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit une liste des propriétés prises en charge par le jeu de données Sybase.

Pour copier des données à partir de Sybase, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **SybaseTable** | Oui |
| tableName | Nom de la table dans la base de données Sybase. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Si vous utilisiez un dataset typé `RelationalTable`, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser désormais le nouveau dataset.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source Sybase.

### <a name="sybase-as-source"></a>Sybase en tant que source

Pour copier des données à partir de Sybase, les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **SybaseSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Si vous utilisiez une source de données typée `RelationalSource`, elle reste prise en charge telle quelle, mais nous vous suggérons d’utiliser désormais la nouvelle source.

## <a name="data-type-mapping-for-sybase"></a>Mappage de type de données pour Sybase

Lors de la copie de données à partir de Sybase, les mappages suivants sont utilisés entre les types de données Sybase et les types de données intermédiaires utilisés en interne dans le service. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

Sybase prend en charge les types T-SQL. Pour obtenir une table de mappage entre les types SQL et les types de données intermédiaires, consultez la section [Connecteur Azure SQL Database – Mappage de type de données](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database).

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
