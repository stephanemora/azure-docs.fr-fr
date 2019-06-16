---
title: 'Partitionnement des données dans Azure Cosmos DB : API Gremlin'
description: Découvrez comment vous pouvez utiliser un graphique partitionné dans Azure Cosmos DB. Cet article décrit également la configuration requise et les meilleures pratiques relatives aux graphiques partitionnés.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f1e486a302b440d819e15ef86f8d76ea5e50d201
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60888405"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Utilisation d’un graphique partitionné dans Azure Cosmos DB

Une des fonctionnalités clés de l’API Gremlin dans Azure Cosmos DB est la capacité de gérer des graphes à grande échelle avec la mise à l’échelle horizontale. La mise à l’échelle horizontale est exécutée via les [fonctionnalités de partitionnement d’Azure Cosmos DB](partition-data.md). Les conteneurs peuvent évoluer indépendamment en termes de débit et de stockage. Vous pouvez créer des conteneurs dans Azure Cosmos DB qui peuvent être automatiquement mis à l’échelle pour stocker des données graphiques. Les données sont automatiquement réparties en fonction de la **clé de partition** spécifiée.

Les modalités de partitionnement des bases de données de graphiques seront décrites dans ce document, ainsi que ses implications pour les sommets (ou nœuds) et les arêtes.

## <a name="requirements-for-partitioned-graph"></a>Conditions requises pour le graphique partitionné

Vous devez comprendre les détails suivants pour créer un conteneur graphique partitionné :

- **Le partitionnement est requis** si le conteneur doit stocker plus de 10 Go ou si vous souhaitez allouer plus de 10 000 unités de requête par seconde (RU).

- **Les sommets et arêtes sont stockés sous forme de documents JSON**.

- **Les sommets nécessitent une clé de partition**. Cette clé détermine dans quelle partition le sommet est stocké par un algorithme de hachage. Le nom de cette clé de partition est un mot unique ne contenant ni espace ni caractère spécial. La clé de partition est définie lors de la création d’un nouveau conteneur. Elle se présente au format : `/partitioning-key-name`.

- **Les arêtes seront stockées avec leur sommet source**. En d’autres termes, la clé de partition de chaque sommet définit là où elles sont stockées avec ses arêtes sortantes. Cela permet d’éviter les requêtes entre partitions lors de l’utilisation de la cardinalité `out()` dans les requêtes de graphique.

- **Les requêtes de graphique doivent spécifier une clé de partition**. Pour tirer pleinement parti du partitionnement horizontal dans Azure Cosmos DB, la clé de partition doit être spécifiée lorsqu’un sommet unique est sélectionné, dans la mesure du possible. Les requêtes suivantes permettent de sélectionner un ou plusieurs sommets dans un graphique partitionné :

    - `/id` et `/label` ne sont pas pris en charge en tant que clés de partition d’un conteneur dans l’API Gremlin.


    - Sélection d’un sommet par ID, puis **à l’aide de l’étape `.has()` pour spécifier la propriété de clé de partition** : 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Sélection d’un sommet en **spécifiant un tuple comprenant la valeur de la clé de partition et l’ID** : 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Spécification d’un **tableau de tuples de valeurs de clé de partition et d’ID** :
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Sélection d’un ensemble de sommets et **spécification d’une liste de valeurs de clé de partition** : 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Meilleures pratiques lors de l’utilisation d’un graphique partitionné

Pour garantir de hautes performances et une bonne évolutivité lors de l’utilisation de graphiques partitionnés avec des conteneurs illimités, utilisez les instructions suivantes :

- **Spécifiez toujours la valeur de la clé de partition lors de l’interrogation d’un sommet**. L’obtention des sommets à partir d’une partition connue permet d’optimiser les performances.

- **Utilisez la direction sortante lorsque vous interrogez des arêtes dans la mesure du possible**. Comme nous l’avons déjà vu, les arêtes sont stockées avec leurs sommets sources dans la direction sortante. En d’autres termes, le risque de recourir à des requêtes entre partitions est réduit lorsque les données et les requêtes sont conçues dans cet esprit.

- **Choisissez une clé de partition qui répartira les données de manière uniforme entre les partitions**. Cette décision dépend fortement du modèle de données de la solution. Pour en savoir plus sur la création d’une clé de partition appropriée, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](partition-data.md).

- **Optimisez les requêtes pour obtenir des données dans les limites d’une partition**. Une stratégie de partitionnement optimale doit être en phase avec les modèles de requête. Les requêtes qui obtiennent des données à partir d’une seule partition offrent les meilleures performances.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite passer aux articles suivants :

* Pour en savoir plus, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](partition-data.md).
* Pour en savoir plus, consultez [Prise en charge de Gremlin dans l’API Gremlin](gremlin-support.md).
* Pour en savoir plus, consultez [Présentation de l’API Gremlin](graph-introduction.md).
