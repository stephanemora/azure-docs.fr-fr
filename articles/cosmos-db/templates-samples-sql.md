---
title: Modèles Azure Resource Manager pour Core (API SQL) Azure Cosmos DB
description: Utilisez des modèles Azure Resource Manager pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: bb39414e73ec3df2d2714d794f7060815b99bce1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531982"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Modèles Azure Resource Manager pour Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cet article présente uniquement des exemples de modèles Azure Resource Manager pour des comptes de l’API Core (SQL). Vous trouverez également des exemples de modèles pour les API [Cassandra](cassandra/templates-samples.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](mongodb/resource-manager-template-samples.md) et [Table](table/resource-manager-templates.md).

## <a name="core-sql-api"></a>API Core (SQL)

|**Modèle**|**Description**|
|---|---|
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un débit de mise à l’échelle automatique](manage-with-templates.md#create-autoscale) | Ce modèle crée un compte d’API Core (SQL) dans deux régions, une base de données et un conteneur avec un débit de mise à l’échelle automatique. |
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un magasin analytique](manage-with-templates.md#create-analytical-store) | Ce modèle crée un compte d’API Core (SQL) dans une région avec un conteneur configuré avec une durée de vie Analytical TTL activée et une option d’utilisation de débit manuel ou de mise à l’échelle automatique. |
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un débit (manuel) standard](manage-with-templates.md#create-manual) | Ce modèle crée un compte d’API Core (SQL) dans deux régions, une base de données et un conteneur avec un débit standard. |
|[Créer un compte Azure Cosmos, une base de données et un conteneur avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur](manage-with-templates.md#create-sproc) | Ce modèle crée un compte d’API Core (SQL) dans deux régions avec une procédure stockée, un déclencheur et un UDF pour un conteneur. |
|[Créer un compte Azure Cosmos avec l’identité Azure AD, les définitions de rôle et l’attribution de rôle](manage-with-templates.md#create-rbac) | Ce modèle crée un compte d’API Core (SQL) avec l’identité Azure AD, les définitions de rôle et l’attribution de rôle sur un principal de service. |
|[Créer un point de terminaison privé pour un compte Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ce modèle crée un point de terminaison privé pour un compte d’API Core (SQL) Azure Cosmos existant dans un réseau virtuel existant. |
|[Créer un compte Azure Cosmos gratuit](manage-with-templates.md#free-tier) |  Ce modèle crée un compte d’API Core (SQL) Azure Cosmos DB au niveau de service gratuit. |

Pour consulter la documentation de référence, voir [Référence Azure Resource Manager pour Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions).
