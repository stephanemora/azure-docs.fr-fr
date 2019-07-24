---
title: Modèles Azure Resource Manager pour Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969182"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modèles Azure Resource Manager pour Azure Cosmos DB

Les tableaux suivants incluent des liens vers des modèles Azure Resource Manager pour Azure Cosmos DB :

## <a name="sql-core-api"></a>API SQL (Core)

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte, une base de données et un conteneur Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ce modèle crée un compte API SQL (Core) dans deux régions avec multimaître activé. Le compte Azure Cosmos dispose de deux conteneurs qui partagent le débit du niveau de la base de données. |
|[Mettre à jour le débit (RU/s) d’une base de données](manage-sql-with-resource-manager.md#database-ru-update) | Ce modèle met à jour le débit d’une base de données dans un compte API SQL (Core). |
|[Mettre à jour le débit (RU/s) d’un conteneur](manage-sql-with-resource-manager.md#container-ru-update) | Ce modèle met à jour le débit d’un conteneur dans un compte API SQL (Core). |

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