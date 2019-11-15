---
title: Modèles Azure Resource Manager pour Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ecdfe89e899e0d416784ae32a0d66b335c09e2b6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582810"
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
|[Mettre à jour le débit (RU/s) d’une base de données](manage-mongodb-with-resource-manager.md#database-ru-update) | Ce modèle met à jour le débit d’une base de données dans un compte API MongoDB. |
|[Mettre à jour le débit (RU/s) d’une collection](manage-mongodb-with-resource-manager.md#collection-ru-update) | Ce modèle met à jour le débit d’un conteneur dans un compte API MongoDB. |

## <a name="cassandra-api"></a>API Cassandra

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, un espace de clés et une table Azure Cosmos](manage-cassandra-with-resource-manager.md#create-resource) | Ce modèle crée un compte API Cassandra dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose de deux tables qui partagent le débit du niveau de l’espace de clés. |
|[Mettre à jour le débit (RU/s) d’un espace de clés](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Ce modèle met à jour le débit d’un espace de clés dans un compte API Cassandra. |
|[Mettre à jour le débit (RU/s) d’une table](manage-cassandra-with-resource-manager.md#table-ru-update) | Ce modèle met à jour le débit d’une table dans un compte API Cassandra. |

## <a name="gremlin-api"></a>API Gremlin

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, une base de données et un graph Azure Cosmos](manage-gremlin-with-resource-manager.md#create-resource) | Ce modèle crée un compte API Gremlin dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose de deux graphs qui partagent le débit du niveau de la base de données. |
|[Mettre à jour le débit (RU/s) d’une base de données](manage-gremlin-with-resource-manager.md#database-ru-update) | Ce modèle met à jour le débit d’une base de données dans un compte API Gremlin. |
|[Mettre à jour le débit (RU/s) d’un graph](manage-gremlin-with-resource-manager.md#graph-ru-update) | Ce modèle met à jour le débit d’un graph dans un compte API Gremlin. |

## <a name="table-api"></a>API de table

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte et une table Azure Cosmos](manage-table-with-resource-manager.md#create-resource) | Ce modèle crée un compte API Table dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose d’une seule table. |
|[Mettre à jour le débit (RU/s) d’une table](manage-table-with-resource-manager.md#table-ru-update) | Ce modèle met à jour le débit d’une table dans un compte API Table. |

> [!TIP]
> Pour activer le débit partagé lorsque vous utilisez l’API Table, activez le débit au niveau d’un compte dans le portail Azure.

Consultez la page de [référence ARM pour Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pour vous renseigner.
