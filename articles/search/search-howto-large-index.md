---
title: Régénérer un index Recherche Azure ou actualiser du contenu pouvant faire l’objet d’une recherche | Microsoft Docs
description: Ajoutez de nouveaux éléments, mettez à jour des documents ou des éléments existants, ou supprimez des documents obsolètes via une régénération complète ou une indexation incrémentielle partielle visant à actualiser un index Recherche Azure.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659610"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>Comment augmenter l’échelle de l’indexation dans Azure Search

En cas d’augmentation du volume de données ou d’évolution des besoins de traitement, de simples [régénérations ou tâches de réindexation](search-howto-reindex.md) peuvent ne pas suffire. 

Pour répondre à ces besoins croissants, nous vous recommandons de commencer par augmenter [l’échelle et la capacité](search-capacity-planning.md) dans les limites de votre service existant. 

Dans un deuxième temps, si vous pouvez utiliser des [indexeurs](search-indexer-overview.md), ajoutez des mécanismes d’indexation évolutive. Les indexeurs intègrent un planificateur qui vous permet d’exécuter les opérations d’indexation à intervalles réguliers ou d’étendre le traitement au-delà de la fenêtre de 24 heures. En outre, lorsqu’ils sont combinés à des définitions de sources de données, les indexeurs offrent des capacités de parallélisme en partitionnant les données et en planifiant des exécutions en parallèle.

### <a name="scheduled-indexing-for-large-data-sets"></a>Indexation planifiée de jeux de données volumineux

La planification est un mécanisme important pour le traitement de jeux de données volumineux et d’analyses à exécution lente telles que l’analyse d’images dans un pipeline de recherche cognitive. Le traitement de l’indexeur s’exécute dans une fenêtre de 24 heures. Si le traitement n’est pas terminé dans ce délai de 24 heures, les fonctions de planification de l’indexeur peuvent vous être d’une aide précieuse. 

Par conception, l’indexation planifiée démarre à intervalles spécifiques. En général, les tâches sont entièrement exécutées, puis redémarrées au prochain intervalle planifié. Toutefois, si le traitement n’est pas terminé à la fin de l’intervalle, l’indexeur s’arrête (car le délai de traitement a expiré). Au prochain intervalle, le traitement reprend là où il s’était arrêté, le système gardant en mémoire l’endroit où la tâche doit redémarrée. 

En pratique, pour les charges d’index réparties sur plusieurs jours, vous pouvez définir une fenêtre d’exécution de 24 heures pour l’indexeur. Lorsque l’indexation est relancée pour la fenêtre suivante, elle reprend au dernier bon document connu. De cette façon, un indexeur peut s’exécuter sur un backlog de documents pendant plusieurs jours jusqu’à ce que tous les documents non traités soient traités. Pour en savoir plus sur cette approche, consultez l’article [Indexation de jeux de données volumineux](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indexation parallèle

Une alternative consiste à configurer une stratégie d’indexation parallèle. Une telle approche peut s’avérer judicieuse pour les opérations d’indexation inhabituelles nécessitant de nombreuses ressources de calcul, telles que la reconnaissance optique des caractères appliquée à des documents numérisés dans un pipeline de recherche cognitive. Dans un pipeline d’enrichissement de recherche cognitive, l’analyse d’image et le traitement en langage naturel requièrent un temps d’exécution relativement long. L’indexation parallèle sur un service qui ne gère pas de requêtes de manière simultanée peut être une option viable pour traiter un grand volume de contenu à exécution lente. 

Une stratégie de traitement parallèle est définie tel que suit :

+ Placez vos données sources dans plusieurs conteneurs ou dans plusieurs dossiers virtuels au sein du même conteneur. 
+ Mappez chaque mini jeu de données à une [source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source), jumelée à son propre [indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pour la recherche cognitive, référencez le même [ensemble de compétences](https://docs.microsoft.com/rest/api/searchservice/create-skillset) dans chaque définition d’indexeur.
+ Écrivez dans le même index de recherche cible. 
+ Planifiez une exécution simultanée de tous les indexeurs.

> [!Note]
> Le service Recherche Azure ne prend pas en charge l’option consistant à dédier des réplicas ou des partitions à des charges de travail spécifiques. Une indexation simultanée de grande envergure surcharge votre système au détriment des performances des requêtes. Si vous avez un environnement de test, exécutez-y d’abord vos opérations d’indexation parallèle afin d’en identifier les inconvénients.

## <a name="configure-parallel-indexing"></a>Configurer l’indexation parallèle

Pour les indexeurs, la capacité de traitement est plus ou moins basée sur un sous-système d’indexeur pour chaque unité de service utilisée par votre service de recherche. Plusieurs indexeurs peuvent être exécutés en même temps sur les services Recherche Azure configurés sur le niveau De base ou Standard et ayant au moins deux réplicas. 

1. Dans le [portail Azure](https://portal.azure.com), sur la page **Vue d’ensemble** de votre tableau de bord de service de recherche, vérifiez le **niveau tarifaire** pour vous assurer qu’il est compatible avec l’indexation parallèle. Les niveaux De base et Standard fournissent plusieurs réplicas.

2. Sous **Paramètres** > **Échelle**, [augmentez le nombre de réplicas](search-capacity-planning.md) pour le traitement parallèle : un réplica supplémentaire pour chaque charge de travail d’indexeur. Conservez-en un nombre suffisant pour le volume de requêtes existant. Sacrifier les charges de travail de requête au profit de l’indexation n’est pas judicieux.

3. Répartissez les données dans plusieurs conteneurs à un niveau qui est accessible par les indexeurs Recherche Azure. Placez-les par exemple dans plusieurs tables Azure SQL Database, dans différents conteneurs du stockage Blob Azure ou dans plusieurs collections. Définissez un objet de source de données pour chaque table ou conteneur.

4. Créez plusieurs indexeurs et planifiez leur exécution parallèle :

   + Prenons l’exemple d’un service contenant six réplicas. Configurez les six indexeurs, chacun mappé à une source de données contenant un sixième du jeu de données afin de diviser le jeu de données en 6. 

   + Faites pointer chaque indexeur vers le même index. Pour les charges de travail de recherche cognitive, faites pointer chaque indexeur vers le même ensemble de compétences.

   + Dans chaque définition d’indexeur, planifiez le même modèle d’exécution. Par exemple, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` crée une planification démarrant le 15-05-2018 sur tous les indexeurs, avec un intervalle d’exécution de huit heures.

À l’heure planifiée, tous les indexeurs commencent à s’exécuter en chargeant les données, en procédant aux enrichissements (si vous avez configuré un pipeline de recherche cognitive) et en écrivant dans l’index. Le service Recherche Azure ne verrouille pas l’index pour les mises à jour. Il prend en charge les écritures simultanées, en effectuant une nouvelle tentative si une écriture spécifique échoue à la première tentative.

> [!Note]
> Lorsque vous augmentez le nombre de réplicas, envisagez d’augmenter le nombre de partitions si vous anticipez une augmentation significative de la taille de l’index. Les partitions stockent des sections de contenu indexé. Ainsi, plus vous avez de partitions, plus la section de contenu que chaque partition doit stocker est réduite.

## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Indexation dans le portail](search-import-data-portal.md)
+ [Indexeur Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeur Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexeur Stockage Table Azure](search-howto-indexing-azure-tables.md)
+ [Sécurité dans Recherche Azure](search-security-overview.md)