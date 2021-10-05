---
title: Copier les données dans l’index de recherche
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour envoyer ou copier des données vers un index de recherche Azure.
titleSuffix: Azure Data Factory & Azure Synapse
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 289a347e0007547b1fdba2ffd2b3a4674efb524d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744102"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory-or-synapse-analytics"></a>Copier des données vers un index de recherche cognitive Azure à l’aide d’Azure Data Factory ou de Synapse Analytics

> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-azure-search-connector.md)
> * [Version actuelle](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données vers un index de recherche cognitive Azure. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de toute banque de données source prise en charge dans l’index de recherche. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-azure-search-using-ui"></a>Créer un service lié à Azure Search à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à Azure Search dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Créez un nouveau service lié avec l’interface utilisateur d’Azure Synapse.":::

2. Recherchez Search et sélectionnez le connecteur recherche Azure Search.

   :::image type="content" source="media/connector-azure-search/azure-search-connector.png" alt-text="Sélectionnez le connecteur Azure Search.":::    


1. Configurez les détails du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-azure-search/configure-azure-search-linked-service.png" alt-text="Configurez un service lié à Azure Search.":::

## <a name="connector-configuration-details"></a>Détails de configuration des connecteurs

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Recherche cognitive Azure.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Recherche cognitive Azure sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **AzureSearch** | Oui |
| url | URL du service de recherche. | Oui |
| key | Clé d’administration du service de recherche. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

> [!IMPORTANT]
> Lors de la copie de données d’une banque de données cloud vers l’index de recherche, dans le service lié Recherche cognitive Azure, vous devez référencer un runtime d’intégration Azure avec une région explicite dans connactVia. Définissez la région comme celle où réside votre service de recherche. En savoir plus dans [Integration Runtime Azure](concepts-integration-runtime.md#azure-integration-runtime).

**Exemple :**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données de Recherche cognitive Azure.

Pour copier des données dans Recherche cognitive Azure, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur **AzureSearchIndex** | Oui |
| indexName | Nom de l’index de recherche. Le service ne crée pas l’index. L’index doit exister dans Recherche cognitive Azure. | Oui |

**Exemple :**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Recherche cognitive Azure.

### <a name="azure-cognitive-search-as-sink"></a>Recherche cognitive Azure en tant que récepteur

Pour copier des données vers Recherche cognitive Azure, définissez **AzureSearchIndexSink** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur **AzureSearchIndexSink** | Oui |
| writeBehavior | Indique s’il convient de procéder à une fusion ou à un remplacement lorsqu’un document existe déjà dans l’index. Voir la [propriété WriteBehavior](#writebehavior-property).<br/><br/>Valeurs autorisées : **Merge** (par défaut) et **Upload**. | Non |
| writeBatchSize | Charge des données dans l’index de recherche lorsque la taille de la mémoire tampon atteint writeBatchSize. Pour plus d’informations, voir la [propriété WriteBatchSize](#writebatchsize-property).<br/><br/>Valeurs autorisées : entier de 1 à 1000 ; la valeur par défaut est 1000. | Non |
| maxConcurrentConnections |La limite supérieure de connexions simultanées établies au magasin de données pendant l’exécution de l’activité. Spécifiez une valeur uniquement lorsque vous souhaitez limiter les connexions simultanées.| Non |

### <a name="writebehavior-property"></a>Propriété WriteBehavior

AzureSearchSink effectue une opération d’upsert lors de l’écriture des données. En d’autres termes, lorsque vous écrivez un document, si la clé du document existe déjà dans l’index de recherche, Recherche cognitive Azure met à jour le document existant au lieu de lever une exception de conflit.

AzureSearchSink fournit les deux comportements upsert suivants (en utilisant le Kit de développement logiciel (SDK) AzureSearch) :

- **Fusion** : combine toutes les colonnes du nouveau document avec celles du document existant. Pour les colonnes ayant une valeur Null dans le nouveau document, la valeur de celles du document existant est conservée.
- **Chargement**  : Le nouveau document remplace le document existant. Pour les colonnes qui ne sont pas spécifiées dans le nouveau document, la valeur est définie sur Null qu’il existe ou non une valeur autre que Null dans le document existant.

**Fusion** est le comportement par défaut.

### <a name="writebatchsize-property"></a>Propriété WriteBatchSize

Le service Recherche cognitive Azure prend en charge l’écriture de documents sous forme de lot. Un lot peut contenir 1 à 1 000 actions. Une action gère un document pour effectuer l’opération de chargement/fusion.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Prise en charge des types de données

Le tableau suivant indique si un type de données Recherche cognitive Azure est pris en charge ou non.

| Type de données Recherche cognitive Azure | Pris en charge dans le récepteur de Recherche cognitive Azure |
| ---------------------- | ------------------------------ |
| String | O |
| Int32 | O |
| Int64 | O |
| Double | O |
| Boolean | O |
| DataTimeOffset | O |
| Tableau de chaînes | N |
| GeographyPoint | N |

Les autres types de données, par exemple ComplexType, ne sont pas pris en charge pour le moment. Pour obtenir la liste complète des types de données pris en charge par Recherche cognitive Azure, consultez [Types de données pris en charge (Recherche cognitive Azure)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).