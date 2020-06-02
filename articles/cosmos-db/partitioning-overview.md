---
title: Partitionnement dans Azure Cosmos DB
description: En savoir plus sur le partitionnement dans Azure Cosmos DB, les meilleures pratiques lors du choix d’une clé de partition et la gestion des partitions logiques
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: a9368e67abf3c45981cf1f85fe46a2a2799a6877
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864332"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB

Azure Cosmos DB utilise le partitionnement pour procéder à la mise à l’échelle des conteneurs au sein d’une base de données afin de répondre aux besoins de performances de votre application. Avec le partitionnement, les éléments d’un conteneur sont répartis en sous-ensembles distincts, appelés *partitions logiques*. Les partitions logiques sont formées en fonction de la valeur d’une *clé de partition* associée à chaque élément d’un conteneur. Tous les éléments d’une partition logique possèdent la même valeur de clé de partition.

Par exemple, un conteneur contient des éléments. Chaque élément a une valeur unique pour la propriété `UserID`. Si `UserID` sert de clé de partition pour les éléments présents dans un conteneur et qu’il existe 1 000 valeurs `UserID` uniques, 1 000 partitions logiques sont créées pour le conteneur.

Outre une clé de partition qui détermine la partition logique de l’élément, chaque élément présent dans un conteneur possède un *ID d’élément* (unique dans une partition logique). La combinaison de la clé de partition et de l’*ID d’élément* crée l’*index* de l’élément, qui identifie l’élément de façon unique.

