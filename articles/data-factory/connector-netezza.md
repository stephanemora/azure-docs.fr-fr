---
title: Copier des données de Netezza avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de Netezza vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: c3c179cfbf86c2dddfb34b46540aba8898038751
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966486"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copier des données de Netezza avec Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Netezza. Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données depuis Netezza vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré pour permettre la connectivité. Vous n’avez pas besoin d’installer un pilote manuellement pour utiliser ce connecteur.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Prise en main

Vous pouvez créer un pipeline qui utilise une activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Pour obtenir des instructions détaillées sur la création d’un pipeline ayant une activité de copie, consultez le [Tutoriel sur l’activité de copie](quickstart-create-data-factory-dot-net.md).

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres au connecteur Netezza.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Netezza sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **Netezza**. | OUI |
| connectionString | Chaîne de connexion ODBC permettant de se connecter à Netezza. <br/>Marquez ce champ comme SecureString pour le stocker de façon sécurisée dans Data Factory. Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `pwd` de la chaîne de connexion. Pour plus d’informations, reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Pour plus d’informations, consultez la section [Prérequis](#prerequisites). À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

Voici un exemple de chaîne de connexion typique : `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Le tableau suivant décrit les propriétés que vous pouvez définir :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| SecurityLevel | Le niveau de sécurité (SSL/TLS) que le pilote utilise pour la connexion au magasin de données. Exemple : `SecurityLevel=preferredSecured`. Les valeurs prises en charge sont les suivantes :<br/>- **Non sécurisé uniquement** (**onlyUnSecured**) : le pilote n’utilise pas SSL.<br/>- **Non sécurisé préféré (preferredUnSecured) (valeur par défaut)**  : si le serveur offre un choix, le pilote n’utilise pas SSL. <br/>- **Sécurisé préféré (preferredSecured)**  : si le serveur offre un choix, le pilote utilise le protocole SSL. <br/>- **Sécurisé uniquement (onlySecured)**  : le pilote ne se connecte pas si aucune connexion SSL n’est disponible. | Non |
| CaCertFile | Chemin complet du certificat SSL utilisé par le serveur. Exemple : `CaCertFile=<cert path>;`| Oui, si SSL est activé |

**Exemple**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage du mot de passe dans Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
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

## <a name="dataset-properties"></a>Propriétés du jeu de données

Cette section contient la liste des propriétés prises en charge par le jeu de données Netezza.

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données](concepts-datasets-linked-services.md).

Pour copier des données à partir de Netezza, définissez la propriété **type** du jeu de données sur **NetezzaTable**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **NetezzaTable** | OUI |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section contient la liste des propriétés prises en charge par la source Netezza.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza en tant que source

Pour copier des données à partir de Netezza, définissez le type **source** de l’activité de copie sur **NetezzaSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source d’activité de copie doit être définie sur **NetezzaSource**. | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Exemple : `"SELECT * FROM MyTable"` | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
