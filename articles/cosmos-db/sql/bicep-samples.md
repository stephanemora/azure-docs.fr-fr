---
title: Exemples Bicep pour Azure Cosmos DB Core (API SQL)
description: Utilisez Bicep pour créer et configurer Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 8cab03d2b3cd3c6d7b6a2f9bd15c686bea529de9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700672"
---
# <a name="bicep-for-azure-cosmos-db"></a>Bicep pour Azure Cosmos DB

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cet article présente des exemples Bicep pour des comptes d’API Core (SQL). Vous trouverez également des exemples Bicep pour les API [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md) et [Table](../table/manage-with-bicep.md).

## <a name="core-sql-api"></a>API Core (SQL)

|**Exemple**|**Description**|
|---|---|
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un débit de mise à l’échelle automatique](manage-with-bicep.md#create-autoscale) | Crée un compte d’API Core (SQL) dans deux régions, une base de données et un conteneur avec un débit avec mise à l’échelle automatique. |
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un magasin analytique](manage-with-bicep.md#create-analytical-store) | Crée un compte d’API Core (SQL) dans une région avec un conteneur configuré avec une durée de vie analytique activée, et une option pour utiliser un débit manuel ou avec mise à l’échelle automatique. |
|[Créer un compte, une base de données et un conteneur Azure Cosmos avec un débit (manuel) standard](manage-with-bicep.md#create-manual) | Crée un compte d’API Core (SQL) dans deux régions, une base de données et un conteneur avec un débit standard. |
|[Créer un compte Azure Cosmos, une base de données et un conteneur avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur](manage-with-bicep.md#create-sproc) | Crée un compte d’API Core (SQL) dans deux régions avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur pour un conteneur. |
|[Créer un compte Azure Cosmos avec l’identité Azure AD, les définitions de rôle et l’attribution de rôle](manage-with-bicep.md#create-rbac) | Crée un compte d’API Core (SQL) avec une identité Azure AD, des définitions de rôle et une attribution de rôle sur un principal de service. |
|[Créer un compte Azure Cosmos gratuit](manage-with-bicep.md#free-tier) |  Crée un compte d’API Core (SQL) Azure Cosmos DB sur un niveau gratuit. |

## <a name="next-steps"></a>Étapes suivantes

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.

* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md)
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
