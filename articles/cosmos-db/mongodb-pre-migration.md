---
title: Étapes de prémigration pour la migrations de données vers l’API Azure Cosmos DB pour MongoDB
description: Ce document fournit une vue d’ensemble des prérequis pour une migration de données de MongoDB vers Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445194"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Étapes de prémigration pour les migrations de données de MongoDB vers l’API Azure Cosmos DB pour MongoDB

Avant de migrer vos données de MongoDB (local ou dans le cloud (IaaS)) vers l’API Azure Cosmos DB pour MongoDB, vous devez :

1. [Création d’un compte Azure Cosmos DB](#create-account)
2. [Estimer le débit nécessaire pour vos charges de travail](#estimate-throughput)
3. [Choisir une clé de partition optimale pour vos données](#partitioning)
4. [Comprendre la stratégie d’indexation que vous pouvez définir sur vos données](#indexing)

Si vous avez déjà effectué les étapes prérequises ci-dessus pour la migration, consultez la page [Migrer des données MongoDB vers l’API Azure Cosmos DB pour MongoDB ](../dms/tutorial-mongodb-cosmos-db.md) pour obtenir les instructions de migration de données proprement dites. Dans le cas contraire, ce document fournit des instructions pour gérer ces prérequis. 

## <a id="create-account"></a> Créer un compte Azure Cosmos DB 

Avant de commencer la migration, vous devez [créer un compte Azure Cosmos à l’aide de l’API Azure Cosmos DB pour MongoDB](create-mongodb-dotnet.md). 

Lors de la création du compte, vous pouvez choisir des paramètres pour [distribuer globalement](distribute-data-globally.md) vos données. Vous avez également la possibilité d’activer les écritures multirégion (ou configuration multimaître), ce qui permet à chacune de vos régions d’être à la fois une région d’écriture et de lecture.

![Création du compte](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Estimer le débit nécessaire pour vos charges de travail

Avant de commencer la migration à l’aide de [Database Migration Service (DMS)](../dms/dms-overview.md), vous devez estimer le débit à provisionner pour vos collections et bases de données Azure Cosmos.

Le débit peut être provisionné au choix sur :

- Collection

- Base de données

> [!NOTE]
> Vous pouvez également avoir une combinaison des deux, où certaines collections dans une base de données peuvent avoir un débit provisionné dédié et d’autres peuvent partager le débit. Pour plus d’informations, consultez la page [Provisionner le débit sur une base de données et un conteneur](set-throughput.md).
>

Vous devez tout d’abord décider si vous souhaitez provisionner le débit au niveau de la collection ou de la base de données, ou une combinaison des deux. En règle générale, nous vous recommandons de configurer un débit dédié au niveau de la collection. Le provisionnement du débit au niveau de la base de données permet aux collections dans votre base de données de partager le débit provisionné. Avec le débit partagé, toutefois, il n’existe aucune garantie de débit spécifique sur chaque collection, et vous ne bénéficiez de performances prévisibles sur aucune collection spécifique.

Si vous n’êtes pas sûr de la quantité de débit à dédier à chaque collection, vous pouvez choisir le débit au niveau de la base de données. Vous pouvez considérer le débit provisionné configuré sur votre base de données Azure Cosmos comme un équivalent logique de la capacité de calcul d’un serveur de machines virtuelles MongoDB ou d’un serveur physique, mais plus rentable et avec une fonctionnalité de mise à l’échelle élastique. Pour plus d’informations, consultez [Provisionner le débit sur les conteneurs et les bases de données Azure Cosmos](set-throughput.md).

Si vous provisionnez le débit au niveau de la base de données, toutes les collections créées au sein de cette base de données doivent être créées avec une clé de partition. Pour plus d’informations sur le partitionnement, consultez [Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB](partition-data.md). Si vous ne spécifiez pas de clé de partition pendant la migration, Azure Database Migration Service renseigne automatiquement le champ de clé de partition avec un attribut *_id* qui est généré automatiquement pour chaque document.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Nombre optimal d’unités de requête (RU) à provisionner

Dans Azure Cosmos DB, le débit est provisionné à l’avance et mesuré en unités de requête par seconde. Si vous avez des charges de travail qui s’exécutent MongoDB sur une machine virtuelle ou localement, considérez les unités de requête comme une simple abstraction des ressources physiques, telles que la taille d’une machine virtuelle ou d’un serveur local et les ressources qu’ils ont (mémoire, processeur, E/S). 

Contrairement aux machines virtuelles ou aux serveurs locaux, il est facile d’effectuer un scale-up ou scale-down des unités de requête à tout moment. Vous pouvez modifier le nombre d’unités de requête provisionnées en quelques secondes, et vous êtes facturé uniquement pour le nombre maximal d’unités de requête que vous provisionnez pour une période d’une heure donnée. Pour en savoir plus, voir [Unités de requête dans Azure Cosmos DB](request-units.md).

Les principaux facteurs qui affectent le nombre d’unités de requête nécessaires sont les suivants :
- **Taille de l’élément (c’est-à-dire du document)**  : plus la taille d’un élément/document augmente, plus le nombre d’unités de requête consommées pour le lire ou l’écrire augmente.
- **Nombre de propriétés de l’élément** : sur la base d’une [indexation par défaut](index-overview.md) de toutes les propriétés, le nombre d’unités de requête consommées pour écrire un élément augmente avec le nombre de propriétés de l’élément. Vous pouvez réduire la consommation d’unités de requête pour les opérations d’écriture en [limitant le nombre de propriétés indexées](index-policy.md).
- **Opérations simultanées** : le nombre d’unités de requête consommées dépend également de la fréquence d’exécution des différentes opérations CRUD (écritures, lectures, mises à jour, suppressions) et des requêtes plus complexes. Vous pouvez utiliser [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) pour déterminer les besoins de vos données MongoDB actuelles en matière de concurrence.
- **Modèles de requête** : la complexité d’une requête a une incidence sur le nombre d’unités de requête qu’elle consomme.

Si vous exportez des fichiers JSON à l’aide de [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) et que vous connaissez le nombre d’écritures, de lectures, de mises à jour et de suppressions qui se produisent par seconde, vous pouvez utiliser le [planificateur de capacité Azure Cosmos DB](https://www.documentdb.com/capacityplanner) pour estimer le nombre initial d’unités de requête à provisionner. Le planificateur de capacité ne tient pas compte du coût des requêtes plus complexes. Par conséquent, si vous avez des requêtes complexes sur vos données, des unités de requête supplémentaires seront consommées. La calculatrice part également du principe que tous les champs sont indexés et que la cohérence de session est utilisée. La meilleure façon de comprendre le coût des requêtes consiste à migrer vos données (ou exemples de données) vers Azure Cosmos DB, de [vous connecter au point de terminaison Cosmos DB](connect-mongodb-account.md) et d’exécuter un exemple de requête à partir de l’interpréteur de commandes MongoDB à l’aide de la commande `getLastRequestStastistics` pour obtenir les frais de requête, ce qui génèrera le nombre d’unités de requête consommées :

`db.runCommand({getLastRequestStatistics: 1})`

Cette commande génère un document JSON semblable au suivant :

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Une fois que vous connaissez le nombre d’unités de requête consommées par une requête et les besoins en concurrence pour cette requête, vous pouvez ajuster le nombre d’unités de requête provisionnées. L’optimisation des unités de requête n’est pas un événement ponctuel : vous devez en permanence optimiser ou effectuer un scale-up des unités de requête provisionnées, selon l’importance du trafic attendu, par opposition à une lourde charge de travail ou l’importation de données.

## <a id="partitioning"></a>Choisir votre clé de partition
Le partitionnement est un aspect essentiel à prendre en compte avant de migrer vers une base de données mondialement distribuée comme Azure Cosmos DB. Azure Cosmos DB utilise le partitionnement pour procéder à la mise à l’échelle des conteneurs au sein d’une base de données afin de répondre aux besoins de scalabilité et de performances de votre application. Avec le partitionnement, les éléments d’un conteneur sont répartis en sous-ensembles distincts, appelés partitions logiques. Pour plus d’informations et pour obtenir des recommandations concernant le choix de la clé de partition appropriée pour vos données, consultez la section [Choix d’une de clé de partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexer vos données
Par défaut, Azure Cosmos DB indexe tous vos champs de données lors de l’ingestion. Vous pouvez modifier la [stratégie d’indexation](index-policy.md) dans Azure Cosmos DB à tout moment. En fait, il est souvent recommandé de désactiver l’indexation lors de la migration des données, puis de la réactiver quand les données sont déjà dans Cosmos DB. Pour plus d’informations sur l’indexation, consultez la section [Indexation dans Azure Cosmos DB](index-overview.md). 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migre automatiquement les collections MongoDB avec des index uniques. Toutefois, les index uniques doivent être créés avant la migration. Azure Cosmos DB ne prend pas en charge la création d’index uniques quand il existe déjà des données dans vos collections. Pour plus d’informations, consultez [Clés uniques dans Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Étapes suivantes
* [Migrer vos données MongoDB vers Cosmos DB à l’aide de Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisionner le débit sur les conteneurs et les bases de données Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Partitionnement dans Azure Cosmos DB)
* [Distribution de données mondiale avec Azure Cosmos DB](distribute-data-globally.md)
* [Indexation dans Azure Cosmos DB](index-overview.md)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
