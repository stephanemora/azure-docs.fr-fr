---
title: Copie de données du service web Amazon Marketplace à l’aide d’Azure Data Factory (préversion) | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données du service web Amazon Marketplace vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 45208b5c6538ea523a7b87d6dbdeb99e792783ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262046"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-preview"></a>Copie de données du service web Amazon Marketplace à l’aide d’Azure Data Factory (préversion)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données du service web Amazon Marketplace. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en préversion. Essayez-le et envoyez-nous vos commentaires. Si vous souhaitez établir une dépendance sur les connecteurs en préversion dans votre solution, veuillez contacter le [support Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données du service web Amazon Marketplace vers tout magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques au connecteur du service web Amazon Marketplace.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié au service web Amazon Marketplace sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **AmazonMWS** | Oui |
| endpoint | Le point de terminaison du serveur Amazon MWS, (autrement dit, mws.amazonservices.com)  | Oui |
| marketplaceID | L’ID Amazon Marketplace à partir duquel vous souhaitez récupérer des données. Pour récupérer des données à partir de plusieurs ID Marketplace, séparez-les par une virgule (`,`). (autrement dit, A2EUQ1WTGCTBG2)  | Oui |
| sellerID | L’ID de vendeur Amazon.  | Oui |
| mwsAuthToken | Le jeton d’authentification Amazon MWS. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| accessKeyId | L’ID de la clé d’accès utilisée pour accéder aux données.  | Oui |
| secretKey | La clé secrète utilisée pour accéder aux données. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non  |
| useHostVerification | Indique si le nom d’hôte du certificat du serveur doit correspondre à celui du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non  |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non  |

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
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
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

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
