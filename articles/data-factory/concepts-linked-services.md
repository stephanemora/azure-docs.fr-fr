---
title: Services liés dans Azure Data Factory | Microsoft Docs
description: En savoir plus sur les services liés dans Data Factory. Les services liés lient le calcul et les banques de données à une fabrique de données.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870131"
---
# <a name="linked-services-in-azure-data-factory"></a>Services liés dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Version actuelle](concepts-datasets-linked-services.md)

Cet article décrit les services liés, comment ils sont définis au format JSON, et comment ils sont utilisés dans les pipelines Azure Data Factory.

Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md) pour obtenir une vue d’ensemble.

## <a name="overview"></a>Présentation
Une fabrique de données peut avoir un ou plusieurs pipelines. Un **pipeline** constitue un regroupement logique d’**activités** qui exécutent ensemble une tâche. Les activités d’un pipeline définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’un serveur SQL Server local vers un stockage Blob Azure. Ensuite, vous pouvez utiliser une activité Hive qui exécute un script Hive sur un cluster Azure HDInsight pour traiter les données du stockage Blob afin de produire des données de sortie. Enfin, vous pouvez utiliser une deuxième activité de copie pour copier les données de sortie dans Azure SQL Data Warehouse sur lequel des solutions de génération de rapports décisionnelles sont développées. Pour plus d’informations sur les pipelines et les activités, voir [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md).

À présent, un **jeu de données** est une vue de données nommée qui pointe ou fait référence simplement aux données que vous souhaitez utiliser dans vos **activités** en tant qu’entrées et sorties.

Avant de créer un jeu de données, vous devez créer un **service lié** pour lier votre banque de données à la fabrique de données. Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Considérez les choses de la façon suivante : le jeu de données représente la structure des données à l’intérieur des magasins de données liés, et le service lié définit la connexion à la source de données. Par exemple, un service lié de stockage Azure relie un compte de stockage à la fabrique de données. Un jeu de données d'objets blob représente le conteneur d’objets blob et le dossier à l’intérieur de ce compte de stockage Azure contenant les objets blob d’entrée à traiter.

Voici un exemple de scénario. Pour copier des données du stockage Blob vers une base de données SQL, vous devez créer deux services liés : Stockage Azure et Azure SQL Database. Créez ensuite deux jeux de données : le jeu de données d’objets blob Azure (qui fait référence au service lié Stockage Azure) et le jeu de données de table Azure SQL (qui fait référence au service lié Azure SQL Database). Les services liés Stockage Azure et Azure SQL Database contiennent des chaînes de connexion utilisées par la fabrique de données pendant l’exécution pour se connecter à votre instance Stockage Azure et Azure SQL Database, respectivement. Le jeu de données d’objets blob Azure spécifie le conteneur d’objets blob et le dossier d’objets blob qui contient les objets blob d’entrée dans votre stockage Blob. Le jeu de données de table SQL Azure spécifie la table SQL dans votre base de données SQL dans laquelle les données doivent être copiées.

Le diagramme suivant montre la relation entre le pipeline, l’activité, le jeu de données et le service lié dans la fabrique de données :

![Relation entre le pipeline, l’activité, le jeu de données et les services liés](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Service lié JSON
Un service lié dans Data Factory se définit de la façon suivante au format JSON :

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

La table suivante décrit les propriétés dans le JSON ci-dessus :

Propriété | Description | Obligatoire |
-------- | ----------- | -------- |
Nom | Nom du service lié. Voir [Azure Data Factory - Règles d’affectation des noms](naming-rules.md). |  Oui |
Type | Type du service lié. Par exemple :  AzureStorage (magasin de données) ou AzureBatch (calcul). Consultez la description de typeProperties. | Oui |
typeProperties | Les propriétés type sont différentes pour chaque magasin de données et chaque unité de calcul. <br/><br/> Vous trouverez la liste des types de magasins de données pris en charge et leurs propriétés dans le tableau [type de jeu de données](concepts-datasets-linked-services.md#dataset-type) de cet article. Accédez à l’article sur le connecteur de magasin de données pour en savoir plus sur les propriétés de type propres à un magasin de données. <br/><br/> Vous trouverez la liste des types de calcul pris en charge et leurs propriétés sur la page [Services liés de calcul](compute-linked-services.md). | Oui |
connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre magasin de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non 

## <a name="linked-service-example"></a>Exemple de service lié
Le service lié suivant est un service lié Stockage Azure. Notez que le type est défini sur AzureStorage. Les propriétés de type du service lié Stockage Azure comprennent une chaîne de connexion. Le service Data Factory l’utilise pour se connecter au magasin de données à l’exécution.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Créez des services liés
Vous pouvez créer des services liés à l’aide d’un de ces outils ou les kits de développement logiciel : [API .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), modèle Azure Resource Manager et portail Azure

## <a name="data-store-linked-services"></a>Services liés de magasin de données
Connecter des magasins de données se trouve dans notre [magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats). Faire référence à la liste de propriétés de connexion spécifiques nécessaires pour différents magasins.

## <a name="compute-linked-services"></a>Services liés de calcul
Référence [prise en charge des environnements de calcul](compute-linked-services.md) pour plus d’informations sur les différents environnements de calcul vous pouvez vous connecter à votre fabrique de données, ainsi que les différentes configurations.

## <a name="next-steps"></a>Étapes suivantes
Consultez les didacticiels suivants pour obtenir des instructions pas à pas sur la création de pipelines et de jeux de données à l’aide de l’un de ces outils ou Kits de développement logiciel (SDK).

- [Démarrage rapide : créer une fabrique de données à l’aide de .NET](quickstart-create-data-factory-dot-net.md)
- [Démarrage rapide : créer une fabrique de données à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)
- [Démarrage rapide : créer une fabrique de données à l’aide d’une API REST](quickstart-create-data-factory-rest-api.md)
- [Démarrage rapide : créer une fabrique de données à l’aide du portail Azure](quickstart-create-data-factory-portal.md)
