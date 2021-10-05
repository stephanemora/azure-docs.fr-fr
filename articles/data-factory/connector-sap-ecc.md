---
title: Copier des données à partir de SAP ECC
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory ou Synapse Analytics pour copier des données de SAP ECC vers des banques de données réceptrices prises en charge.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 47e7b51a75569ea1c23910b78a1b5396759381f7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764067"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory-or-synapse-analytics"></a>Copier des données de SAP ECC à l’aide d’Azure Data Factory ou de Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de SAP ECC (SAP Enterprise Central Component). Pour plus d’informations, consultez l’article [Vue d’ensemble d’activité de copie](copy-activity-overview.md).

>[!TIP]
>Pour en savoir plus sur la prise en charge générale de l’intégration de données SAP, consultez le livre blanc [Intégration de données SAP à l’aide d’Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) offrant une présentation détaillée sur chaque connecteur SAP, une comparaison et des conseils.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur SAP ECC est pris en charge pour les activités suivantes :

- [Activité Copy](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)

Vous pouvez copier des données de SAP ECC vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SAP ECC prend en charge ce qui suit :

- Copie de données à partir de SAP ECC sur SAP NetWeaver version 7.0 et ultérieures.
- Copie de données à partir de tous les objets exposés par les services SAP ECC OData, tels que :

  - Tables ou affichages SAP.
  - Objets de l’interface de programmation d’applications métier [BAPI].
  - Extracteurs de données.
  - Des données ou des documents intermédiaires (IDOC) envoyés à SAP Process Integration (PI) qui peuvent être reçus en tant que OData par le biais d’adaptateurs relatifs.

- Copie de données en utilisant une authentification de base.

La version 7.0 ou ultérieure fait référence à la version SAP NetWeaver au lieu de la version SAP ECC. Par exemple, SAP ECC 6.0 EHP 7 dispose en général de la version NetWeaver >=7.4. Si vous n’êtes pas sûr de votre environnement, voici les étapes à suivre pour confirmer la version de votre système SAP :

1. Utilisez l’interface graphique utilisateur SAP pour vous connecter au système SAP. 
2. Accédez à **Système** -> **État**. 
3. Vérifiez la version de SAP_BASIS et assurez-vous qu’elle est supérieure ou égale à 701.  
      :::image type="content" source="./media/connector-sap-table/sap-basis.png" alt-text="Vérifier SAP_BASIS":::

>[!TIP]
>Pour copier des données à partir de SAP ECC via une table ou un affichage SAP, utilisez le connecteur de [Table SAP](connector-sap-table.md), qui est plus rapide et plus évolutif.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce connecteur SAP ECC, vous devez exposer les entités SAP ECC par le biais des services OData via une passerelle SAP. Plus précisément :

- **Configurer la passerelle SAP**. Sur les serveurs avec une version de SAP NetWeaver supérieure à la version 7.4, la passerelle SAP est déjà installée. Pour les versions antérieures, vous devez installer la passerelle SAP incorporée ou le système hub de passerelle SAP avant d’exposer les données SAP ECC par le biais des services OData. Pour configurer la passerelle SAP, consultez le [guide d’installation](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Activer et configurer le service SAP OData**. Vous pouvez activer le service OData par le biais de TCODE SICF en quelques secondes. Vous pouvez également configurer les objets qui doivent être exposés. Pour plus d’informations, consultez la page [guide pas à pas](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bien démarrer

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-sap-ecc-using-ui"></a>Créer un service lié à SAP ECC à l’aide de l’interface utilisateur

Utilisez les étapes suivantes pour créer un service lié à SAP ECC dans l’interface utilisateur du portail Azure.

1. Accédez à l’onglet Gérer dans votre espace de travail Azure Data Factory ou Synapse et sélectionnez Services liés, puis cliquez sur Nouveau :

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Capture d’écran montrant la création d’un service lié avec l’interface utilisateur Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Capture d’écran de la création d’un service lié avec l’interface utilisateur Azure Synapse.":::

2. Recherchez SAP et sélectionnez le connecteur SAP ECC.

    :::image type="content" source="media/connector-sap-ecc/sap-ecc-connector.png" alt-text="Capture d’écran du connecteur SAP ECC.":::    

1. Configurez les informations du service, testez la connexion et créez le nouveau service lié.

    :::image type="content" source="media/connector-sap-ecc/configure-sap-ecc-linked-service.png" alt-text="Capture d’écran de la configuration du service lié pour SAP ECC.":::

## <a name="connector-configuration-details"></a>Informations de configuration du connecteur

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités spécifiques au connecteur SAP ECC.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SAP ECC sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `type` doit être définie sur `SapEcc`. | Oui |
| `url` | L’URL du service OData SAP ECC. | Oui |
| `username` | Le nom d’utilisateur utilisé pour se connecter à SAP ECC. | Non |
| `password` | Le mot de passe en texte en clair utilisé pour se connecter à SAP ECC. | Non |
| `connectVia` | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. Pour plus d’informations, consultez la section [Conditions préalables](#prerequisites). Si vous ne spécifiez pas de runtime, le runtime d’intégration Azure par défaut est utilisé. | Non |

### <a name="example"></a>Exemple

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données](concepts-datasets-linked-services.md). La section suivante fournit la liste des propriétés prises en charge par le jeu de données de SAP ECC.

Pour copier des données de SAP ECC, définissez la propriété `type` du jeu de données sur `SapEccResource`.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `path` | Chemin de l’entité OData SAP ECC. | Oui |

### <a name="example"></a>Exemple

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez [Pipelines](concepts-pipelines-activities.md). La section suivante fournit la liste des propriétés prises en charge par la source de SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC en tant que source

Pour copier des données de SAP ECC, définissez la propriété `type` dans la section `source` de l’activité de copie sur `SapEccSource`.

Les propriétés prises en charge dans la section `source` de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| `type` | La propriété `SapEccSource` de la section `type` d’activité de copie doit être définie sur `source`. | Oui |
| `query` | Les options de requête OData pour filtrer les données. Par exemple :<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Le connecteur SAP ECC copie les données à partir de l’URL combinée :<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Pour plus d’informations, consultez [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Non |
| `sapDataColumnDelimiter` | Caractère unique utilisé comme délimiteur passé à SAP RFC pour fractionner les données de sortie. | Non |
| `httpRequestTimeout` | Délai d’expiration (valeur **TimeSpan**) pour l’obtention d’une réponse par la requête HTTP. Cette valeur correspond au délai d’expiration pour l’obtention d’une réponse, et non au délai d’expiration pour la lecture des données de la réponse. Si elle n’est pas spécifiée, la valeur par défaut est **00:30:00** (30 minutes). | Non |

### <a name="example"></a>Exemple

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mappage de type de données pour SAP ECC

Lorsque vous copiez des données à partir de SAP ECC, les mappages suivants sont utilisés à partir des types de données OData pour les données SAP ECC vers les types de données intermédiaires que le service utilise en interne. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données OData | Type de données de service intermédiaire |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Les types de données complexes ne sont actuellement pas pris en charge.

## <a name="lookup-activity-properties"></a>Propriétés de l’activité Lookup

Pour en savoir plus sur les propriétés, consultez [Activité Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une liste des magasins de données pris en charge comme sources et récepteurs par l’activité de copie, consultez [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
