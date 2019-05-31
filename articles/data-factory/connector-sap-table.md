---
title: Copier des données à partir de la Table de SAP à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment copier des données à partir de la Table SAP vers des banques de données récepteur pris en charge à l’aide d’une activité de copie dans un pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: 4dee0e994c9e7be9677a8f1051481850990998e9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247168"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Copier des données à partir de la Table de SAP à l’aide d’Azure Data Factory

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir d’une Table de SAP. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données à partir de la Table SAP vers toute banque de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Table SAP prend en charge :

- Copie de données à partir de la Table SAP dans **SAP Business Suite avec version 7.01 ou une version ultérieure** (dans une récente SAP prise en charge de Package pile publiée après l’année 2015) ou **S/4HANA**.
- Copie de données à partir des deux **Table Transparent SAP** et **vue**.
- Copie des données à l’aide **l’authentification de base** ou **SNC** (sécuriser les Communications réseau) si SNC est configuré.
- Connexion à **serveur d’applications** ou **serveur Message**.

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser ce connecteur Table SAP, vous devez :

- Configurer un Runtime d’intégration auto-hébergé avec version 3.17 ou version ultérieure. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).

- Téléchargez la version **64 bits de [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** à partir du site web de SAP et installez-la sur la machine dotée du runtime d'intégration auto-hébergé. Lorsque l’installation, dans la fenêtre d’étapes d’installation facultatif, veillez à sélectionner le **installer des assemblys GAC** option. 

    ![Installer SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Utilisateur SAP utilisé dans le connecteur de Table de SAP de fabrique de données doit comporter les autorisations suivantes : 

    - Autorisation pour RFC. 
    - Autorisations pour l’activité « Exécuter » de l’objet d’autorisation « S_SDSAUTH ».

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur de la Table SAP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP Business Warehouse Open Hub sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **SapTable** | Oui |
| serveur | Nom du serveur sur lequel réside l’instance SAP.<br/>Applicable si vous souhaitez vous connecter à **serveur d’applications SAP**. | Non  |
| systemNumber | Numéro de système du système SAP.<br/>Applicable si vous souhaitez vous connecter à **serveur d’applications SAP**.<br/>Valeur autorisée : nombre décimal à deux chiffres représenté sous forme de chaîne. | Non |
| messageServer | Le nom d’hôte du serveur de messages SAP.<br/>Applicable si vous souhaitez vous connecter à **SAP Message Server**. | Non  |
| messageServerService | Le nom de service ou le port numéro du serveur de Message.<br/>Applicable si vous souhaitez vous connecter à **SAP Message Server**. | Non  |
| systemId | SystemID du système SAP où se trouve la table.<br/>Applicable si vous souhaitez vous connecter à **SAP Message Server**. | Non |
| logonGroup | Le groupe ouverture de session pour le système SAP.<br/>Applicable si vous souhaitez vous connecter à **SAP Message Server**. | Non |
| clientId | ID de client du client dans le système SAP.<br/>Valeur autorisée : nombre décimal à trois chiffres représenté sous forme de chaîne. | Oui |
| langage | Langue utilisée par le système SAP. | Non (la valeur par défaut est **EN**)|
| userName | Nom de l’utilisateur ayant accès au serveur SAP. | Oui |
| password | Mot de passe pour l’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui |
| sncMode | Indicateur d’activation de SNC pour accéder au serveur SAP où se trouve la table.<br/>Applicable si vous souhaitez utiliser SNC pour se connecter au serveur SAP.<br/>Les valeurs autorisées sont les suivantes : **0** (désactivé, valeur par défaut) ou **1** (activé). | Non  |
| sncMyName | Nom CRS de l’initiateur pour accéder au serveur SAP où se trouve la table.<br/>S’applique lorsque `sncMode` est sur. | Non |
| sncPartnerName | Nom SNC du partenaire de communication pour accéder au serveur SAP où se trouve la table.<br/>S’applique lorsque `sncMode` est sur. | Non  |
| sncLibraryPath | Bibliothèque du produit de sécurité externes pour accéder au serveur SAP où se trouve la table.<br/>S’applique lorsque `sncMode` est sur. | Non |
| sncQop | Qualité SNC de Protection.<br/>S’applique lorsque `sncMode` est sur. <br/>Les valeurs autorisées sont les suivantes : **1** (authentification), **2** (intégrité), **3** (confidentialité), **8** (par défaut), **9** (Maximum). | Non |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |Oui |

**Exemple 1 : connexion au serveur d’applications SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : connexion au serveur de messages SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 3 : connexion à l’aide de SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit une liste des propriétés prises en charge par le jeu de données de Table SAP.

Pour copier des données depuis / vers SAP BW Open Hub, les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie **SapTableResource**. | Oui |
| tableName | Le nom de la Table pour copier des données à partir de SAP. | Oui |

**Exemple :**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit une liste des propriétés prises en charge par la source de la Table SAP.

### <a name="sap-table-as-source"></a>Table SAP en tant que source

Pour copier des données à partir de la Table SAP, les propriétés suivantes sont prises en charge.

| Propriété                         | Description                                                  | Obligatoire |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | La propriété type doit être définie **SapTableSource**.       | Oui      |
| rowCount                         | Nombre de lignes à récupérer.                              | Non        |
| rfcTableFields                   | Champs à copier à partir de la table SAP. Par exemple : `column0, column1`. | Non       |
| rfcTableOptions                  | Options pour filtrer les lignes dans la Table SAP. Par exemple : `COLUMN0 EQ 'SOMEVALUE'`. | Non       |
| customRfcReadTableFunctionModule | Module de fonction RFC personnalisé qui peut être utilisé pour lire les données à partir de la Table SAP. | Non       |
| partitionOption                  | Le mécanisme de partition pour lire à partir de la table SAP. Les options prises en charge sont les suivantes : <br/>- **None**<br/>- **PartitionOnInt** (entier normal ou aux valeurs entières avec des zéros à gauche, telles que 0000012345)<br/>- **PartitionOnCalendarYear** (4 chiffres au format « Aaaa »)<br/>- **PartitionOnCalendarMonth** (6 chiffres au format « YYYYMM »)<br/>- **PartitionOnCalendarDate** (8 chiffres au format « AAAAMMJJ ») | Non        |
| partitionColumnName              | Le nom de la colonne à partitionner les données. | Non        |
| partitionUpperBound              | La valeur maximale de la colonne spécifiée dans `partitionColumnName` qui sera utilisé pour le partitionnement de continuer. | Non       |
| partitionLowerBound              | La valeur minimale de la colonne spécifiée dans `partitionColumnName` qui sera utilisé pour le partitionnement de continuer. | Non        |
| maxPartitionsNumber              | Le nombre maximal de partitions pour fractionner les données. | Non        |

>[!TIP]
>- Si votre table SAP comporte un gros volume de données tels que plusieurs milliards de lignes, utilisez `partitionOption` et `partitionSetting` pour fractionner les données en petites partitions, auquel cas les données sont lues par les partitions et chaque partition de données est récupérée à partir de votre serveur SAP via un seul Appel RFC.<br/>
>- En prenant `partitionOption` comme `partitionOnInt` par exemple, le nombre de lignes dans chaque partition est calculé en (nombre total de lignes comprise entre *partitionUpperBound* et *partitionLowerBound*) /*maxPartitionsNumber*.<br/>
>- Si vous souhaitez exécuter davantage de partitions en parallèle pour accélérer la copie, il est fortement recommandé d’effectuer `maxPartitionsNumber` comme un multiple de la valeur de `parallelCopies` (en savoir plus à partir de [copie parallèle](copy-activity-performance.md#parallel-copy)).

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-table"></a>Mappage de type de données pour la Table SAP

Lors de la copie des données à partir de la Table SAP, les mappages suivants sont utilisés entre les types de données de Table de SAP pour les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type SAP ABAP | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| C (Chaîne) | String |
| J’ai (entier) | Int32 |
| F (Float) | Double |
| D (Date) | Chaîne |
| T (Heure) | Chaîne |
| P (« packed » DCB, Devise, Décimal, Qté) | Decimal |
| N (numérique) | String |
| X (données binaires et brutes) | Chaîne |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
