---
title: Partitionnement dans Azure Cosmos DB
description: Vue d’ensemble du partitionnement dans Azure Cosmos DB.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: a8db510bea57fa3d6ee873571e586bcef7508b26
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961633"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB

Azure Cosmos DB utilise le partitionnement à l’échelle des conteneurs individuels dans une base de données en fonction des besoins de performances de votre application. Dans le partitionnement, les éléments dans un conteneur sont divisées en sous-ensembles distincts appelés *partitions logiques*. Partitions logiques sont formées en fonction de la valeur d’un *clé de partition* qui est associé à chaque élément dans un conteneur. Tous les éléments dans une partition logique ont la même valeur de clé de partition.

Par exemple, un conteneur contient des documents. Chaque document possède une valeur unique pour le `UserID` propriété. Si `UserID` sert de la partition de clé pour les éléments dans le conteneur et il y a 1 000 `UserID` valeurs, 1 000 partitions logiques sont créées pour le conteneur.

En plus d’une clé de partition qui détermine la partition logique de l’élément, chaque élément dans un conteneur a un *ID de l’élément* (unique au sein d’une partition logique). Combinaison de la clé de partition et l’ID d’élément crée l’élément *index*, qui identifie de façon unique l’élément.

[Choix d’une clé de partition](partitioning-overview.md#choose-partitionkey) est une décision importante qui affecte les performances de votre application.

## <a name="managing-logical-partitions"></a>La gestion des partitions logiques

Azure Cosmos DB en toute transparence et automatiquement gère le positionnement des partitions logiques sur les partitions physiques (votre infrastructure de serveur) pour traiter efficacement les besoins d’évolutivité et les performances du conteneur. Comme les exigences de débit et le stockage d’une application augmentent, Azure Cosmos DB déplace des partitions logiques automatiquement répartir la charge sur un plus grand nombre de serveurs. 

Azure Cosmos DB utilise le partitionnement basé sur le hachage pour répartir les partitions logiques sur les partitions physiques. Azure Cosmos DB hache la valeur de clé de partition d’un élément. Le résultat haché détermine la partition physique. Ensuite, Azure Cosmos DB alloue l’espace de clé de partition de hachages de clé uniformément entre les partitions physiques.

Les requêtes qui accèdent aux données d'une même partition sont plus économiques que les requêtes qui accèdent à plusieurs partitions. Transactions (dans les procédures stockées ou déclencheurs) sont autorisées uniquement sur les éléments dans une partition logique unique.

Pour en savoir plus sur la façon dont Azure Cosmos DB gère les partitions, consultez [partitions logiques](partition-data.md). (Il n’est pas nécessaire de comprendre les détails suivants pour créer ou exécuter vos applications.)

## <a id="choose-partitionkey"></a>Choix d’une clé de partition

Lorsque vous choisissez une clé de partition, prenez en compte les informations suivantes :

* Une partition logique unique a une limite supérieure de 10 Go de stockage.  

* Les conteneurs partitionnés ont un débit minimum de 400 unités de requête par seconde (RU/s). Requêtes sur la même clé de partition ne peut pas dépasser le débit est alloué à une partition. Si les requêtes dépassent le débit alloué, les demandes sont limitées. Il est donc important de choisir une clé de partition qui ne crée pas de « zones réactives » au sein de votre application.

* Choisir une clé de partition qui répartit la charge de travail uniformément entre toutes les partitions et uniforme au fil du temps. Votre choix de clé de partition doit équilibrer la nécessité pour les requêtes de partition efficace et les transactions par rapport à l’objectif à atteindre une évolutivité de la distribution des éléments sur plusieurs partitions.

* Choisissez une clé de partition dotée d'un large éventail de valeurs et de modèles d'accès répartis uniformément sur les partitions logiques. Cela permet de répartir les données et l’activité dans votre conteneur sur l’ensemble des partitions logiques, afin que les ressources de stockage de données et le débit peuvent être répartis sur les partitions logiques.

* Candidats pour les clés de partition peuvent inclure des propriétés qui apparaissent fréquemment en tant que filtre dans vos requêtes. Les requêtes peuvent être efficacement acheminées en incluant la clé de partition dans le prédicat de filtre.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [partitions](partition-data.md).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
