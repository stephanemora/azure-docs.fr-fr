---
title: Modèles Azure Resource Manager pour Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961857"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modèles Azure Resource Manager pour Azure Cosmos DB

Les tableaux suivants incluent des liens vers des modèles Azure Resource Manager pour Azure Cosmos DB :

## <a name="sql-core-api"></a>API SQL (Core)

|**Modèle**|**Description**|
|---|---|
|[Créer un compte, une base de données et un conteneur Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ce modèle crée un compte d’API SQL (Core) dans deux régions avec deux conteneurs avec un débit de base de données partagé et un conteneur avec un débit dédié. Le débit peut être mis à jour en renvoyant le modèle avec une valeur de propriété de débit mise à jour. |
|[Créer un compte Azure Cosmos, une base de données et un conteneur avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur](manage-sql-with-resource-manager.md#create-sproc) | Ce modèle crée un compte d’API SQL (Core) dans deux régions avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur (UDF) pour un conteneur. |

## <a name="mongodb-api"></a>API MongoDB

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, une base de données et une collection Azure Cosmos](manage-mongodb-with-resource-manager.md#create-resource) | Ce modèle crée un compte via l’API Azure Cosmos DB pour MongoDB dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose de deux conteneurs qui partagent le débit du niveau de la base de données. |

## <a name="cassandra-api"></a>API Cassandra

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, un espace de clés et une table Azure Cosmos](manage-cassandra-with-resource-manager.md#create-resource) | Ce modèle crée un compte API Cassandra dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose de deux tables qui partagent le débit du niveau de l’espace de clés. |

## <a name="gremlin-api"></a>API Gremlin

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, une base de données et un graph Azure Cosmos](manage-gremlin-with-resource-manager.md#create-resource) | Ce modèle crée un compte API Gremlin dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose de deux graphs qui partagent le débit du niveau de la base de données. |

## <a name="table-api"></a>API de table

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte et une table Azure Cosmos](manage-table-with-resource-manager.md#create-resource) | Ce modèle crée un compte API Table dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose d’une seule table. |

> [!TIP]
> Pour activer le débit partagé lorsque vous utilisez l’API Table, activez le débit au niveau d’un compte dans le portail Azure.

Pour consulter la documentation de référence, voir [Référence Azure Resource Manager pour Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions).
