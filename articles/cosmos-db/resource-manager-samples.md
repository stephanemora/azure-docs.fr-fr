---
title: Modèles Azure Resource Manager pour Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586148"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modèles Azure Resource Manager pour Azure Cosmos DB

Les tableaux suivants incluent des liens vers des modèles Azure Resource Manager pour Azure Cosmos DB :

## <a name="core-sql-api"></a>API Core (SQL)

|**Modèle**|**Description**|
|---|---|
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un débit de mise à l’échelle automatique](manage-sql-with-resource-manager.md#create-autoscale) | Ce modèle crée un compte d’API Core (SQL) dans deux régions, une base de données et un conteneur avec un débit de mise à l’échelle automatique. |
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un magasin analytique](manage-sql-with-resource-manager.md#create-analytical-store) | Ce modèle crée un compte d’API Core (SQL) dans une région avec un conteneur configuré avec une durée de vie Analytical TTL activée et une option d’utilisation de débit manuel ou de mise à l’échelle automatique. |
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un débit (manuel) standard](manage-sql-with-resource-manager.md#create-manual) | Ce modèle crée un compte d’API Core (SQL) dans deux régions, une base de données et un conteneur avec un débit standard. |
|[Créer un compte Azure Cosmos, une base de données et un conteneur avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur](manage-sql-with-resource-manager.md#create-sproc) | Ce modèle crée un compte d’API Core (SQL) dans deux régions avec une procédure stockée, un déclencheur et un UDF pour un conteneur. |
|[Créer un point de terminaison privé pour un compte Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ce modèle crée un point de terminaison privé pour un compte d’API Core (SQL) Azure Cosmos existant dans un réseau virtuel existant. |
|[Créer un compte Azure Cosmos gratuit](manage-sql-with-resource-manager.md#free-tier) |  Ce modèle crée un compte d’API Core (SQL) Azure Cosmos DB au niveau de service gratuit. |

## <a name="mongodb-api"></a>API MongoDB

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, une base de données et une collection Azure Cosmos avec un débit de mise à l’échelle automatique](manage-mongodb-with-resource-manager.md#create-autoscale) | Ce modèle crée un compte à l’aide de l’API Azure Cosmos DB pour MongoDB dans deux régions avec deux conteneurs qui partagent le débit de mise à l’échelle automatique au niveau de la base de données. |
|[Créer un compte, une base de données et une collection Azure Cosmos avec un débit (manuel) standard](manage-mongodb-with-resource-manager.md#create-manual) | Ce modèle crée un compte à l’aide de l’API Azure Cosmos DB pour MongoDB dans deux régions avec deux conteneurs qui partagent le débit standard au niveau de la base de données. |

## <a name="cassandra-api"></a>API Cassandra

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, un espace de clé, un tableau Azure Cosmos avec un débit de mise à l’échelle automatique](manage-cassandra-with-resource-manager.md#create-autoscale) | Ce modèle crée un compte d’API Cassandra dans deux régions, avec un espace de clé et un tableau avec un débit de mise à l’échelle automatique. |
|[Créer un compte, un espace de clé, un tableau Azure Cosmos avec un débit (manuel) standard](manage-cassandra-with-resource-manager.md#create-manual) | Ce modèle crée un compte d’API Cassandra dans deux régions, avec un espace de clé et un tableau avec un débit manuel. |

## <a name="gremlin-api"></a>API Gremlin

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, une base de données et un graphique Azure Cosmos avec un débit de mise à l’échelle automatique](manage-gremlin-with-resource-manager.md#create-autoscale) | Ce modèle crée un compte d’API Gremlin dans deux régions avec une base de données et un graphique avec un débit de mise à l’échelle automatique. |
|[Créer un compte, une base de données et un graphique Azure Cosmos avec un débit (manuel) standard](manage-gremlin-with-resource-manager.md#create-manual) | Ce modèle crée un compte d’API Gremlin dans deux régions avec une base de données et un graphique avec un débit standard. |

## <a name="table-api"></a>API de table

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, un tableau Azure Cosmos avec un débit de mise à l’échelle automatique](manage-table-with-resource-manager.md#create-autoscale) | Ce modèle crée un compte d’API Table dans deux régions, avec une table unique avec un débit de mise à l’échelle automatique. |
|[Créer un compte, un tableau Azure Cosmos avec un débit (manuel) standard](manage-table-with-resource-manager.md#create-manual) | Ce modèle crée un compte d’API Table dans deux régions, avec une table unique avec un débit standard. |

Pour consulter la documentation de référence, voir [Référence Azure Resource Manager pour Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions).
