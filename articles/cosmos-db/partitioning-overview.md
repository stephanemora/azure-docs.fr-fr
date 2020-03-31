---
title: Partitionnement dans Azure Cosmos DB
description: En savoir plus sur le partitionnement dans Azure Cosmos DB, les meilleures pratiques lors du choix d’une clé de partition et la gestion des partitions logiques
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79227281"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB

Azure Cosmos DB utilise le partitionnement pour procéder à la mise à l’échelle des conteneurs au sein d’une base de données afin de répondre aux besoins de performances de votre application. Avec le partitionnement, les éléments d’un conteneur sont répartis en sous-ensembles distincts, appelés *partitions logiques*. Les partitions logiques sont formées en fonction de la valeur d’une *clé de partition* associée à chaque élément d’un conteneur. Tous les éléments d’une partition logique possèdent la même valeur de clé de partition.

Par exemple, un conteneur contient des éléments. Chaque élément a une valeur unique pour la propriété `UserID`. Si `UserID` sert de clé de partition pour les éléments présents dans un conteneur et qu’il existe 1 000 valeurs `UserID` uniques, 1 000 partitions logiques sont créées pour le conteneur.

Outre une clé de partition qui détermine la partition logique de l’élément, chacun élément présent dans un conteneur possède un *identifiant d’élément* (unique dans une partition logique). La combinaison de la clé de partition et de l’ID de l’élément crée *l’index* de l’élément, qui identifie l’élément de façon unique.

Le [choix d’une clé de partition](partitioning-overview.md#choose-partitionkey) est une décision importante qui déterminera les performances de votre application.

## <a name="managing-logical-partitions"></a>Gestion des partitions logiques

Azure Cosmos DB gère de manière transparente et automatique le positionnement des partitions logiques sur les partitions physiques afin de répondre efficacement aux besoins d’extensibilité et de performances du conteneur. À mesure que les exigences de d’une application augmentent en matière de débit et de stockage, Azure Cosmos DB déplace les partitions logiques afin de répartir automatiquement la charge sur un plus grand nombre de serveurs. 

Azure Cosmos DB utilise un partitionnement basé sur un hachage pour répartir les partitions logiques sur des partitions physiques. Azure Cosmos DB hache la valeur de la clé de partition d’un élément. Le résultat du hachage détermine la partition physique. Azure Cosmos DB alloue ensuite l’espace des hachages de clé de partition uniformément entre les partitions physiques.

Les requêtes qui accèdent aux données d’une même partition logique sont plus économiques que les requêtes qui accèdent à plusieurs partitions. Les transactions (dans les procédures stockées ou dans les déclencheurs) ne sont autorisées que pour les éléments d’une même partition logique.

Pour en savoir plus sur la façon dont Azure Cosmos DB gère les partitions, consultez [Partitions logiques](partition-data.md). (Il n’est pas nécessaire de connaître le fonctionnement interne de la création ou de l’exécution de vos applications mais il n’est décrit ici qu’à titre d’information.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Choix d’une clé de partition

Lors du choix d’une clé de partition, tenez compte des détails suivants :

* La limite supérieure de stockage d’une même partition logique est de 20 Go.  

* Les conteneurs Azure Cosmos ont un débit minimal de 400 unités de requête par seconde (RU/s). Lorsque le débit est approvisionné sur une base de données, le nombre minimum de RU par conteneur est de 100 unités de requête par seconde (RU/s). Les requêtes pour la même clé de partition ne peuvent pas dépasser le débit alloué à une partition. Si elles dépassent le débit alloué, le débit des requêtes est limité. Il est donc important de choisir une clé de partition qui ne crée pas de « zones réactives » au sein de votre application.

* Choisissez une clé de partition dotée d'un large éventail de valeurs et de modèles d'accès répartis uniformément sur les partitions logiques. Cela permet de répartir les données et l’activité de votre conteneur sur l’ensemble des partitions logiques afin que les ressources de débit et de stockage des données puissent être réparties sur les partitions logiques.

* Choisissez une clé de partition qui répartit la charge de travail de manière uniforme sur toutes les partitions et dans le temps. Votre choix de clé de partition doit équilibrer le besoin de requêtes et de transactions de partition efficaces par rapport à l’objectif de répartition des éléments sur plusieurs partitions afin de garantir l’extensibilité.

* Les candidats aux clés de partition peuvent inclure les propriétés qui apparaissent fréquemment en tant que filtre dans vos requêtes. Les requêtes peuvent être efficacement acheminées en incluant la clé de partition dans le prédicat de filtre.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [partitionnement et la mise à l’échelle horizontale dans Azure Cosmos DB](partition-data.md).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
