---
title: Modèles Azure Resource Manager pour Azure Cosmos DB
description: Utiliser des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969182"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modèles Azure Resource Manager pour Azure Cosmos DB

Les tableaux suivants incluent des liens vers des modèles Azure Resource Manager pour Azure Cosmos DB :

## <a name="sql-core-api"></a>API SQL (Core)

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte Azure Cosmos, la base de données, le conteneur](manage-sql-with-resource-manager.md#create-resource) | Ce modèle crée un compte d’API SQL (Core) dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux conteneurs qui partagent le débit du niveau de la base de données. |
|[Mettre à jour de débit (RU/s) pour une base de données](manage-sql-with-resource-manager.md#database-ru-update) | Ce modèle met à jour le débit pour une base de données dans un compte d’API SQL (Core). |
|[Mettre à jour de débit (RU/s) pour un conteneur](manage-sql-with-resource-manager.md#container-ru-update) | Ce modèle met à jour le débit pour un conteneur dans un compte d’API SQL (Core). |

## <a name="mongodb-api"></a>API MongoDB

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte Azure Cosmos, la base de données, la collection](manage-mongodb-with-resource-manager.md#create-resource) | Ce modèle crée un compte à l’aide des API Azure Cosmos DB pour MongoDB dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux conteneurs qui partagent le débit du niveau de la base de données. |
|[Mettre à jour de débit (RU/s) pour une base de données](manage-mongodb-with-resource-manager.md#database-ru-update) | Ce modèle met à jour le débit pour une base de données dans un compte d’API MongoDB. |
|[Mettre à jour de débit (RU/s) pour une collection](manage-mongodb-with-resource-manager.md#collection-ru-update) | Ce modèle met à jour le débit pour un conteneur dans un compte d’API MongoDB. |

## <a name="cassandra-api"></a>API Cassandra

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte Azure Cosmos, l’espace de clés, la table](manage-cassandra-with-resource-manager.md#create-resource) | Ce modèle crée un compte d’API Cassandra dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux tables qui partagent un débit au niveau de l’espace de clés. |
|[Mettre à jour de débit (RU/s) pour un espace de clés](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Ce modèle met à jour le débit pour un espace de clés dans un compte d’API Cassandra. |
|[Mettre à jour de débit (RU/s) pour une table](manage-cassandra-with-resource-manager.md#table-ru-update) | Ce modèle met à jour le débit pour une table dans un compte d’API Cassandra. |

## <a name="gremlin-api"></a>API Gremlin

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte Azure Cosmos, la base de données, le graphique](manage-gremlin-with-resource-manager.md#create-resource) | Ce modèle crée un compte de l’API Gremlin dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux graphiques qui partagent le débit du niveau de la base de données. |
|[Mettre à jour de débit (RU/s) pour une base de données](manage-gremlin-with-resource-manager.md#database-ru-update) | Ce modèle met à jour le débit pour une base de données dans un compte de l’API Gremlin. |
|[Mettre à jour de débit (RU/s) pour un graphique](manage-gremlin-with-resource-manager.md#graph-ru-update) | Ce modèle met à jour le débit pour un graphique dans un compte de l’API Gremlin. |

## <a name="table-api"></a>API de table

|**Modèle**|**Description**|
|---| ---|
|[Créer un compte Azure Cosmos de la table](manage-table-with-resource-manager.md#create-resource) | Ce modèle crée un compte d’API de Table dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura une seule table. |
|[Mettre à jour de débit (RU/s) pour une table](manage-table-with-resource-manager.md#table-ru-update) | Ce modèle met à jour le débit pour une table dans un compte d’API de Table. |

> [!TIP]
> Pour activer le débit partagé lors de l’utilisation des API de Table, activez débit au niveau du compte dans le portail Azure.

Consultez [référence ARM pour Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) page pour la documentation de référence.