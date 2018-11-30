---
title: Partitionnement dans Azure Cosmos DB
description: Vue d’ensemble du partitionnement dans Azure Cosmos DB
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: b89830d566b36b0446836d8f32aee5756e2d0991
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498431"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB

Le partitionnement est la technique utilisée par Cosmos DB pour procéder à la mise à l'échelle des conteneurs individuels au sein d'une base de données afin de répondre aux besoins de performances de votre application. Grâce au partitionnement, les éléments d'un conteneur sont répartis en sous-ensembles distincts, appelés partitions logiques. Les partitions logiques sont créées en fonction de la valeur d'une propriété de clé de partition associée à chaque élément.

Une partition logique est un sous-ensemble distinct d'éléments situés dans un conteneur. Les éléments présents dans une partition logique sont identifiés par la valeur de la clé de partition partagée par tous les éléments de la partition logique.  Prenons l'exemple d'un conteneur incluant des documents qui comportent chacun une propriété `UserID`.  Si `UserID` sert de clé de partition pour les éléments présents dans un conteneur et qu'il existe 1 000 valeurs `UserID` uniques, 1 000 partitions logiques seront créées pour le conteneur.

Chacun des éléments présents dans un conteneur possède une **clé de partition** qui détermine la **partition logique** de l'élément. Il possède également un **identifiant d'élément** (unique dans une partition logique).  L'**index** d'un élément l'identifie de manière unique, et il est formé en combinant la clé de partition et l'identifiant de l'élément.

Le choix d’une clé de partition est une décision importante qui déterminera les performances de votre application.  Pour des instructions détaillées, consultez l'article [Choix d'une clé de partition](partitioning-overview.md#choose-partitionkey).

## <a name="logical-partition-management"></a>Gestion des partitions logiques

Cosmos DB gère de manière transparente et automatique le positionnement des partitions logiques sur les partitions physiques (infrastructure de serveur) afin de répondre efficacement aux besoins d'extensibilité et de performances du conteneur. À mesure que les exigences de l'application augmentent en matière de débit et de stockage, Cosmos DB déplace les partitions logiques afin de répartir automatiquement la charge sur un plus grand nombre de serveurs. Pour en savoir plus sur la façon dont Cosmos DB gère les partitions, consultez l'article [Partitions logiques](partition-data.md). Il n'est pas nécessaire de connaître ces détails pour créer ou exécuter vos applications.

Cosmos DB utilise un partitionnement basé sur un hachage pour répartir les partitions logiques sur des partitions physiques.  La valeur de la clé de partition d'un élément est hachée par Cosmos DB, et le résultat du hachage détermine la partition physique. Cosmos DB alloue l’espace des hachages de clé de partition uniformément entre les « N » partitions physiques.

Les requêtes qui accèdent aux données d'une même partition sont plus économiques que les requêtes qui accèdent à plusieurs partitions. Les transactions (dans les procédures stockées ou dans les déclencheurs) ne sont autorisées que pour les éléments d'une même partition logique.  

## <a id="choose-partitionkey"></a>Choix d’une clé de partition

Lors du choix d’une clé de partition, tenez compte des détails suivants :

* La limite supérieure de stockage d'une même partition logique est de 10 Go.  

* Les conteneurs partitionnés sont configurés avec un débit minimum de 400 unités de requête/seconde. Les requêtes pour la même clé de partition ne peuvent pas dépasser le débit alloué à une partition. Si elles dépassent le débit alloué, elles sont limitées. Il est donc important de choisir une clé de partition qui ne crée pas de « zones réactives » au sein de votre application.

* Choisissez une clé de partition qui répartit la charge de travail de manière uniforme sur toutes les partitions et dans le temps.  Votre choix de clé de partition doit équilibrer le besoin de requêtes et/ou de transactions de partition efficaces par rapport à l'objectif de répartition des éléments sur plusieurs partitions afin de garantir l'extensibilité.

* Choisissez une clé de partition dotée d'un large éventail de valeurs et de modèles d'accès répartis uniformément sur les partitions logiques. L'idée de base est de répartir les données et l'activité de votre conteneur sur l'ensemble des partitions logiques afin que les ressources de débit et de stockage des données puissent être réparties sur les partitions logiques.

* Les candidats aux clés de partition peuvent inclure les propriétés qui apparaissent fréquemment en tant que filtre dans vos requêtes. Les requêtes peuvent être efficacement acheminées en incluant la clé de partition dans le prédicat de filtre.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [partitions](partition-data.md)
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md)
* En savoir plus sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md)
