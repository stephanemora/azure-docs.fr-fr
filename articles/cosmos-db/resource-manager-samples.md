---
title: Modèles Azure Resource Manager pour Azure Cosmos DB
description: Utiliser des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078459"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modèles Azure Resource Manager pour Azure Cosmos DB

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour Azure Cosmos DB :

|**Type d’API** | **lier à l’exemple**| **Description** |
|---|---| ---|
|API de base (SQL)| [Créer un compte Azure Cosmos DB (multimaître)](manage-sql-with-resource-manager.md) | Ce modèle crée un compte d’API SQL dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux conteneurs qui partagent le débit du niveau de la base de données. |
| API MongoDB | [Créer un compte Azure Cosmos DB (multimaître)](manage-mongodb-with-resource-manager.md) | Ce modèle crée un compte à l’aide des API d’Azure Cosmos DB pour MongoDB dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux conteneurs qui partagent le débit du niveau de la base de données. |
| API Cassandra | [Créer un compte Azure Cosmos DB (multimaître)](manage-cassandra-with-resource-manager.md) | Ce modèle crée un compte d’API Cassandra dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux tables qui partagent un débit au niveau de l’espace de clés. |
| API Gremlin| [Créer un compte Azure Cosmos DB (multimaître)](manage-gremlin-with-resource-manager.md) | Ce modèle crée un compte de l’API Gremlin dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura deux graphiques qui partagent le débit du niveau de la base de données. |
| API de table | [Créer un compte Azure Cosmos DB (multimaître)](manage-table-with-resource-manager.md) | Ce modèle crée un compte d’API de Table dans deux régions avec à multiples maîtres activé. Le compte Azure Cosmos aura une seule table. |

> [!TIP]
> Pour activer le débit partagé lors de l’utilisation des API de Table, activez débit au niveau du compte dans le portail Azure.

Consultez [référence ARM pour Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) page pour la documentation de référence.