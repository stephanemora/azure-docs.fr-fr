---
title: Sortie Azure Stream Analytics dans Cosmos DB
description: Cet article décrit comment utiliser Azure Stream Analytics pour enregistrer dans Azure Cosmos DB la sortie JSON à des fins d’archivage des données et d’exécution de requêtes à faible latence sur des données JSON non structurées.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 9bdb012db2e7502d765fd342a636591bbbcb2c6c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311736"
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
> Vous devez ajouter 0.0.0.0 à la liste des adresses IP autorisées de votre pare-feu Azure Cosmos DB.

Vous trouverez ci-dessous le détail de certaines options de collection Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Régler la cohérence, la disponibilité et la latence
Pour respecter les exigences de votre application, Azure Cosmos DB vous permet d’ajuster la base de données et les collections, et de faire les meilleurs compromis entre cohérence, disponibilité et latence. Selon les niveaux de cohérence de lecture qui s’appliquent à votre scénario compte tenu de la latence de lecture et d’écriture, vous pouvez choisir un niveau de cohérence sur votre compte de base de données. Par défaut, Azure Cosmos DB permet également une indexation synchrone sur chaque opération CRUD exécutée sur votre collection. Cette option est également utile pour contrôler les performances de lecture/écriture dans Azure Cosmos DB. Pour plus d’informations, consultez l’article [Modifier le niveau de cohérence des bases de données et des requêtes](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upserts à partir de Stream Analytics
L’intégration de Stream Analytics à Azure Cosmos DB vous permet d’insérer ou mettre à jour des enregistrements dans votre collection à partir d’une colonne d’identification de document donnée. C’est ce que l’on appelle un *Upsert*.

Stream Analytics utilise une approche optimiste de type upsert, c’est-à-dire que les mises à jour ne sont effectuées qu’en cas d’échec de l’insertion lié à un conflit d’ID de document. Cette mise à jour est effectuée sous la forme d’un correctif, de sorte que le document peut être partiellement mis à jour. Autrement dit, l’ajout de nouvelles propriétés ou le remplacement d’une propriété existante est effectué de façon incrémentielle. Toutefois, les modifications apportées aux valeurs des propriétés du tableau dans votre document JSON ont pour effet d’écraser l’intégralité du tableau. Les valeurs du tableau ne sont donc pas fusionnées.

Si le document JSON entrant a un champ ID existant, ce champ est automatiquement utilisé comme colonne d’ID de document dans Cosmos DB, et toutes les écritures suivantes sont traitées comme telles, ce qui conduit à l’une des situations suivantes :
- des ID uniques aboutissent à une insertion
- des ID dupliquées et « ID du document » défini sur « ID » aboutissent à une opération upsert
- des ID dupliquées et « ID du document » non défini aboutissent à une erreur après le premier document

Si vous souhaitez enregistrer <i>tous les</i> documents, y compris ceux avec un ID dupliqué, renommez le champ d’ID dans votre requête (avec le mot clé AS) et laissez Cosmos DB créer le champ d’ID, ou remplacez l’ID par une autre valeur de la colonne (avec le mot clé AS ou avec le paramètre « ID de document »).

## <a name="data-partitioning-in-cosmos-db"></a>Partitionnement des données dans Cosmos DB
Les conteneurs Azure Cosmos DB [illimités](../cosmos-db/partition-data.md) constituent l’approche recommandée pour le partitionnement des données, car Azure Cosmos DB met automatiquement à l’échelle les partitions en fonction de votre charge de travail. Pendant une opération d’écriture dans des conteneurs illimités, Stream Analytics utilise autant d’enregistreurs parallèles que d’étapes de requête précédentes ou de schémas de partitionnement d’entrée.
> [!Note]
> À ce stade, Azure Stream Analytics prend uniquement en charge un nombre illimité de collections avec des clés de partition au niveau supérieur. Par exemple, la clé de partition `/region` est prise en charge. Les clés de partition imbriquées (par exemple, `/region/name`) ne sont pas prises en charge. 

Pour les collections Azure Cosmos DB fixes arrivées à saturation, Stream Analytics n’offre pas de possibilité de montée en puissance ou en parallèle. Elles ont une limite maximale de 10 Go et un débit de 10 000 RU/s.  Pour migrer les données d’un conteneur fixe vers un conteneur illimité (par exemple avec au moins 1 000 RU/s et une clé de partition), vous devez utiliser [l’outil de migration de données](../cosmos-db/import-data.md) ou la [bibliothèque de flux de modification](../cosmos-db/change-feed.md).

L’écriture dans plusieurs conteneurs fixes étant dépréciée, elle n’est pas l’approche recommandée pour mettre à l’échelle votre travail Stream Analytics. L’article [Partitionnement et mise à l’échelle dans Cosmos DB](../cosmos-db/sql-api-partition-data.md) fournit des détails supplémentaires.

## <a name="cosmos-db-settings-for-json-output"></a>Paramètres de Cosmos DB pour la sortie JSON
Lorsque vous créez une sortie Cosmos DB dans Stream Analytics, vous devez fournir des informations supplémentaires, comme indiqué ci-dessous. Cette section fournit une explication de la définition des propriétés.


![écran de sortie documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Champ           | Description 
-------------   | -------------
Alias de sortie    | Alias faisant référence à cette sortie dans votre requête ASA   
Nom du compte    | Nom ou URI de point de terminaison du compte Azure Cosmos DB 
Clé du compte     | Clé d’accès partagé du compte Azure Cosmos DB
Base de données        | Nom de la base de données Azure Cosmos DB
Nom de la collection | Nom de la collection à utiliser `MyCollection` est un exemple d’entrée valide ; une collection nommée `MyCollection` doit exister.  
ID du document     | facultatif. Nom de colonne dans les événements de sortie utilisé comme clé unique sur laquelle doivent être basées les opérations d’insertion ou de mise à jour. Si vide, tous les événements sont insérés, sans option de mise à jour.
