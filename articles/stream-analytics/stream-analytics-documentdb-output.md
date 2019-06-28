---
title: Sortie Azure Stream Analytics dans Cosmos DB
description: Cet article décrit comment utiliser Azure Stream Analytics pour enregistrer dans Azure Cosmos DB la sortie JSON à des fins d’archivage des données et d’exécution de requêtes à faible latence sur des données JSON non structurées.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402306"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Sortie Azure Stream Analytics dans Azure Cosmos DB  
Stream Analytics peut cibler [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) pour la sortie JSON, ce qui permet d’archiver des données et d’exécuter des requêtes à faible latence sur des données JSON non structurées. Ce document traite certaines meilleures pratiques recommandées pour l’implémentation de cette configuration.

Pour ceux qui ne connaissent pas Cosmos DB, commencez par suivre le [parcours d’apprentissage de Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/). 

> [!Note]
> Pour le moment, Azure Stream Analytics prend uniquement en charge la connexion à Azure Cosmos DB à l’aide de **l’API SQL**.
> Les autres API Azure Cosmos DB ne sont pas encore prises en charge. Si vous pointez Azure Stream Analytics vers les comptes Azure Cosmos DB créés avec d'autres API, les données risquent de ne pas être correctement stockées. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Principes de base de Cosmos DB en tant que cible de sortie
La sortie Azure Cosmos DB dans Stream Analytics vous permet d’écrire les résultats du traitement de votre flux en tant que sortie JSON dans vos collections Cosmos DB. Stream Analytics ne crée pas de collections dans votre base de données, mais vous oblige à les créer dès le départ. Ainsi, vous contrôlez vous-même les coûts de facturation des collections Cosmos DB et pouvez optimiser les performances, la cohérence et la capacité de vos collections directement à l’aide des [API Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Vous devez ajouter 0.0.0.0 à la liste des adresses IP autorisées de votre pare-feu Azure Cosmos DB.

Vous trouverez ci-dessous le détail de certaines options de collection Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Régler la cohérence, la disponibilité et la latence
Pour respecter les exigences de votre application, Azure Cosmos DB vous permet d’ajuster la base de données et les collections, et de faire les meilleurs compromis entre cohérence, disponibilité et latence. Selon les niveaux de cohérence de lecture qui s’appliquent à votre scénario compte tenu de la latence de lecture et d’écriture, vous pouvez choisir un niveau de cohérence sur votre compte de base de données. Par défaut, Azure Cosmos DB permet également une indexation synchrone sur chaque opération CRUD exécutée sur votre collection. Cette option est également utile pour contrôler les performances de lecture/écriture dans Azure Cosmos DB. Pour plus d’informations, consultez l’article [Modifier le niveau de cohérence des bases de données et des requêtes](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upserts à partir de Stream Analytics
L’intégration de Stream Analytics à Azure Cosmos DB vous permet d’insérer ou mettre à jour des enregistrements dans votre collection à partir d’une colonne d’identification de document donnée. C’est ce que l’on appelle un *Upsert*.

Stream Analytics utilise une approche optimiste de type upsert, c’est-à-dire que les mises à jour ne sont effectuées qu’en cas d’échec de l’insertion lié à un conflit d’ID de document. Avec le niveau de compatibilité 1.0, cette mise à jour est effectuée sous forme de correctif, de sorte que le document peut être partiellement mis à jour. Autrement dit, l’ajout de nouvelles propriétés ou le remplacement d’une propriété existante est effectué de façon incrémentielle. Toutefois, les modifications apportées aux valeurs des propriétés du tableau dans votre document JSON ont pour effet d’écraser l’intégralité du tableau. Les valeurs du tableau ne sont donc pas fusionnées. Avec le niveau 1.2, le comportement de l'opération upsert est modifié pour insérer ou remplacer le document. Ce point est détaillé dans la section Niveau de compatibilité 1.2 ci-dessous.

Si le document JSON entrant a un champ ID existant, ce champ est automatiquement utilisé comme colonne d’ID de document dans Cosmos DB, et toutes les écritures suivantes sont traitées comme telles, ce qui conduit à l’une des situations suivantes :
- des ID uniques aboutissent à une insertion
- des ID dupliquées et « ID du document » défini sur « ID » aboutissent à une opération upsert
- des ID dupliquées et « ID du document » non défini aboutissent à une erreur après le premier document

Si vous souhaitez enregistrer <i>tous les</i> documents, y compris ceux avec un ID dupliqué, renommez le champ d’ID dans votre requête (avec le mot clé AS) et laissez Cosmos DB créer le champ d’ID, ou remplacez l’ID par une autre valeur de la colonne (avec le mot clé AS ou avec le paramètre « ID de document »).

## <a name="data-partitioning-in-cosmos-db"></a>Partitionnement des données dans Cosmos DB
Les conteneurs Azure Cosmos DB [illimités](../cosmos-db/partition-data.md) constituent l'approche recommandée pour le partitionnement des données, car Azure Cosmos DB procède automatiquement à la mise à l'échelle des partitions en fonction de votre charge de travail. Pendant une opération d'écriture dans des conteneurs illimités, Stream Analytics utilise autant d'enregistreurs parallèles que l'étape de requête précédente ou le schéma de partitionnement d'entrée.
> [!Note]
> À ce stade, Azure Stream Analytics prend uniquement en charge un nombre illimité de collections avec des clés de partition au niveau supérieur. Par exemple, la clé de partition `/region` est prise en charge. Les clés de partition imbriquées (par exemple, `/region/name`) ne sont pas prises en charge. 

Pour les collections Azure Cosmos DB fixes arrivées à saturation, Stream Analytics n’offre pas de possibilité de montée en puissance ou en parallèle. Elles ont une limite maximale de 10 Go et un débit de 10 000 RU/s.  Pour migrer les données d’un conteneur fixe vers un conteneur illimité (par exemple avec au moins 1 000 RU/s et une clé de partition), vous devez utiliser [l’outil de migration de données](../cosmos-db/import-data.md) ou la [bibliothèque de flux de modification](../cosmos-db/change-feed.md).

L’écriture dans plusieurs conteneurs fixes étant dépréciée, elle n’est pas l’approche recommandée pour mettre à l’échelle votre travail Stream Analytics. L’article [Partitionnement et mise à l’échelle dans Cosmos DB](../cosmos-db/sql-api-partition-data.md) fournit des détails supplémentaires.

## <a name="improved-throughput-with-compatibility-level-12"></a>Débit amélioré avec le niveau de compatibilité 1.2
Avec le niveau de compatibilité 1.2, Stream Analytics prend en charge l’intégration native pour écrire en bloc dans Cosmos DB. Cela permet d’écrire efficacement dans Cosmos DB avec un débit optimisé et de gérer efficacement les requêtes de limitation. Le mécanisme d’écriture amélioré est disponible sous un nouveau niveau de compatibilité en raison d’une différence de comportement de l'opération upsert.  Avant le niveau 1.2, le comportement de l'opération upsert consiste à insérer ou fusionner le document. Avec le niveau 1.2, l'opération upsert consiste à insérer ou remplacer le document. 

Avant le niveau 1.2, une procédure stockée personnalisée est utilisée pour exécuter en bloc l'opération upsert sur les documents par clé de partition dans Cosmos DB, où un lot est écrit en tant que transaction. Si un seul enregistrement se heure à une erreur temporaire (limitation), le lot dans son intégralité doit faire l'objet d'une nouvelle tentative. Cela ralentit considérablement les scénarios présentant des limitations raisonnables. La comparaison suivante illustre le comportement des travaux avec le niveau 1.2.

La configuration ci-dessous montre deux travaux Stream Analytics identiques lisant à partir de la même entrée (Event Hub). Les deux travaux Stream Analytics sont [entièrement partitionnés](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) avec une requête passthrough et écrivent dans des collections Cosmos DB identiques. Les métriques de gauche proviennent du travail configuré avec le niveau de compatibilité 1.0 et les métriques de droite avec le niveau 1.2. La clé de partition des collections Cosmos DB correspond à un GUID unique issu de l'événement d'entrée.

![Comparaison des métriques Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Le taux d’événements entrants dans Event Hub est deux fois supérieur à ce que les Cosmos DB (unités de requête de 20 Ko) peuvent recevoir, et dès lors une limitation est à prévoir dans Cosmos DB. Cela étant, le travail configuré avec le niveau 1.2 écrit de manière cohérente à un débit plus élevé (Événements de sortie/minute) et utilise un pourcentage moins élevé d'unités de stockage. Dans votre environnement, cette différence dépend de plusieurs autres facteurs tels que le format des événements, la taille des événements d'entrée/messages, les clés de partition, les requêtes, etc.

![Comparaison des métriques Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Avec le niveau de compatibilité 1.2, Stream Analytics utilise de manière plus intelligente 100 % du débit disponible dans Cosmos DB, avec très peu de nouvelles soumissions dues aux limitations. Cela permet une meilleure expérience pour les autres charges de travail telles que les requêtes en cours d’exécution sur la collection en même temps. S'il vous faut tester la manière dont ASA évolue avec Cosmos DB en tant que récepteur pour 1 à 10 K de messages/seconde, ce [projet d’exemples Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) est à votre disposition.
Veuillez noter que le débit de sortie Cosmos DB est identique avec les niveaux 1.0 et 1.1. Le niveau 1 2 ne correspondant actuellement pas au niveau par défaut, pouvez [définir le niveau de compatibilité](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) d’un travail Stream Analytics à l’aide du portail ou en appelant [l’API REST create job](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Il est *vivement recommandé* d'utiliser le niveau de compatibilité 1.2 dans ASA avec Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Paramètres de Cosmos DB pour la sortie JSON

Lorsque vous créez une sortie Cosmos DB dans Stream Analytics, vous devez fournir des informations supplémentaires, comme indiqué ci-dessous. Cette section fournit une explication de la définition des propriétés.

![écran de sortie documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Champ           | Description|
|-------------   | -------------|
|Alias de sortie    | Alias faisant référence à cette sortie dans votre requête ASA.|
|Abonnement    | Sélectionnez l’abonnement Azure.|
|ID de compte      | Nom ou URI de point de terminaison du compte Azure Cosmos DB.|
|Clé de compte     | Clé d’accès partagé du compte Azure Cosmos DB.|
|Base de données        | Nom de la base de données Azure Cosmos DB.|
|Modèle de nom de collection | Nom de la collection à utiliser `MyCollection` est un exemple d’entrée valide ; une collection nommée `MyCollection` doit exister.  |
|ID du document     | facultatif. Nom de colonne dans les événements de sortie utilisé comme clé unique sur laquelle doivent être basées les opérations d’insertion ou de mise à jour. Si vide, tous les événements sont insérés, sans option de mise à jour.|