Le [choix d’une clé de partition](partitioning-overview.md#choose-partitionkey) est une décision importante qui déterminera les performances de votre application.

## <a name="managing-logical-partitions"></a>Gestion des partitions logiques

Azure Cosmos DB gère de manière transparente et automatique le positionnement des partitions logiques sur les partitions physiques afin de répondre efficacement aux besoins d’extensibilité et de performances du conteneur. À mesure que le débit et le stockage requis par une application augmentent, Azure Cosmos DB déplace les partitions logiques pour répartir automatiquement la charge sur un plus grand nombre de partitions physiques. En savoir plus sur les [partitions physiques](partition-data.md#physical-partitions).

Azure Cosmos DB utilise un partitionnement basé sur un hachage pour répartir les partitions logiques sur des partitions physiques. Azure Cosmos DB hache la valeur de la clé de partition d’un élément. Le résultat du hachage détermine la partition physique. Azure Cosmos DB alloue ensuite l’espace des hachages de clé de partition uniformément entre les partitions physiques.

Les transactions (dans les procédures stockées ou dans les déclencheurs) ne sont autorisées que pour les éléments d’une même partition logique.

En savoir plus sur la [façon dont Azure Cosmos DB gère les partitions](partition-data.md). (Il n’est pas nécessaire de connaître le fonctionnement interne de la création ou de l’exécution de vos applications mais il n’est décrit ici qu’à titre d’information.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Choix d’une clé de partition

La sélection de votre clé de partition est un choix de conception simple, mais important dans Azure Cosmos DB. Une fois que vous avez sélectionné votre clé de partition, il n’est pas possible de la modifier sur place. Si vous avez besoin de modifier votre clé de partition, vous devez déplacer vos données vers un nouveau conteneur à l’aide de la nouvelle clé de partition de votre choix.

Pour **tous les conteneurs**, votre clé de partition doit :

* Être une propriété qui a une valeur qui ne change pas. Si une propriété est votre clé de partition, vous ne pouvez pas mettre à jour la valeur de cette propriété.
* Avoir une cardinalité élevée. En d’autres termes, la propriété doit avoir une large gamme de valeurs possibles.
* Répartir la consommation d’unités de requête (RU) et le stockage des données uniformément sur toutes les partitions logiques. Cela garantit une consommation des RU et une répartition du stockage uniformes sur les partitions physiques.

Si vous avez besoin de [transactions ACID à plusieurs éléments](database-transactions-optimistic-concurrency.md#multi-item-transactions) dans Azure Cosmos DB, vous devez utiliser [des procédures stockées ou des déclencheurs](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Toutes les procédures stockées et tous les déclencheurs basés sur JavaScript sont limités à une seule partition logique.

## <a name="partition-keys-for-read-heavy-containers"></a>Clés de partition pour les conteneurs à lecture intensive

Pour la plupart des conteneurs, les critères ci-dessus sont tout ce que vous devez prendre en compte lors de la sélection d’une clé de partition. Toutefois, pour les conteneurs à lecture intensive, vous souhaiterez peut-être choisir une clé de partition qui apparaît fréquemment comme filtre dans vos requêtes. Les requêtes peuvent être [efficacement acheminées uniquement vers les partitions physiques concernées](how-to-query-container.md#in-partition-query) en incluant la clé de partition dans le prédicat de filtre.

Si la plupart des demandes de votre charge de travail sont des requêtes et que la plupart de vos requêtes ont un filtre d’égalité sur la même propriété, cette propriété peut être un bon choix de clé de partition. Par exemple, si vous exécutez fréquemment une requête qui filtre sur `UserID`, la sélection de `UserID` comme clé de partition réduit le nombre de [requêtes dans plusieurs partitions](how-to-query-container.md#avoiding-cross-partition-queries).

Toutefois, si votre conteneur est petit, vous n’avez probablement pas assez de partitions physiques pour avoir à vous soucier de l’impact sur les performances des requêtes dans plusieurs partitions. La plupart des petits conteneurs dans Azure Cosmos DB nécessitent uniquement une ou deux partitions physiques.

Si votre conteneur peut atteindre plus de quelques partitions physiques, vous devez vous assurer de choisir une clé de partition qui minimise les requêtes dans plusieurs partitions. Votre conteneur nécessitera plus de quelques partitions physiques lorsque l’une des conditions suivantes est remplie :

* Votre conteneur aura plus de 30 000 RU configurées
* Votre conteneur stockera plus de 100 Go de données

## <a name="using-item-id-as-the-partition-key"></a>Utilisation de l’ID d’élément comme clé de partition

Si votre conteneur a une propriété qui présente une large gamme de valeurs possibles, il s’agit probablement d’un bon choix de clé de partition. L’*ID d’élément* est un exemple possible d’une telle propriété. Pour les petits conteneurs à lecture intensive ou les conteneurs à écriture intensive de toute taille, l’*ID d’élément* est naturellement un bon choix pour la clé de partition.

La propriété système *ID d’élément* existe dans chaque élément de votre conteneur Cosmos. Vous pouvez avoir d’autres propriétés qui représentent un ID logique de votre élément. Dans de nombreux cas, il s’agit également d’excellents choix de clé de partition pour les mêmes raisons que pour l’*ID d’élément*.

L’*ID d’élément* est un excellent choix de clé de partition pour les raisons suivantes :

* Il existe un large éventail de valeurs possibles (un *ID d’élément* unique par élément).
* Étant donné qu’il existe un *ID d’élément* unique par élément, l’*ID d’élément* fait un excellent travail pour équilibrer la consommation des RU et le stockage des données.
* Vous pouvez facilement effectuer des lectures de point efficaces, car vous connaissez toujours la clé de partition d’un élément si vous connaissez son *ID d’élément*.

Voici quelques points à prendre en compte lors de la sélection de l’*ID d’élément* comme clé de partition :

* Si l’*ID d’élément* est la clé de partition, il deviendra un identificateur unique dans tout le conteneur. Vous ne pouvez pas avoir d’éléments dont l’*ID d’élément* est dupliqué.
* Si vous disposez d’un conteneur à lecture intensive qui comporte un grand nombre de [partitions physiques](partition-data.md#physical-partitions), les requêtes seront plus efficaces si elles sont dotées d’un filtre d’égalité avec l’*ID d’élément*.
* Vous ne pouvez pas exécuter de procédures stockées ni de déclencheurs sur plusieurs partitions logiques.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [partitionnement et la mise à l’échelle horizontale dans Azure Cosmos DB](partition-data.md).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
