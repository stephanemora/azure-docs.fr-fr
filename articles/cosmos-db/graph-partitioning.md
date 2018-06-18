---
title: Partitionnement de l’API Graph | Microsoft Docs
description: Découvrez comment vous pouvez utiliser un graphique partitionné dans Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: e2658c01ea3a31efdcf2fef3bb9b56df536de295
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795650"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Utilisation d’un graphique partitionné dans Azure Cosmos DB

Une des fonctionnalités clés de l’API Graph dans Azure Cosmos DB tient à la capacité de gérer des graphiques à grande échelle par l’évolutivité horizontale. Ce processus est possible grâce aux [fonctionnalités de partitionnement d’Azure Cosmos DB](partition-data.md#how-does-partitioning-work), qui utilisent des collections (également appelées conteneurs) capables d’évoluer de manière indépendante en termes de débit et de stockage. Azure Cosmos DB prend en charge les types de conteneurs suivants sur toutes les API :

- **Collection fixe** : ces collections peuvent stocker une base de données de graphiques dont la taille peut atteindre 10 Go et lui allouer jusqu’à 10 000 unités de requête par seconde. Pour créer une collection fixe, il est inutile de spécifier une propriété de clé de partition dans les données.

- **Collection illimitée** : ces collections sont dimensionnées automatiquement pour stocker un graphique au-delà de la limite de 10 Go grâce au partitionnement horizontal. Chaque partition stockera 10 Go et les données seront automatiquement réparties en fonction de la **clé de partition spécifiée**, qui sera un paramètre obligatoire lors de l’utilisation d’une collection illimitée. Ce type de conteneur peut stocker un volume de données quasi illimité et traiter 100 000 unités de requête par seconde ou plus [en contactant le support](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

Les modalités de partitionnement des bases de données de graphiques seront décrites dans ce document, ainsi que ses implications pour les sommets (ou nœuds) et les arêtes.

## <a name="requirements-for-partitioned-graph"></a>Conditions requises pour le graphique partitionné

Vous devez comprendre les détails suivants pour créer un conteneur graphique partitionné :
- **La configuration du partitionnement sera nécessaire** si la collection est censée dépasser les 10 Go ou si l’allocation de plus de 10 000 unités de requête par seconde (RU/s) sera nécessaire.
- **Les sommets et arêtes sont stockés sous la forme de documents JSON** dans le serveur principal d’un conteneur de l’API Graph d’Azure Cosmos DB.
- **Les sommets nécessitent une clé de partition**. Cette clé détermine dans quelle partition le sommet est stocké par un algorithme de hachage. Le nom de cette clé de partition est un mot unique sans espace ni caractère spécial, et il est défini lors de la création d’une collection au format `/partitioning-key-name` sur le portail.
- **Les arêtes seront stockées avec leur sommet source**. En d’autres termes, la clé de partition de chaque sommet définira là où elles seront stockées avec ses arêtes sortantes. Cela permet d’éviter les requêtes entre partitions lors de l’utilisation de la cardinalité `out()` dans les requêtes de graphique.
- **Les requêtes de graphique doivent spécifier une clé de partition**. Pour tirer pleinement parti du partitionnement horizontal dans Azure Cosmos DB, la clé de partition doit être spécifiée lorsqu’un sommet unique est sélectionné, dans la mesure du possible. Les requêtes suivantes permettent de sélectionner un ou plusieurs sommets dans un graphique partitionné :

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

Suivez les consignes ci-dessous pour garantir une évolutivité et des performances optimales lors de l’utilisation de graphiques partitionnés dans des collections illimitées :
- **Spécifiez toujours la valeur de la clé de partition lors de l’interrogation d’un sommet**. L’obtention d’un sommet à partir d’une partition connue est le moyen le plus efficace en termes de performances.
- **Utilisez la direction sortante lorsque vous interrogez des arêtes dans la mesure du possible**. Comme nous l’avons déjà vu, les arêtes sont stockées avec leurs sommets sources dans la direction sortante. En d’autres termes, le risque de recourir à des requêtes entre partitions est minimisé lorsque les données et les requêtes sont conçues dans cet esprit.
- **Choisissez une clé de partition qui répartira les données de manière uniforme entre les partitions**. Cette décision dépend fortement du modèle de données de la solution. Pour en savoir plus sur la création d’une clé de partition appropriée, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](partition-data.md).
- **Optimisez les requêtes pour obtenir des données dans les limites d’une partition, lorsque cela est possible**. Une stratégie de partitionnement optimale doit être en phase avec les modèles de requête. Les requêtes qui obtiennent des données à partir d’une seule partition offrent les meilleures performances.

## <a name="next-steps"></a>Étapes suivantes
Cet article fournit une vue d’ensemble des concepts et des meilleures pratiques du partitionnement avec l’API Graph d’Azure Cosmos DB. 

* Pour en savoir plus, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](partition-data.md).
* Pour en savoir plus, consultez [Prise en charge des graphes Azure Cosmos DB Gremlin](gremlin-support.md).
* Pour en savoir plus, consultez [Présentation d’Azure Cosmos DB : API Graph](graph-introduction.md).
