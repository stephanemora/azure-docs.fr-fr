---
title: Copier des données à partir de SAP Business Warehouse via Open Hub à l'aide d'Azure Data Factory | Microsoft Docs
description: Apprenez à utiliser l'activité de copie dans un pipeline Azure Data Factory pour copier des données de SAP Business Warehouse (BW) vers des banques de données réceptrices prises en charge via Open Hub.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 03/08/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c64842dc89c9519c738701558f510940f4cc148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848870"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copier des données à partir de SAP Business Warehouse via Open Hub à l'aide d'Azure Data Factory

Cet article explique comment utiliser l'activité de copie d'Azure Data Factory pour copier des données à partir d'un SAP Business Warehouse (BW) via Open Hub. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de SAP Business Warehouse vers une banque de données réceptrice prise en charge via Open Hub. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SAP Business Warehouse Open Hub prend en charge ce qui suit :

- SAP Business Warehouse **version 7.01 ou une version ultérieure (dans une récente SAP prise en charge de Package pile publiée après l’année 2015)**.
- Copie de données via la table OHD locale qui peut être DSO, InfoCube, MultiProvider, DataSource, etc.
- Copie de données en utilisant une authentification de base.
- Connexion au serveur d'applications.

## <a name="sap-bw-open-hub-integration"></a>Intégration de SAP BW Open Hub 

Le service [SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) permet d'extraire efficacement des données à partir de SAP BW. Le schéma suivant illustre l'un des flux typiques dont disposent les clients dans leur système SAP, et dans lequel les données circulent comme suit : SAP ECC -> PSA -> DSO -> Cube.

La destination SAP BW Open Hub (OHD) définit la cible vers laquelle les données SAP sont relayées. Les objets pris en charge par processus de transfert des données de SAP (DTP) peuvent être utilisés comme sources de données hub open, par exemple, DSO, InfoCube, source de données, etc. Les types de destination Open Hub, où les données relayées sont stockées, peuvent être des tables de bases de données (locales ou distantes) et des fichiers plats. Ce connecteur SAP BW Open Hub prend en charge la copie de données de la table OHD locale dans BW. Si vous utilisez d'autres types, vous pouvez vous connecter directement à la base de données ou au système de fichiers à l'aide d'autres connecteurs.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Flux d’extraction de delta

ADF SAP BW Connector Open Hub offre deux propriétés facultatives : `excludeLastRequest` et `baseRequestId` qui peut être utilisé pour gérer la charge de delta à partir de Open Hub. 

- **excludeLastRequestId**: Indique s'il faut exclure les enregistrements de la dernière requête. Valeur par défaut est true. 
- **baseRequestId**: ID de requête pour le chargement delta. Une fois qu’il est défini, seules les données avec ID de demande supérieur à la valeur de cette propriété seront récupérées. 

En général, l’extraction à partir de SAP InfoProviders à Azure Data Factory (ADF) consistant en 2 étapes : 

1. **Données de transfert de processus (DTP) pour SAP BW** cette étape copie les données à partir d’un fournisseur d’infos SAP BW dans une table de SAP BW Open Hub 

1. **Copie des données ADF** dans cette étape, la table Open Hub est lu par le connecteur ADF 

