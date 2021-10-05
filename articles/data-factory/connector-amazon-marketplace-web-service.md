---
title: Copier des données à partir de la Place de marché AWS
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données du service web Amazon Marketplace vers des banques de données réceptrices prises en charge.
titleSuffix: Azure Data Factory & Azure Synapse
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.author: jianleishen
author: jianleishen
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8529419520deab9f00073e2ad16e2022ae5efa74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828304"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-or-synapse-analytics"></a>Copie de données du service web Amazon Marketplace à l’aide d’Azure Data Factory ou de Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données d’Amazon Marketplace Web Service. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Amazon Marketplace Web Service est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier les données du service web Amazon Marketplace vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

La service fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-marketplace-web-service-using-ui"></a>Créer un service lié au service web Amazon Marketplace à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié au service web Amazon Marketplace dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez Amazon et sélectionnez le connecteur de service web Amazon Marketplace.

   :::image type="content" source="media/connector-amazon-marketplace-web-service/amazon-marketplace-web-service-connector.png" alt-text="Capture d’écran du connecteur de service web Amazon Marketplace.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-amazon-marketplace-web-service/configure-amazon-marketplace-web-service-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour le service web Amazon Marketplace.":::

## <a name="connector-configuration-details"></a>Informations de configuration du connecteur

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques au connecteur du service web Amazon Marketplace.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié au service web Amazon Marketplace sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **AmazonMWS** | Oui |
| endpoint | Le point de terminaison du serveur Amazon MWS, (autrement dit, mws.amazonservices.com)  | Oui |
| marketplaceID | L’ID Amazon Marketplace à partir duquel vous souhaitez récupérer des données. Pour récupérer des données à partir de plusieurs ID Marketplace, séparez-les par une virgule (`,`). (autrement dit, A2EUQ1WTGCTBG2)  | Oui |
| sellerID | L’ID de vendeur Amazon.  | Oui |
| mwsAuthToken | Le jeton d’authentification Amazon MWS. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| accessKeyId | L’ID de la clé d’accès utilisée pour accéder aux données.  | Oui |
| secretKey | La clé secrète utilisée pour accéder aux données. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non |
| useHostVerification | Indique si le nom d’hôte du certificat du serveur doit correspondre à celui du serveur en cas de connexion TLS. La valeur par défaut est true.  | Non |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion TLS. La valeur par défaut est true.  | Non |

**Exemple :**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données du service web Amazon Marketplace.

Pour copier des données du service web Amazon Marketplace, affectez la valeur **AmazonMWSObject** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **AmazonMWSObject** | Oui |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source du service web Amazon Marketplace.

### <a name="amazon-mws-as-source"></a>Amazon MWS en tant que source

Pour copier des données du service web Amazon Marketplace, définissez **AmazonMWSSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **AmazonMWSSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez la section sur les [magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
