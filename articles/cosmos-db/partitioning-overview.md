---
title: Partitionnement dans Azure Cosmos DB
description: Vue d’ensemble du partitionnement dans Azure Cosmos DB.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: e88be8e7b94566ff94dd94a8679f8ade9d54c0b6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784517"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB

Azure Cosmos DB utilise le partitionnement à l’échelle des conteneurs individuels dans une base de données en fonction des besoins de performances de votre application. Dans le partitionnement, les éléments dans un conteneur sont divisées en sous-ensembles distincts appelés *partitions logiques*. Partitions logiques sont formées en fonction de la valeur d’un *clé de partition* qui est associé à chaque élément dans un conteneur. Tous les éléments dans une partition logique ont la même valeur de clé de partition.

Par exemple, un conteneur contient des éléments. Chaque élément a une valeur unique pour le `UserID` propriété. Si `UserID` sert de la partition de clé pour les éléments dans le conteneur et il y a 1 000 `UserID` valeurs, 1 000 partitions logiques sont créées pour le conteneur.

En plus d’une clé de partition qui détermine la partition logique de l’élément, chaque élément dans un conteneur a un *ID de l’élément* (unique au sein d’une partition logique). Combinaison de la clé de partition et l’ID d’élément crée l’élément *index*, qui identifie de façon unique l’élément.

[Choix d’une clé de partition](partitioning-overview.md#choose-partitionkey) est une décision importante qui affecte les performances de votre application.

## <a name="managing-logical-partitions"></a>La gestion des partitions logiques

Azure Cosmos DB en toute transparence et automatiquement gère le positionnement des partitions logiques sur des partitions physiques pour traiter efficacement les besoins d’évolutivité et les performances du conteneur. Comme les exigences de débit et le stockage d’une application augmentent, Azure Cosmos DB déplace des partitions logiques automatiquement répartir la charge sur un plus grand nombre de serveurs. 

Azure Cosmos DB utilise le partitionnement basé sur le hachage pour répartir les partitions logiques sur les partitions physiques. Azure Cosmos DB hache la valeur de clé de partition d’un élément. Le résultat haché détermine la partition physique. Ensuite, Azure Cosmos DB alloue l’espace de clé de partition de hachages de clé uniformément entre les partitions physiques.

Les requêtes qui accèdent aux données au sein d’une partition logique unique sont plus économiques que les requêtes qui accèdent à plusieurs partitions. Transactions (dans les procédures stockées ou déclencheurs) sont autorisées uniquement sur les éléments dans une partition logique unique.

Pour en savoir plus sur la façon dont Azure Cosmos DB gère les partitions, consultez [partitions logiques](partition-data.md). (Il n’est pas nécessaire de comprendre les détails internes pour générer ou exécuter vos applications, mais ajoutées ici pour un lecteur curieux.)

## <a id="choose-partitionkey"></a>Choix d’une clé de partition

Voici un bons conseils pour le choix d’une clé de partition :

* Une partition logique unique a une limite supérieure de 10 Go de stockage.  

* Les conteneurs Cosmos Azure ont un débit minimum de 400 unités de requête par seconde (RU/s). Requêtes sur la même clé de partition ne peut pas dépasser le débit est alloué à une partition. Si les requêtes dépassent le débit alloué, les demandes sont limitées. Il est donc important de choisir une clé de partition qui ne crée pas de « zones réactives » au sein de votre application.

* Choisissez une clé de partition dotée d'un large éventail de valeurs et de modèles d'accès répartis uniformément sur les partitions logiques. Cela permet de répartir les données et l’activité dans votre conteneur sur l’ensemble des partitions logiques, afin que les ressources de stockage de données et le débit peuvent être répartis sur les partitions logiques.

* Choisir une clé de partition qui répartit la charge de travail uniformément entre toutes les partitions et uniforme au fil du temps. Votre choix de clé de partition doit équilibrer la nécessité pour les requêtes de partition efficace et les transactions par rapport à l’objectif à atteindre une évolutivité de la distribution des éléments sur plusieurs partitions.

* Candidats pour les clés de partition peuvent inclure des propriétés qui apparaissent fréquemment en tant que filtre dans vos requêtes. Les requêtes peuvent être efficacement acheminées en incluant la clé de partition dans le prédicat de filtre.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB](partition-data.md).
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).
