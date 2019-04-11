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
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470404"
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
Pour respecter les exigences de votre application, Azure Cosmos DB vous permet d’ajuster la base de données et les collections et de trouver un compromis entre cohérence, disponibilité et latence. Selon les niveaux de cohérence de lecture qui s’appliquent à votre scénario compte tenu de la latence de lecture et d’écriture, vous pouvez choisir un niveau de cohérence sur votre compte de base de données. Par défaut, Azure Cosmos DB permet également une indexation synchrone sur chaque opération CRUD exécutée sur votre collection. Cette option est également utile pour contrôler les performances de lecture/écriture dans Azure Cosmos DB. Pour plus d’informations, consultez l’article [Modifier le niveau de cohérence des bases de données et des requêtes](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upserts à partir de Stream Analytics
L’intégration de Stream Analytics à Azure Cosmos DB vous permet d’insérer ou mettre à jour des enregistrements dans votre collection à partir d’une colonne d’identification de document donnée. C’est ce que l’on appelle un *Upsert*.

Stream Analytics utilise une approche optimiste de type upsert, c’est-à-dire que les mises à jour ne sont effectuées qu’en cas d’échec de l’insertion lié à un conflit d’ID de document. Avec le niveau de compatibilité 1.0, cette mise à jour est effectuée comme un correctif, elle offre des mises à jour partielles au document, autrement dit, l’ajout de nouvelles propriétés ou en remplaçant qu'une propriété existante est effectuée de manière incrémentielle. Toutefois, les modifications apportées aux valeurs des propriétés du tableau dans votre document JSON ont pour effet d’écraser l’intégralité du tableau. Les valeurs du tableau ne sont donc pas fusionnées. 1.2, comportement d’upsert est modifié pour insérer ou remplacer le document. Cela est décrite plus loin dans la section 1.2 de niveau de compatibilité ci-dessous.

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

## <a name="improved-throughput-with-compatibility-level-12"></a>Améliorer le débit avec 1.2 de niveau de compatibilité
Niveau de compatibilité 1.2, Stream Analytique prend en charge l’intégration native en masse d’écrire dans Cosmos DB. Cela permet d’écrire efficacement pour Cosmos DB avec optimisation de débit et efficacement les requêtes de limitation de handle. Le mécanisme d’écriture amélioré est disponible sous un nouveau niveau de compatibilité en raison d’une différence de comportement upsert.  Avant 1.2, le comportement d’upsert doit insérer ou fusionner le document. 1.2, upserts comportement est modifié pour insérer ou remplacer le document. 

Avant de 1.2, utilise une procédure stockée personnalisée aux documents d’upsert en bloc par clé de partition dans Cosmos DB, où un lot est écrit comme une transaction. Même si un seul enregistrement rencontre une erreur temporaire (limitation), la totalité du lot doit être retentée. Ce rendu scénarios avec limitation de la même raisonnable relativement plus lentement. Suivant la comparaison montre le comporte de ces travaux 1.2.

Ci-dessous le programme d’installation affiche les deux travaux d’Analytique de Stream identiques lecture à partir de la même entrée (hub d’événements). Les deux travaux d’Analytique de Stream est [entièrement partitionnée](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) avec une requête passthrough et l’écriture dans des collections COSMOS DB identiques. Mesures sur la gauche sont du travail configuré avec le niveau de compatibilité 1.0 et celles de droite est configuré avec 1.2. Clé de partition des collections COSMOS DB est un guid unique provenant de l’événement d’entrée.

![comparaison de métriques de flux de données analytique](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Taux d’événements entrants dans le concentrateur d’événements est de 2 x plus élevé que les collections Cosmos DB (20 Ko RU) sont configurées pour l’admission, afin de limitation est attendue dans Cosmos DB. Toutefois, le travail avec 1.2, écrit régulièrement à un débit plus élevé (événements de sortie/minute) et avec un taux d’utilisation moyenne SU % inférieur. Dans votre environnement, cette différence dépend de quelques autres facteurs tels que les choix de format de l’événement, taille de messages/événements d’entrée, clés de partition, etc. de requête.

![cosmos db metrics comparison](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1.2, Stream Analytique est plus intelligente en utilisant 100 % du débit disponible dans Cosmos DB avec très peu de nouvelles soumissions de limitation de la limitation du débit. Cela fournit une meilleure expérience pour les autres charges de travail comme les requêtes en cours d’exécution sur la collection en même temps. Au cas où vous deviez tester comment ASA peut évoluer avec Cosmos DB comme un récepteur pour k de 1 à 10k messages/seconde, voici une [projet d’exemples azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) qui vous permet de le faire.
Veuillez noter que le débit de sortie Cosmos DB est identique à 1.0 et 1.1. Étant donné que 1.2 n’est actuellement pas la valeur par défaut, vous pouvez [définir le niveau de compatibilité](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) pour un travail Stream Analytique à l’aide du portail ou à l’aide de la [créer appel d’API REST de travail](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Il a *fortement recommandé de* à utiliser 1.2 de niveau de compatibilité dans ASA avec Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Paramètres de Cosmos DB pour la sortie JSON

Lorsque vous créez une sortie Cosmos DB dans Stream Analytics, vous devez fournir des informations supplémentaires, comme indiqué ci-dessous. Cette section fournit une explication de la définition des propriétés.

![écran de sortie documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Champ           | Description|
|-------------   | -------------|
|Alias de sortie    | Alias faisant référence à cette sortie dans votre requête ASA.|
|Abonnement    | Choisissez l’abonnement Azure.|
|ID de compte      | Nom ou URI de point de terminaison du compte Azure Cosmos DB.|
|Clé de compte     | Clé d’accès partagé du compte Azure Cosmos DB.|
|Base de données        | Nom de la base de données Azure Cosmos DB.|
|Modèle de nom de collection | Nom de la collection à utiliser `MyCollection` est un exemple d’entrée valide - une collection nommée `MyCollection` doit exister.  |
|ID du document     | facultatif. Nom de colonne dans les événements de sortie utilisé comme clé unique sur laquelle doivent être basées les opérations d’insertion ou de mise à jour. Si vide, tous les événements sont insérés, sans option de mise à jour.|
