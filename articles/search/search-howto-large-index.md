---
title: Indexer des grands jeux de données avec des indexeurs intégrés
titleSuffix: Azure Cognitive Search
description: Stratégies pour l’indexation de grands volumes de données ou pour l’indexation gourmande en ressources via le mode de traitement par lots, la gestion des ressources, et des techniques d’indexation planifiée, parallèle et distribuée.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190965"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Comment indexer des grands ensembles de données dans la Recherche cognitive Azure

Au fur et à mesure que les volumes de données augmentent ou que les besoins en traitement évoluent, vous trouverez peut-être que les stratégies d’indexation simples ou par défaut ne sont plus adaptées. Pour la Recherche cognitive Azure, il existe plusieurs approches pour prendre en charge les grands jeux de données, allant de la façon dont une demande de chargement de données est structurée à l’utilisation d’un indexeur spécifique à la source pour les charges de travail planifiées et distribuées.

Les mêmes techniques s’appliquent également aux processus à exécution longue. En particulier, les étapes décrites dans [Indexation parallèle](#parallel-indexing) sont utiles pour l’indexation gourmande en ressources, comme l’analyse d’images ou le traitement en langage naturel dans un [pipeline d’enrichissement de l’IA](cognitive-search-concept-intro.md).

Les sections suivantes décrivent trois techniques d’indexation de grandes quantités de données.

## <a name="option-1-pass-multiple-documents"></a>Option 1 : Transférer plusieurs documents

Un des mécanismes les plus simples pour l’indexation d’un grand jeu de données consiste à soumettre plusieurs documents ou enregistrements dans une même demande. Tant que la charge utile entière est inférieure à 16 Mo, une demande peut gérer jusqu’à 1 000 documents dans une opération de chargement en bloc. Ces limites s’appliquent que vous utilisiez l’[API REST d’ajout de documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou la [méthode Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) du SDK .NET. Pour l’une ou l’autre des API, vous devez empaqueter 1 000 documents dans le corps de chaque requête.

L’indexation par lot est implémentée pour les demandes individuelles avec REST ou .NET, ou via des indexeurs. Quelques indexeurs fonctionnent avec des limites différentes. Plus précisément, l’indexation des objets blob Azure définit la taille des lots à 10 documents en fonction de la taille moyenne des documents la plus élevée. Pour les indexeurs basés sur l’[API REST de création d’un indexeur](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer), vous pouvez définir l’argument `BatchSize` pour personnaliser ce paramètre de façon à le faire mieux correspondre aux caractéristiques de vos données. 

> [!NOTE]
> Pour réduire la taille du document, évitez d’ajouter des données non interrogeables à un index. Les images et autres données binaires ne peuvent pas faire l’objet de recherches directes et ne doivent pas être stockées dans l’index. Pour intégrer des données non interrogeables dans les résultats de la recherche, vous devez définir un champ sans possibilité de recherche qui stocke une référence d’URL vers la ressource.

## <a name="option-2-add-resources"></a>Option n°2 : Ajout de ressources

Les services qui sont provisionnés à un des [niveaux tarifaires standard](search-sku-tier.md) ont souvent des capacités sous-utilisées pour le stockage et les charges de travail (requêtes ou indexation), ce qui fait de l’[augmentation du nombre de partitions et de réplicas](search-capacity-planning.md) une solution évidente pour prendre en charge les grands jeux de données. Pour optimiser les résultats, vous avez besoin des deux ressources : les partitions pour le stockage et les réplicas pour le travail d’ingestion des données.

L’augmentation des réplicas et des partitions est un événement facturable qui augmente les coûts, mais à moins d’indexer en continu sous une charge maximale, vous pouvez procéder à une mise à l’échelle pour la durée du processus d’indexation, puis baisser de nouveau les niveaux de ressources une fois l’indexation terminée.

## <a name="option-3-use-indexers"></a>Option 3 : Utiliser des indexeurs

Les [indexeurs](search-indexer-overview.md) sont utilisés pour analyser le contenu pouvant être recherché dans les sources de données Azure prises en charge. Bien qu’ils ne soient pas spécifiquement destinés à l’indexation à grande échelle, plusieurs fonctionnalités des indexeurs sont particulièrement utiles pour prendre en charge les grands jeux de données :

+ Les planificateurs vous permettent de diviser l’indexation pour l’effectuer à intervalles réguliers : vous pouvez ainsi la répartir dans le temps.
+ L’indexation planifiée peut reprendre au dernier point d’arrêt connu. Si une source de données n’est pas entièrement parcourue dans une fenêtre de 24 heures, l’indexeur reprend l’indexation au deuxième jour, là où elle s’était arrêtée.
+ Le partitionnement des données en sources de données individuelles plus petites permet le traitement parallèle. Vous pouvez séparer les données sources en composants plus petits, par exemple en plusieurs conteneurs dans un stockage Blob Azure, puis créer plusieurs [objets de source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source) correspondants dans la Recherche cognitive Azure, qui peuvent être indexés en parallèle.

> [!NOTE]
> Les indexeurs sont spécifiques à une source de données : l’utilisation d’une approche par indexeur est donc viable seulement pour des sources de données sélectionnées sur Azure : [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Stockage Blob](search-howto-indexing-azure-blob-storage.md), [Stockage Table](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Indexation planifiée

La planification des indexeurs est un mécanisme important pour le traitement des grands jeux de données et pour les processus à exécution longue, comme l’analyse d’images dans un pipeline de recherche cognitive. Le traitement de l’indexeur s’exécute dans une fenêtre de 24 heures. Si le traitement n’est pas terminé dans ce délai de 24 heures, les fonctions de planification de l’indexeur peuvent vous être d’une aide précieuse. 

Par conception, l’indexation planifiée démarre à intervalles spécifiques. En général, les tâches sont entièrement exécutées, puis redémarrées au prochain intervalle planifié. Toutefois, si le traitement n’est pas terminé à la fin de l’intervalle, l’indexeur s’arrête (car le délai de traitement a expiré). Au prochain intervalle, le traitement reprend là où il s’était arrêté, le système gardant en mémoire l’endroit où la tâche doit redémarrée. 

En pratique, pour les charges d’index réparties sur plusieurs jours, vous pouvez définir une fenêtre d’exécution de 24 heures pour l’indexeur. Quand l’indexation reprend pour le cycle suivant de 24 heures, elle redémarre au dernier document valide connu. De cette façon, un indexeur peut s’exécuter sur un backlog de documents pendant plusieurs jours jusqu’à ce que tous les documents non traités soient traités. Pour plus d’informations sur cette approche, consultez [Indexation de grands jeux de données dans Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Pour plus d’informations sur la définition de planifications en général, voir [API REST de création d’indexeur](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) ou [Comment planifier des indexeurs pour la Recherche cognitive Azure](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexation parallèle

Une stratégie d’indexation parallèle est basée sur l’indexation de plusieurs sources de données à l’unisson, où chaque définition de source de données spécifie un sous-ensemble des données. 

Pour des besoins ponctuels d’indexation gourmande en ressources, comme la reconnaissance de caractères sur des documents numérisés dans un pipeline de recherche cognitive, l’analyse d’images ou un traitement en langage naturel, une stratégie d’indexation parallèle est souvent la bonne approche pour effectuer un processus à exécution longue dans le temps le plus court. Si vous pouvez éliminer ou réduire les demandes de requêtes, l’indexation parallèle sur un service qui ne gère pas simultanément des requêtes peut être un choix de stratégie valide pour traiter un grand volume de contenu dont le traitement est lent. 

Un traitement parallèle se déroule comme suit :

+ Répartissez vos données sources entre plusieurs conteneurs ou plusieurs dossiers virtuels au sein du même conteneur. 
+ Mappez chaque petit jeu de données à sa propre [source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source), appairée à son propre [indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Pour la recherche cognitive, référencez le même [ensemble de compétences](https://docs.microsoft.com/rest/api/searchservice/create-skillset) dans chaque définition d’indexeur.
+ Écrivez dans le même index de recherche cible. 
+ Planifiez une exécution simultanée de tous les indexeurs.

> [!NOTE]
> Dans Recherche cognitive Azure, vous ne pouvez pas assigner des réplicas ou des partitions individuels à l’indexation ou au traitement des requêtes. Le système détermine la façon dont les ressources sont utilisées. Pour comprendre l’impact sur les performances des requêtes, vous pouvez tenter une indexation parallèle dans un environnement de test avant de la déployer en production.  

### <a name="how-to-configure-parallel-indexing"></a>Comment configurer l’indexation parallèle

Pour les indexeurs, la capacité de traitement est plus ou moins basée sur un sous-système d’indexeur pour chaque unité de service utilisée par votre service de recherche. Plusieurs indexeurs peuvent être exécutés en même temps sur les services Recherche cognitive Azure configurés sur le niveau De base ou Standard et ayant au moins deux réplicas. 

1. Dans le [portail Azure](https://portal.azure.com), sur la page **Vue d’ensemble** de votre tableau de bord de service de recherche, vérifiez le **niveau tarifaire** pour vous assurer qu’il est compatible avec l’indexation parallèle. Les niveaux De base et Standard fournissent plusieurs réplicas.

2. Sous **Paramètres** > **Échelle**, [augmentez le nombre de réplicas](search-capacity-planning.md) pour le traitement parallèle : un réplica supplémentaire pour chaque charge de travail d’indexeur. Conservez-en un nombre suffisant pour le volume de requêtes existant. Sacrifier les charges de travail de requête au profit de l’indexation n’est pas judicieux.

3. Répartissez les données dans plusieurs conteneurs à un niveau qui est accessible par les indexeurs Recherche cognitive Azure. Placez-les par exemple dans plusieurs tables dans Azure SQL Database, dans différents conteneurs du stockage Blob Azure ou dans plusieurs collections. Définissez un objet de source de données pour chaque table ou conteneur.

4. Créez plusieurs indexeurs et planifiez leur exécution parallèle :

   + Prenons l’exemple d’un service contenant six réplicas. Configurez les six indexeurs, chacun mappé à une source de données contenant un sixième du jeu de données afin de diviser le jeu de données en 6. 

   + Faites pointer chaque indexeur vers le même index. Pour les charges de travail de recherche cognitive, faites pointer chaque indexeur vers le même ensemble de compétences.

   + Dans chaque définition d’indexeur, planifiez le même modèle d’exécution. Par exemple, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` crée une planification démarrant le 15-05-2018 sur tous les indexeurs, avec un intervalle d’exécution de huit heures.

À l’heure planifiée, tous les indexeurs commencent à s’exécuter en chargeant les données, en procédant aux enrichissements (si vous avez configuré un pipeline de recherche cognitive) et en écrivant dans l’index. Le service Recherche cognitive Azure ne verrouille pas l’index pour les mises à jour. Il prend en charge les écritures simultanées, en effectuant une nouvelle tentative si une écriture spécifique échoue à la première tentative.

> [!Note]
> Lorsque vous augmentez le nombre de réplicas, envisagez d’augmenter le nombre de partitions si vous anticipez une augmentation significative de la taille de l’index. Les partitions stockent des sections de contenu indexé. Ainsi, plus vous avez de partitions, plus la section de contenu que chaque partition doit stocker est réduite.

## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Indexation dans le portail](search-import-data-portal.md)
+ [Indexeur Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexeur Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexeur Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexeur Stockage Table Azure](search-howto-indexing-azure-tables.md)
+ [Sécurité dans Recherche cognitive Azure](search-security-overview.md)
