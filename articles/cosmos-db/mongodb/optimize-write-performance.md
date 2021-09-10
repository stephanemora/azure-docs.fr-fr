---
title: Optimiser les performances d’écriture dans l’API Azure Cosmos DB pour MongoDB
description: Cet article explique comment optimiser les performances d’écriture dans l’API Azure Cosmos DB pour MongoDB pour obtenir le meilleur débit possible pour le coût le plus bas.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 2e04953e766c76275079731751cb006fe46712e7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039550"
---
# <a name="optimize-write-performance-in-azure-cosmos-db-api-for-mongodb"></a>Optimiser les performances d’écriture dans l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

L’optimisation des performances en écriture vous aide à tirer le meilleur parti de l’API Azure Cosmos DB pour la mise à l’échelle illimitée de MongoDB. Contrairement à d’autres services MongoDB gérés, l’API pour MongoDB partitionne automatiquement et de manière transparente vos collections pour vous (lors de l’utilisation de collections partitionnées) pour une mise à l’échelle infinie. 

La façon dont vous écrivez des données doit prendre cela en compte par la parallélisation et en répartissant les données entre les partitions pour tirer le meilleur parti de vos bases de données et collections. Cet article explique les meilleures pratiques pour optimiser les performances d’écriture.

## <a name="spread-the-load-across-your-shards"></a>Répartissez la charge entre vos partitions
Lors de l’écriture de données dans une API partitionnée pour une collection MongoDB, vos données sont fractionnées (partitionnées) en tranches minuscules et elles sont écrites dans chaque partition en fonction de la valeur de votre champ de clé de partition. Vous pouvez considérer chaque secteur comme une petite partie d’une machine virtuelle qui stocke uniquement les documents contenant une valeur de clé de partition unique. 

Si votre application écrit une grande quantité de données dans une seule partition, cela ne sera pas efficace, car l’application dépasserait le débit d’une seule partition au lieu de répartir la charge sur l’ensemble de vos partitions. Votre charge d’écriture sera uniformément répartie sur votre collection en écrivant en parallèle avec de nombreux documents avec des valeurs de clé de partition uniques.

Il peut s’agir, par exemple, d’une application de catalogue de produits partitionnée dans le champ catégorie. Au lieu d’écrire dans une catégorie (partition) à la fois, il est préférable d’écrire dans toutes les catégories simultanément pour atteindre le débit d’écriture maximal. 

## <a name="reduce-the-number-of-indexes"></a>Réduire le nombre d’index
L'[indexation](../mongodb-indexing.md) est une fonctionnalité intéressante qui réduit considérablement le temps nécessaire à l’interrogation de vos données. Pour une expérience de requête plus flexible, l’API pour MongoDB active un index de caractères génériques sur vos données par défaut pour faire des requêtes sur tous les champs éblouissants rapidement. Toutefois, tous les index, qui incluent des index génériques, introduisent une charge supplémentaire lors de l’écriture de données, car les écritures modifient la collection et les index. 

Le fait de réduire le nombre d’index aux index dont vous avez besoin pour prendre en charge vos requêtes rend vos écritures plus rapides et moins coûteuses. En règle générale, nous vous recommandons de :

* Tout champ sur lequel vous filtrez doit avoir un index de champ unique correspondant. Cette option permet également de filtrer plusieurs champs.
* Tout groupe de champs sur lequel vous triez doit avoir un index composite pour ce groupe. 

## <a name="set-ordered-to-false-in-the-mongodb-drivers"></a>Défini sur false dans les pilotes MongoDB
Par défaut, les pilotes MongoDB attribuent à l’option ordered la valeur true lors de l’écriture de données, qui écrit chaque document dans l’ordre un par un. Cette option réduit les performances d’écriture puisque chaque demande d’écriture doit attendre la fin de l’opération précédente. Lors de l’écriture de données, affectez la valeur false à cette option pour améliorer les performances. 

```JavaScript
db.collection.insertMany(
   [ <doc1> , <doc2>, ... ],
   {
      ordered: false
   }
)
```

## <a name="tune-for-the-optimal-batch-size-and-thread-count"></a>Réglage de la taille de lot optimale et du nombre de threads
La parallélisation des opérations d’écriture sur de nombreux threads/processus est essentielle à la mise à l’échelle des écritures. L’API pour MongoDB accepte les écritures par lots allant jusqu’à 1 000 documents pour chaque processus/thread. 

Si vous écrivez plus de 1 000 documents à la fois par processus/thread, les fonctions clientes telles que `insertMany()` doivent être limitées à environ 1 000 documents. Dans le cas contraire, le client attendra la validation de chaque lot avant de passer au traitement suivant. Dans certains cas, le fractionnement des lots avec moins ou moins de 1 000 documents est plus rapide.



## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [l’indexation dans l’API pour MongoDB](../mongodb-indexing.md).
* En savoir plus sur le [partitionnement dans Azure Cosmos DB](../partitioning-overview.md).
* En savoir plus sur la [résolution des problèmes fréquents](error-codes-solutions.md).
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
    * Si vous ne connaissez que le nombre de vCore et de serveurs présents dans votre cluster de bases de données existant, lisez l’article sur l’[estimation des unités de requête à l’aide de vCore ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
    * Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)
