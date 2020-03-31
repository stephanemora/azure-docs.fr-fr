---
title: Copier des données de Hive à l’aide d’Azure Data Factory
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de Hive vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 965864f1d2bc50ba7e5ae42e2b174a4fdc8d5c94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929337"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copier des données de Hive à l’aide d’Azure Data Factory 

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de Hive. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Hive est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de Hive vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Hive.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Hive sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Hive** | Oui |
| host | Adresse IP ou nom d’hôte du serveur Hive, séparé par « ; » pour plusieurs hôtes (uniquement quand serviceDiscoveryMode est activé).  | Oui |
| port | Port TCP utilisé par le serveur Hive pour écouter les connexions clientes. Si vous êtes connecté à Azure HDInsights, spécifiez le port 443. | Oui |
| serverType | Type du serveur Hive. <br/>Les valeurs autorisées sont les suivantes : **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Non |
| thriftTransportProtocol | Protocole de transport à utiliser dans la couche Thrift. <br/>Les valeurs autorisées sont les suivantes : **Binary**, **SASL**, **HTTP** | Non |
| authenticationType | Méthode d’authentification utilisée pour accéder au serveur Hive. <br/>Les valeurs autorisées sont les suivantes : **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Oui |
| serviceDiscoveryMode | Valeur true pour indiquer l’utilisation du service ZooKeeper, valeur false dans le cas contraire.  | Non |
| zooKeeperNameSpace | Espace de noms sur ZooKeeper sous lequel les 2 nœuds du serveur Hive sont ajoutés.  | Non |
| useNativeQuery | Indique si le pilote doit utiliser les requêtes HiveQL natives ou les convertir dans un format équivalent dans HiveQL.  | Non |
| username | Nom d’utilisateur utilisé pour accéder au serveur Hive.  | Non |
| mot de passe | Mot de passe correspondant à l’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non |
| httpPath | URL partielle correspondant au serveur Hive.  | Non |
| enableSsl | Indique si les connexions au serveur sont chiffrées suivant le protocole SSL. La valeur par défaut est false.  | Non |
| trustedCertPath | Chemin d’accès complet du fichier .pem contenant les certificats d’autorité de certification approuvés permettant de vérifier le serveur en cas de connexion via SSL. Cette propriété n’est disponible que si le protocole SSL est utilisé sur un runtime d’intégration auto-hébergé. Valeur par défaut : le fichier cacerts.pem installé avec le runtime d’intégration.  | Non |
| useSystemTrustStore | Indique s’il faut utiliser un certificat d’autorité de certification provenant du magasin de confiance du système ou d’un fichier PEM spécifié. La valeur par défaut est false.  | Non |
| allowHostNameCNMismatch | Indique si le nom du certificat SSL émis par l’autorité de certification doit correspondre au nom d’hôte du serveur en cas de connexion SSL. La valeur par défaut est false.  | Non |
| allowSelfSignedServerCert | Indique si les certificats auto-signés provenant du serveur sont autorisés ou non. La valeur par défaut est false.  | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Hive.

Pour copier des données de Hive, définissez la propriété de type du jeu de données sur **HiveObject**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **HiveObject** | Oui |
| schéma | Nom du schéma. |Non (si « query » dans la source de l’activité est spécifié)  |
| table | Nom de la table. |Non (si « query » dans la source de l’activité est spécifié)  |
| tableName | Nom de la table incluant la partie de schéma. Cette propriété est prise en charge pour la compatibilité descendante. Pour les nouvelles charges de travail, utilisez `schema` et `table`. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Hive.

### <a name="hivesource-as-source"></a>HiveSource en tant que source

Pour copier des données de Hive, définissez le type de source dans l’activité de copie sur **HiveSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **HiveSource** | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
