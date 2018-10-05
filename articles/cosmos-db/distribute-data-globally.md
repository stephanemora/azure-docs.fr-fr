---
title: Distribution mondiale des données avec Azure Cosmos DB | Microsoft Docs
description: Découvrez plus d’informations sur la géoréplication à l’échelle de la planète, le multimaître, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure Cosmos DB, service de base de données multimodèle distribué mondialement.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408893"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Distribution de données mondiale avec Azure Cosmos DB

Avec plus de 50 régions géographiques, Azure est partout et continue de s’étendre. Présente dans le monde entier et offrant une prise en charge de la fonction multimaître, l’une des fonctionnalités différenciées qu’Azure propose à ses développeurs est la possibilité de générer, de déployer et de gérer facilement des applications mondialement distribuées.

[Azure Cosmos DB](../cosmos-db/introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la distribution mondiale clés en main, la [mise à l’échelle élastique du débit et du stockage](../cosmos-db/partition-data.md), des latences de l’ordre de quelques millisecondes de lecture et d’écriture dans le monde entier dans plus de 99 % des cas, des [modèles de cohérence bien définis](consistency-levels.md) et la garantie d’une haute disponibilité, le tout soutenu par des [contrats SLA complets de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement toutes vos données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans que vous ayez à vous occuper de la gestion des schémas ou des index.

## <a name="global-distribution-with-multi-master"></a>Diffusion mondiale avec la fonction multimaître

En tant que service cloud, Azure Cosmos DB est conçu avec soin pour prendre en charge l’architecture mutualisée, la distribution mondiale et la fonction multimaître pour les modèles de données de types documents, valeurs clés, graphes et colonnes.

![Conteneur Azure Cosmos DB partitionné et distribué dans trois régions](./media/distribute-data-globally/global-apps.png)

**Conteneur Azure Cosmos DB unique, partitionné et distribué dans plusieurs régions Azure**

Comme nous l’avons appris au moment de la création d’Azure Cosmos DB, l’ajout de la distribution globale ne peut pas venir après coup. Cette fonction ne peut pas être « rajoutée » sur un système de base de données à un seul site. Les possibilités offertes par une base de données mondialement distribuée s’étendent au-delà des fonctionnalités proposées par une reprise d’activité après sinistre géographique (géo-reprise d’activité) traditionnelle disponible avec les bases de données à un seul site. Les bases de données à un seul site offrant la fonctionnalité de récupération d’urgence géographique sont un sous-ensemble strict des bases de données mondialement distribuées.

Avec la distribution mondiale clé en main d’Azure Cosmos DB, les développeurs n’ont pas à créer leur propre génération de modèles automatique de réplication en utilisant le modèle d’expression Lambda sur le journal de base de données ou en effectuant des « doubles écritures » dans plusieurs régions. Nous *déconseillons* ces approches, car il est impossible de garantir leur exactitude et de fournir des contrats de niveau de service fiables.

## <a id="Next Steps"></a>Étapes suivantes

* [Comment Azure Cosmos DB permet une distribution mondiale clé en main](distribute-data-globally-turnkey.md)

* [Principaux avantages de la distribution mondiale d’Azure Cosmos DB](distribute-data-globally-benefits.md)

* [Guide pratique pour configurer la réplication de base de données mondiale dans Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Guide pratique pour activer le multimaître pour les comptes Azure Cosmos DB](enable-multi-master.md)

* [Fonctionnement du basculement manuel et automatique dans Azure Cosmos DB](regional-failover.md)

* [Présentation de la résolution des conflits dans Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Utilisation du multimaître avec des bases de données NoSQL open source](multi-master-oss-nosql.md)