![Flux d’extraction de delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Dans la première étape, un processus DTP est exécutée. Chaque exécution crée un nouvel ID de demande SAP. L’ID de demande est stocké dans la table Open Hub et est ensuite utilisé par le connecteur ADF pour identifier le delta. Les deux étapes exécutent de façon asynchrone : le processus DTP est déclenché par SAP, et la copie des données ADF est déclenchée par le biais ADF. 

Par défaut, ADF ne lit pas la dernière delta à partir de la table Open Hub (option « exclude dernière demande » est true). Les présentes, les données dans ADF ne sont pas à jour avec les données dans le tableau Open Hub (le dernier delta est manquant) à 100 %. En retour, cette procédure permet de s’assurer qu’aucune ligne ne dissimulées provoquée par l’extraction asynchrone. Cela fonctionne bien même lorsque ADF lit la table Open Hub pendant le processus DTP est toujours écrit dans la même table. 

En général, vous stockez l’ID de demande copié max dans la dernière exécution par ADF dans un magasin de données intermédiaire (par exemple, Azure Blob dans diagramme ci-dessus). Par conséquent, la même demande sans être lu une deuxième fois par ADF dans l’exécution ultérieure. Pendant ce temps, notez que les données ne sont pas automatiquement supprimées de la table Open Hub.

Delta appropriée la manipulation des ne sont pas autorisée pour avoir des ID à partir de différents PDT de demande dans la même table Open Hub. Par conséquent, vous ne devez pas créer plusieurs DTP pour chaque Destination de Hub Open (OHD). Lorsque vous avez besoin d’extraction complète et Delta à partir du même fournisseur d’infos, vous devez créer deux OHDs pour le même fournisseur d’infos. 

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser ce connecteur SAP Business Warehouse Open Hub, vous devez suivre les instructions ci-dessous :

- Configurez un runtime d'intégration auto-hébergé avec la version 3.13 ou ultérieure. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).

- Téléchargez la version **64 bits de [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** à partir du site web de SAP et installez-la sur la machine dotée du runtime d'intégration auto-hébergé. Lors de l'installation, dans la fenêtre des étapes de configuration facultatives, sélectionnez l'option **Installer les assemblys dans le GAC**, comme illustré ci-dessous. 

    ![Installer SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L'utilisateur SAP utilisé dans le connecteur Data Factory BW doit disposer des autorisations suivantes : 

    - Autorisation relative à RFC et SAP BW. 
    - Autorisations relatives à l'activité « Exécuter » de l'objet d'autorisation « S_SDSAUTH ».

- Créez le type de destination Open Hub en le définissant sur **Table de base de données** et en cochant l'option « Clé technique ».  Il est également recommandé de ne pas cocher la case Suppression des données de la table, bien que cela ne soit pas obligatoire. Utilisez le DTP (exécution directe ou intégration dans la chaîne de processus existante) pour transférer les données de l'objet source (tel que le cube) que vous avez choisi vers la table de destination Open Hub.

## <a name="getting-started"></a>Prise en main

> [!TIP]
>
> Pour obtenir une description de l’utilisation du connecteur de SAP BW Open Hub, consultez [charger des données à partir de SAP Business Warehouse (BW) à l’aide d’Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques au connecteur SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP Business Warehouse Open Hub sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **SapOpenHub** | Oui |
| serveur | Nom du serveur sur lequel réside l’instance SAP BW. | Oui |
| systemNumber | Numéro de système du système SAP BW.<br/>Valeur autorisée : nombre décimal à deux chiffres représenté sous forme de chaîne. | Oui |
| clientId | ID client du client dans le système SAP W.<br/>Valeur autorisée : nombre décimal à trois chiffres représenté sous forme de chaîne. | Oui |
| langage | Langue utilisée par le système SAP. | Non (la valeur par défaut est **EN**)|
| userName | Nom de l’utilisateur ayant accès au serveur SAP. | Oui |
| password | Mot de passe pour l’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |Oui |

**Exemple :**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Salesforce.

Pour copier des données depuis et vers SAP BW Open Hub, définissez la propriété type du jeu de données sur **SapOpenHubTable**. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur **SapOpenHubTable**.  | Oui |
| openHubDestinationName | Nom de la destination Open Hub à partir de laquelle les données doivent être copiées. | Oui |
| excludeLastRequest | Indique s'il faut exclure les enregistrements de la dernière requête. | Non (la valeur par défaut est **true**) |
| baseRequestId | ID de requête pour le chargement delta. Une fois ceci défini, seules les données dont le requestId est **supérieur à** la valeur de cette propriété sont récupérées.  | Non  |

>[!TIP]
>Si votre table Open Hub ne contient que les données générées par un seul ID de requête, par exemple, vous exécutez toujours un plein chargement et écrasez les données existantes dans la table, ou si vous n'exécutez le DTP qu'une fois à des fins de test, n'oubliez pas de décocher l'option « excludeLastRequest » afin de copier les données.

**Exemple :**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub en tant que source

Pour copier des données à partir de SAP BW Open Hub, définissez **SapOpenHubSource** comme type de source dans l'activité de copie. Bien qu'aucune autre propriété spécifique au type ne soit nécessaire dans la section **source** de l'activité de copie.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mappage de type de données pour SAP BW Open Hub

Lors de la copie de données à partir de SAP BW Open Hub, les mappages suivants sont utilisés entre les types de données SAP BW et les types de données intermédiaires d'Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, voir [Mappages de schémas et de types de données](copy-activity-schema-and-type-mapping.md).

| Type SAP ABAP | Type de données intermédiaires de Data Factory |
|:--- |:--- |
| C (Chaîne) | Chaîne |
| I (entier) | Int32 |
| F (Float) | Double |
| D (Date) | Chaîne |
| T (Heure) | Chaîne |
| P (« packed » DCB, Devise, Décimal, Qté) | Decimal |
| N (Numc) | Chaîne |
| X (données binaires et brutes) | Chaîne |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
