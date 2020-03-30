---
title: Utilisation de comptes Azure Cosmos DB
description: Cet article décrit comment créer et utiliser des comptes Azure Cosmos. Il montre également la hiérarchie de ces différents éléments dans un compte Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225753"
---
# <a name="work-with-azure-cosmos-account"></a>Utiliser un compte Azure Cosmos

Azure Cosmos DB est une plateforme en tant que service (Paas) entièrement gérée. Pour commencer à utiliser Azure Cosmos DB, vous devez initialement créer un compte Azure Cosmos dans votre abonnement Azure. Votre compte Azure Cosmos contient un nom DNS unique et vous pouvez gérer un compte à l’aide du portail Azure, Azure CLI ou à l’aide de différents Kits de développement logiciel (SDK) spécifiques à une langue. Pour en savoir plus, consultez [Comment gérer votre compte Azure Cosmos](how-to-manage-database-account.md).

Le compte Azure Cosmos est une unité fondamentale de distribution mondiale et de haute disponibilité. Vous pouvez ajouter et supprimer des régions Azure à votre compte Azure Cosmos à tout moment pour distribuer globalement vos données et votre débit dans plusieurs régions Azure. Vous pouvez configurer votre compte Azure Cosmos pour une seule ou plusieurs régions d’écriture. Pour en savoir plus, consultez [Comment ajouter et supprimer des régions Azure à votre compte Azure Cosmos](how-to-manage-database-account.md). Vous pouvez configurer le [niveau de cohérence par défaut](consistency-levels.md) sur votre compte Azure Cosmos DB. Azure Cosmos DB fournit des contrats de niveau de service (SLA) complets englobant le débit, la latence au 99ème centile, la cohérence et une haute disponibilité. Pour en savoir plus, consultez [Contrats de niveau de service (SLA) Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Pour gérer en toute sécurité l’accès à toutes les données au sein de votre compte Azure Cosmos, vous pouvez utiliser les [clés principales](secure-access-to-data.md) associées à votre compte. Pour sécuriser davantage l’accès à vos données, vous pouvez configurer un [point de terminaison de service Vnet](vnet-service-endpoint.md) et un [pare-feu IP](firewall-support.md) sur votre compte Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Éléments d’un compte Azure Cosmos

Le conteneur Azure Cosmos est l’unité d’extensibilité de base. Vous pouvez pratiquement avoir un débit approvisionné illimité (RU/s) et un stockage sur un conteneur. Azure Cosmos DB partitionne votre conteneur en toute transparence à l’aide de la clé de partition logique que vous spécifiez pour mettre à l’échelle votre débit approvisionné et votre stockage. Pour en savoir plus, consultez [Utilisation des conteneurs et éléments Azure Cosmos](databases-containers-items.md).

Actuellement, vous pouvez créer un maximum de 100 comptes Azure Cosmos pour un abonnement Azure. Un seul compte Azure Cosmos peut gérer une quantité de données et un débit provisionné pratiquement illimités. Pour gérer vos données et votre débit provisionné, vous pouvez créer une ou plusieurs bases de données Azure Cosmos pour votre compte, puis, au sein de ces bases de données, créer un ou plusieurs conteneurs. L’image suivante illustre la hiérarchie de ces différents éléments dans un compte Azure Cosmos :

![Hiérarchie d’un compte Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment gérer votre compte Azure Cosmos et d’autres concepts :

* [Comment gérer votre compte Azure Cosmos](how-to-manage-database-account.md)
* [Diffusion mondiale](distribute-data-globally.md)
* [Niveaux de cohérence](consistency-levels.md)
* [Utilisation des bases de données, des conteneurs et des éléments Azure Cosmos](databases-containers-items.md)
* [Point de terminaison du service de réseau virtuel pour votre compte Azure Cosmos](vnet-service-endpoint.md)
* [Pare-feu IP de votre compte Azure Cosmos](firewall-support.md)
* [Comment ajouter et supprimer des régions Azure à votre compte Azure Cosmos](how-to-manage-database-account.md)
* [Contrats de niveau de service Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
