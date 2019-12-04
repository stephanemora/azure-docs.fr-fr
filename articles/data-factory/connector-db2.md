---
title: Copier des données à partir de DB2 avec Azure Data Factory
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de DB2 vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: e72e6c112913d646b6dc1479a9b80acc6d4ec7b1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280750"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copier des données de DB2 à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [Version actuelle](connector-db2.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’une base de données DB2. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur de base de données DB2 est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données d’une base de données DB2 vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

En particulier, ce connecteur DB2 prend en charge les plateformes et versions IBM DB2 suivantes avec les versions 9, 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture) :

* IBM DB2 pour z/OS 12.1
* IBM DB2 pour z/OS 11.1
* IBM DB2 pour z/OS 10.1
* IBM DB2 pour i 7.3
* IBM DB2 pour i 7.2
* IBM DB2 pour i 7.1
* IBM DB2 pour LUW 11
* IBM DB2 pour LUW 10.5
* IBM DB2 pour LUW 10.1

> [!TIP]
> Si vous recevez le message d’erreur « Le package correspondant à une requête d’exécution d’instruction SQL est introuvable. SQLSTATE = 51002 SQLCODE =-805 », la raison en est qu’un package nécessaire n’est pas créé pour un utilisateur normal sur un tel système d’exploitation. Suivez les instructions appropriées pour votre type de serveur DB2 :
> - DB2 pour i (AS400) : demandez à un utilisateur chevronné de créer une collection pour l’utilisateur de connexion avant d’utiliser une activité de copie. Commande : `create collection <username>`
> - DB2 pour z/OS ou LUW : utilisez un compte doté de privilèges élevés (utilisateur chevronné ou administrateur disposant d’autorités de package et d’autorisations BIND, BINDADD, GRANT EXECUTE TO PUBLIC) pour exécuter une fois l’activité de copie. Le package nécessaire est ensuite créé automatiquement au cours de la copie. Ensuite, vous pouvez revenir au mode utilisateur normal pour vos séries de copie suivantes.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Le runtime d’intégration fournit un pilote DB2 intégré. Ainsi, vous n’avez pas besoin d’installer manuellement un pilote lors de la copie des données de DB2.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur DB2.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié DB2 sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Db2** | OUI |
| server |Nom du serveur DB2. Vous pouvez spécifier le numéro de port à la suite du nom du serveur en le séparant par un signe deux-points, par exemple, `server:port`. |OUI |
| database |Nom de la base de données DB2. |OUI |
| authenticationType |Type d'authentification utilisé pour se connecter à la base de données DB2.<br/>Valeur autorisée : **De base**. |OUI |
| username |Spécifiez le nom d’utilisateur pour la connexion à la base de données DB2. |OUI |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| packageCollection | Spécifiez sous quel emplacement les packages nécessaires sont créés automatiquement par ADF lors de l’interrogation de la base de données. | Non |
| certificateCommonName | Lorsque vous utilisez le chiffrement SSL (Secure Sockets Layer) ou TLS (Transport Layer Security), vous devez entrer une valeur pour le nom commun du certificat. | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données DB2.

Pour copier des données à partir de DB2, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type du jeu de données doit être définie sur : **Db2Table** | OUI |
| schema | Nom du schéma. |Non (si « query » dans la source de l’activité est spécifié)  |
| table | Nom de la table. |Non (si « query » dans la source de l’activité est spécifié)  |
| tableName | Nom de la table avec le schéma. Cette propriété est prise en charge pour la compatibilité descendante. Utilisez `schema` et `table` pour une nouvelle charge de travail. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Si vous utilisiez un dataset typé `RelationalTable`, il reste pris en charge tel quel, mais nous vous suggérons d’utiliser désormais le nouveau dataset.

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source DB2.

### <a name="db2-as-source"></a>DB2 en tant que source

Pour copier des données de DB2, les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type de la source d’activité de copie doit être définie sur : **Db2Source** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Si vous utilisiez une source de données typée `RelationalSource`, elle reste prise en charge telle quelle, mais nous vous suggérons d’utiliser désormais la nouvelle source.

## <a name="data-type-mapping-for-db2"></a>Mappage de type de données pour DB2

Lors de la copie de données de DB2, les mappages suivants sont utilisés entre les types de données DB2 et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type de base de données DB2 | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Chaîne |
| Clob |Chaîne |
| Date |Datetime |
| DB2DynArray |Chaîne |
| DbClob |Chaîne |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Chaîne |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Chaîne |
| LongVarGraphic |Chaîne |
| Chiffre |Decimal |
| Real |Unique |
| SmallInt |Int16 |
| Temps |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte[] |
| VarChar |Chaîne |
| VarGraphic |Chaîne |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
