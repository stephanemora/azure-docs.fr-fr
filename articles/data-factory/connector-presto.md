---
title: Copier des données à partir de Presto
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données de Presto vers des banques de données réceptrices prises en charge.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 92c4d895d59dbbc145f4f3836cbb81608ea2ecfc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836224"
---
# <a name="copy-data-from-presto-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de Presto à l’aide d’Azure Data Factory ou Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l’activité de copie dans le pipeline Azure Data Factory ou Synapse Analytics pour copier des données de Presto. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Presto est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de Presto vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Le service fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-presto-using-ui"></a>Créer un service lié à Presto à l’aide de l’interface utilisateur

Suivez les étapes suivantes pour créer un service lié à Presto dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse, sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez Presto et sélectionnez le connecteur Presto.

   :::image type="content" source="media/connector-presto/presto-connector.png" alt-text="Capture d’écran du connecteur Presto.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-presto/configure-presto-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour Presto.":::

## <a name="connector-configuration-details"></a>Détails de configuration du connecteur

Les sections suivantes donnent des précisions sur les propriétés utilisées pour définir des entités Data Factory propres au connecteur Presto.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Presto :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Presto** | Oui |
| host | Adresse IP ou nom d’hôte du serveur Presto (par exemple, 192.168.222.160)  | Oui |
| serverVersion | Version du serveur Presto (par exemple, 0.148-t)  | Oui |
| catalogue | Contexte du catalogue pour toutes les requêtes effectuées sur le serveur.  | Oui |
| port | Port TCP utilisé par le serveur Presto pour écouter les connexions clientes. La valeur par défaut est 8080.  | Non |
| authenticationType | Mécanisme d’authentification utilisé pour se connecter au serveur Presto. <br/>Les valeurs autorisées sont les suivantes : **Anonymous**, **LDAP** | Oui |
| username | Nom d’utilisateur utilisé pour se connecter au serveur Presto.  | Non |
| mot de passe | Mot de passe correspondant au nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non |
| enableSsl | Indique si les connexions au serveur sont chiffrées à l’aide du protocole TLS. La valeur par défaut est false.  | Non |
| trustedCertPath | Chemin complet du fichier .pem contenant les certificats d’autorité de certification approuvés permettant de vérifier le serveur en cas de connexion TLS. Cette propriété n’est disponible que si le protocole TLS est utilisé sur un runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier PEM spécifié. La valeur par défaut est false.  | Non |
| allowHostNameCNMismatch | Indique si le nom du certificat TLS/SSL émis par l’autorité de certification doit correspondre au nom d’hôte du serveur en cas de connexion TLS. La valeur par défaut est false.  | Non |
| allowSelfSignedServerCert | Indique si les certificats auto-signés provenant du serveur sont autorisés ou non. La valeur par défaut est false.  | Non |
| timeZoneID | Fuseau horaire local utilisé par la connexion. Les valeurs valides de cette option sont spécifiées dans la base de données IANA Time Zone Database. Valeur par défaut : le fuseau horaire système.  | Non |

**Exemple :**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données Presto.

Pour copier des données de Presto, affectez la valeur **PrestoObject** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **PrestoObject** | Oui |
| schéma | Nom du schéma. |Non (si « query » dans la source de l’activité est spécifié)  |
| table | Nom de la table. |Non (si « query » dans la source de l’activité est spécifié)  |
| tableName | Nom de la table avec le schéma. Cette propriété est prise en charge pour la compatibilité descendante. Utilisez `schema` et `table` pour une nouvelle charge de travail. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par la source Presto.

### <a name="presto-as-source"></a>Presto en tant que source

Pour copier des données de Presto, affectez la valeur **PrestoSource** au type source de l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **PrestoSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
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
