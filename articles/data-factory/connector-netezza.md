---
title: Copier des données depuis Netezza
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données de Netezza vers des banques de données réceptrices prises en charge.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 588700126d87361e4530c073cdcaf1a5a9339511
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828174"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de Netezza à l’aide d’Azure Data Factory ou Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment utiliser l’activité de copie dans des pipelines Azure Data Factory ou Azure Synapse Analytics pour copier des données à partir de Netezza. Il s’appuie sur l’article [Activité de copie](copy-activity-overview.md), qui présente une vue d’ensemble de cette activité.

>[!TIP]
>Pour le scénario de migration de données de Netezza vers Azure, consultez [Migrer des données depuis le serveur Netezza local vers Azure](data-migration-guidance-netezza-azure-sqldw.md) pour en savoir plus.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Netezza est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)


Vous pouvez copier les données depuis Netezza vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Le connecteur Netezza prend en charge la copie en parallèle à partir de la source. Pour en savoir plus, consultez [Copie en parallèle à partir de Netezza](#parallel-copy-from-netezza).

Le service fournit un pilote intégré pour permettre la connectivité. Vous n’avez pas besoin d’installer un pilote manuellement pour utiliser ce connecteur.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bien démarrer

Vous pouvez créer un pipeline qui utilise une activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, consultez le [Didacticiel de l’activité de copie](quickstart-create-data-factory-dot-net.md).

## <a name="create-a-linked-service-to-netezza-using-ui"></a>Créer un service lié à Netezza à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à Netezza dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse, sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez Netezza, puis sélectionnez le connecteur Netezza.

   :::image type="content" source="media/connector-netezza/netezza-connector.png" alt-text="Capture d’écran du connecteur Netezza.":::    


1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

   :::image type="content" source="media/connector-netezza/configure-netezza-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour Netezza.":::

## <a name="connector-configuration-details"></a>Informations de configuration du connecteur

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisables pour définir les entités propres au connecteur Netezza.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Netezza sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** doit être définie sur **Netezza**. | Oui |
| connectionString | Chaîne de connexion ODBC permettant de se connecter à Netezza. <br/>Vous pouvez également définir un mot de passe dans Azure Key Vault et extraire la configuration `pwd` de la chaîne de connexion. Pour plus d’informations, reportez-vous aux exemples suivants et à l’article [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). À défaut de spécification, l’Azure Integration Runtime par défaut est utilisé. |Non |

Voici un exemple de chaîne de connexion typique : `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Le tableau suivant décrit les propriétés que vous pouvez définir :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| SecurityLevel | Niveau de sécurité que le pilote utilise pour la connexion au magasin de données. Le pilote prend en charge les connexions SSL avec authentification unidirectionnelle à l’aide de SSL version 3. <br>Exemple : `SecurityLevel=preferredSecured`. Les valeurs prises en charge sont les suivantes :<br/>- **Non sécurisé uniquement** (**onlyUnSecured**) : le pilote n’utilise pas SSL.<br/>- **Non sécurisé préféré (preferredUnSecured) (valeur par défaut)**  : si le serveur offre un choix, le pilote n’utilise pas SSL. <br/>- **Sécurisé préféré (preferredSecured)**  : si le serveur offre un choix, le pilote utilise le protocole SSL. <br/>- **Sécurisé uniquement (onlySecured)**  : le pilote ne se connecte pas si aucune connexion SSL n’est disponible. | Non |
| CaCertFile | Chemin complet du certificat SSL utilisé par le serveur. Exemple : `CaCertFile=<cert path>;`| Oui, si SSL est activé |

**Exemple**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : stockage du mot de passe dans Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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
| type | La propriété type du jeu de données doit être définie sur : **NetezzaTable** | Oui |
| schéma | Nom du schéma. |Non (si « query » dans la source de l’activité est spécifié)  |
| table | Nom de la table. |Non (si « query » dans la source de l’activité est spécifié)  |
| tableName | Nom de la table avec le schéma. Cette propriété est prise en charge pour la compatibilité descendante. Utilisez `schema` et `table` pour une nouvelle charge de travail. | Non (si « query » dans la source de l’activité est spécifié) |

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

>[!TIP]
>Pour charger efficacement des données à partir de Netezza à l'aide du partitionnement des données, consultez la section [Copie en parallèle à partir de Netezza](#parallel-copy-from-netezza).

Pour copier des données à partir de Netezza, définissez le type **source** de l’activité de copie sur **NetezzaSource**. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété **type** de la source d’activité de copie doit être définie sur **NetezzaSource**. | Oui |
| query | Utiliser la requête SQL personnalisée pour lire les données. Exemple : `"SELECT * FROM MyTable"` | Non (si « tableName » est spécifié dans dataset) |
| partitionOptions | Spécifie les options de partitionnement des données utilisées pour charger des données à partir de Netezza. <br>Les valeurs autorisées sont : **Aucun** (par défaut), **DataSlice** et **DynamicRange**.<br>Lorsqu’une option de partition est activée (autrement dit, pas `None`), le degré de parallélisme pour charger simultanément des données à partir d’une base de données Netezza est contrôlé par le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de l’activité de copie. | Non |
| partitionSettings | Spécifiez le groupe de paramètres pour le partitionnement des données. <br>S’applique lorsque l’option de partitionnement n’est pas `None`. | Non |
| partitionColumnName | Spécifiez le nom de la colonne source **dans type entier** qu’utilisera le partitionnement par plages de valeurs pour la copie en parallèle. S’il n’est pas spécifié, la clé primaire de la table est détectée automatiquement et utilisée en tant que colonne de partition. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionColumnName` dans la clause WHERE. Consultez l’exemple de la section [Copie en parallèle à partir de Netezza](#parallel-copy-from-netezza). | Non |
| partitionUpperBound | Valeur maximale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque de l’option de partition est `DynamicRange`. Si vous avez recours à une requête pour récupérer des données sources, utilisez `?AdfRangePartitionUpbound` dans la clause WHERE. Pour consulter un exemple, voir [Copie en parallèle à partir de Netezza](#parallel-copy-from-netezza). | Non |
| partitionLowerBound | Valeur minimale de la colonne de partition à partir de laquelle copier des données. <br>S’applique lorsque l’option de partitionnement est `DynamicRange`. Si vous utilisez une requête pour récupérer des données sources, utilisez `?AdfRangePartitionLowbound` dans la clause WHERE. Pour consulter un exemple, voir [Copie en parallèle à partir de Netezza](#parallel-copy-from-netezza). | Non |

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

## <a name="parallel-copy-from-netezza"></a>Copie en parallèle à partir de Netezza

Le connecteur Netezza de Data Factory propose un partitionnement de données intégré pour copier des données à partir de Netezza, en parallèle. Vous trouverez des options de partitionnement de données dans la table **Source** de l’activité de copie.

:::image type="content" source="./media/connector-netezza/connector-netezza-partition-options.png" alt-text="Capture d’écran représentant les options de partition":::

Lorsque vous activez la copie partitionnée, le service exécute des requêtes en parallèle sur votre source Netezza pour charger des données par partitions. Le degré de parallélisme est contrôlé via le paramètre [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sur l’activité de copie. Par exemple, si vous définissez `parallelCopies` sur quatre, le service génère et exécute simultanément quatre requêtes basées sur l’option de partition et les paramètres que vous avez spécifiés, chacune récupérant des données à partir de votre base de données Netezza.

Il vous est recommandé d’activer la copie en parallèle avec partitionnement des données notamment lorsque vous chargez une grande quantité de données à partir de votre base de données Netezza. Voici quelques suggestions de configurations pour différents scénarios. Lors de la copie de données dans un magasin de données basé sur des fichiers, il est recommandé de les écrire dans un dossier sous la forme de plusieurs fichiers (spécifiez uniquement le nom du dossier). Les performances seront meilleures qu’avec l’écriture dans un seul fichier.

| Scénario                                                     | Paramètres suggérés                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Chargement complet à partir d’une table volumineuse.                                   | **Option de partition** : Tranche de données. <br><br/>Lors de l’exécution, le service partitionne automatiquement les données en fonction des [tranches de données intégrées de Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html) et copie les données par partitions. |
| Chargez une grande quantité de données à l’aide d’une requête personnalisée.                 | **Option de partition** : Tranche de données.<br>**Requête**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Lors de l’exécution, le service remplace `?AdfPartitionCount` (par le numéro de copie parallèle défini sur l'activité de copie) et `?AdfDataSliceCondition` par la logique de partition de tranche de données, puis l'envoie à Netezza. |
| Chargez une grande quantité de données à l’aide d’une requête personnalisée, qui dispose d’une colonne d’entiers avec valeur uniformément distribuée pour le partitionnement par plages de valeurs. | **Options de partition** : Partition dynamique par spécification de plages de valeurs.<br>**Requête**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonne de partition** : Spécifiez la colonne utilisée pour partitionner les données. Vous pouvez procéder au partitionnement par rapport à la colonne avec le type de données entier.<br>**Limite supérieure de partition** et **limite inférieure de partition** : Indiquez si vous souhaitez filtrer le contenu par rapport à la colonne de partition pour récupérer uniquement les données entre les plages inférieure et supérieure.<br><br>Lors de l’exécution, le service remplace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` et `?AdfRangePartitionLowbound` par le nom réel de la colonne et les plages de valeurs de chaque partition et les envoie à Netezza. <br>Par exemple, si votre colonne de partition « ID » est définie sur une limite inférieure de 1 et une limite supérieure de 80, avec une copie en parallèle définie sur 4, le service récupère les données via 4 partitions. Les ID sont inclus entre [1,20], [21, 40], [41, 60] et [61, 80], respectivement. |

**Exemple : requête avec partition tranche de données**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exemple : requête avec partition dynamique par spécification de plages de valeurs**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
