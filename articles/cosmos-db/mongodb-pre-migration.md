---
title: Étapes pour les migrations de données avant la migration à partir de MongoDB à l’API d’Azure Cosmos DB pour MongoDB
description: Ce document fournit une vue d’ensemble de la configuration requise pour une migration de données à partir de MongoDB à Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013905"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Étapes pour les migrations de données avant la migration à partir de MongoDB à l’API d’Azure Cosmos DB pour MongoDB

Avant de migrer vos données à partir de MongoDB (en local ou dans le cloud (IaaS)) à l’API d’Azure Cosmos DB pour MongoDB, vous devez :

1. [Création d’un compte Azure Cosmos DB](#create-account)
2. [Estimer le débit nécessaire pour vos charges de travail](#estimate-throughput)
3. [Choisir une clé de partition optimales pour vos données](#partitioning)
4. [Comprendre la stratégie d’indexation que vous pouvez définir sur vos données](#indexing)

Si vous avez déjà effectué les conditions préalables ci-dessus pour la migration, consultez le [MongoDB de migrer des données à l’API d’Azure Cosmos DB pour MongoDB](../dms/tutorial-mongodb-cosmos-db.md) pour les instructions de migration de données réelles. Si ce n’est pas le cas, ce document fournit des instructions pour gérer ces conditions préalables. 

## <a id="create-account"></a> Créer un compte Azure Cosmos DB 

Avant de commencer la migration, vous devez [créer un compte Azure Cosmos à l’aide des API d’Azure Cosmos DB pour MongoDB](create-mongodb-dotnet.md). 

Lors de la création de compte, vous pouvez choisir les paramètres à [distribuer globalement](distribute-data-globally.md) vos données. Vous avez également la possibilité d’activer plusieurs régions écritures (configuration multimaître), qui permet à chacune de vos zones de région de lecture et à la fois une écriture.

![Création du compte](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Estimer la nécessité de débit de vos charges de travail

Avant de commencer la migration à l’aide de la [Database Migration Service (DMS)](../dms/dms-overview.md), vous devez estimer la quantité de débit à configurer pour vos bases de données Azure Cosmos et les collections.

Débit peut être configuré sur soit :

- Collection

- Base de données

> [!NOTE]
> Vous pouvez également avoir une combinaison des éléments ci-dessus, où certaines collections dans une base de données peuvent avoir dédié de débit configuré, et d’autres utilisateurs peuvent partager le débit. Pour plus d’informations, consultez le [définir le débit sur une base de données et un conteneur](set-throughput.md) page.
>

Vous devez tout d’abord décider si vous souhaitez configurer la base de données ou débit au niveau de la collection ou une combinaison des deux. En règle générale, il est recommandé de configurer un débit dédié au niveau de la collection. Configurant le débit au niveau de la base de données permet aux collections dans votre base de données pour partager le débit approvisionné. Toutefois, avec un débit partagé, n’est pas garanti pour un débit spécifique sur chaque collection et vous n’obtenez pas des performances prévisibles sur n’importe quelle collection spécifique.

Si vous n’êtes pas sûr de la quantité de débit doit être dédié à chaque collection, vous pouvez choisir le débit du niveau de la base de données. Vous pouvez considérer le débit approvisionné configurée sur votre base de données Azure Cosmos en tant qu’équivaut logiquement à celle de la capacité de calcul d’une VM MongoDB ou un serveur physique, mais elle est plus rentable avec la possibilité de mettre à l’échelle. Pour plus d’informations, consultez [approvisionner le débit sur les bases de données et les conteneurs Azure Cosmos](set-throughput.md).

Si vous configurez le débit au niveau de la base de données, tous les regroupements créés au sein de cette base de données doivent être créés avec une clé de partition/partition. Pour plus d’informations sur le partitionnement, consultez [partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB](partition-data.md). Si vous ne spécifiez pas une clé de partition/partition pendant la migration, le Service de Migration de base de données Azure renseigne automatiquement le champ de clé de partition avec un *_id* attribut qui est généré automatiquement pour chaque document.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Nombre optimal d’unités de requête (RU) à configurer

Dans Azure Cosmos DB, le débit est configuré à l’avance et est mesuré en unités de requête (RU) par seconde. Si vous avez des charges de travail qui s’exécutent de MongoDB sur une machine virtuelle ou en local, considérez en RU comme une simple abstraction pour les ressources physiques, telles que pour la taille d’une machine virtuelle ou serveur sur un site et les ressources qu’ils possèdent, par exemple, mémoire, UC, e/s. 

Contrairement aux machines virtuelles ou des serveurs locaux, les unités de requête sont faciles à l’échelle à tout moment. Vous pouvez modifier le nombre d’unités réservées approvisionnées en quelques secondes, et vous êtes facturé uniquement pour le nombre maximal d’unités de requête que vous devez configurer pour une période d’une heure donnée. Pour en savoir plus, voir [Unités de requête dans Azure Cosmos DB](request-units.md).

Les facteurs clés qui affectent le nombre d’unités de requête nécessaires sont les suivantes :
- **Taille de l’élément (par exemple, le document)**: À mesure que la taille d’un élément/document augmente, le nombre d’unités de requête consommées pour lire ou écrire de l’élément ou de document augmente également.
- **Nombre de propriétés de l’élément** : En supposant que le [l’indexation par défaut](index-overview.md) sur toutes les propriétés, le nombre d’unités de requête consommées pour écrire un élément augmente en même temps que l’élément propriété count. Vous pouvez réduire la consommation d’unités de demande pour les opérations d’écriture par [limitant le nombre de propriétés indexées](index-policy.md).
- **Opérations simultanées**: Demander le nombre d’unités consommées dépend également de la fréquence avec les différentes opérations CRUD (par exemple, les écritures, les lectures, les mises à jour, suppressions) et l’exécution des requêtes plus complexes. Vous pouvez utiliser [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) pour les besoins de concurrence de vos données de MongoDB en cours de la sortie.
- **Modèles de requête** : La complexité d’une requête affecte le nombre d’unités de demande est consommé par la requête.

Si vous exportez les fichiers JSON à l’aide de [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) et comprendre le nombre d’écritures, lectures, met à jour et supprime qui se produisent par seconde, vous pouvez utiliser la [Planificateur de capacité Azure Cosmos DB](https://www.documentdb.com/capacityplanner) pour estimer la Nombre initial de l’approvisionnement d’unités de requête. Capacity planner ne pas tenir compte du coût des requêtes plus complexes. Par conséquent, si vous avez des requêtes complexes sur vos données, unités de requête supplémentaires seront consommées. La Calculatrice suppose également que tous les champs sont indexés, et la cohérence de session est utilisée. La meilleure façon de comprendre le coût des requêtes consiste à migrer vos données (ou les exemples de données) à Azure Cosmos DB, [se connecter au point de terminaison de Cosmos DB](connect-mongodb-account.md) et exécuter un exemple de requête à partir de l’interpréteur de commandes MongoDB à l’aide de la `getLastRequestStastistics` commande pour obtenir le frais de requête qui produira le nombre d’unités de requête consommées :

`db.runCommand({getLastRequestStatistics: 1})`

Cette commande génère un document JSON similaire à ce qui suit :

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Une fois que vous comprenez le nombre d’unités de requête consommées par une requête et les besoins de concurrence pour cette requête, vous pouvez ajuster le nombre d’unités réservées approvisionnées. Optimisation des unités de requête n’est pas un événement unique : vous devez en permanence optimiser ou mettre à l’échelle des unités de requête mis en service, selon que vous ne sont pas attend un trafic important, par opposition à une lourde charge de travail ou l’importation de données.

## <a id="partitioning"></a>Choisissez votre clé de partition
Le partitionnement est un aspect important de prendre en compte avant de migrer vers une base de données mondialement distribué comme Azure Cosmos DB. Azure Cosmos DB utilise le partitionnement à l’échelle des conteneurs individuels dans une base de données pour satisfaire les besoins d’évolutivité et les performances de votre application. Dans le partitionnement, les éléments dans un conteneur sont divisées en sous-ensembles distinctes, appelées partitions logiques. Pour plus d’informations et des recommandations sur le choix de la clé de partition appropriée pour vos données, consultez le [en choisissant une section de la clé de Partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Vos données d’index
Par défaut, Azure Cosmos DB indexe tous vos champs de données à l’ingestion. Vous pouvez modifier le [stratégie d’indexation](index-policy.md) dans Azure Cosmos DB à tout moment. En fait, il est souvent recommandé de désactiver l’indexation lors de la migration des données, puis réactiver lorsque les données sont déjà dans Cosmos DB. Pour plus d’informations sur l’indexation, vous pouvez en savoir plus sur dans le [l’indexation dans Azure Cosmos DB](index-overview.md) section. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migre automatiquement les collections MongoDB avec les index uniques. Toutefois, l’index uniques doivent être créés avant la migration. Azure Cosmos DB ne prend pas en charge la création d’index uniques, lorsqu’il existe déjà des données dans vos collections. Pour plus d’informations, consultez [Clés uniques dans Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Étapes suivantes
* [Migrer vos données MongoDB vers Cosmos DB à l’aide de Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Approvisionner le débit sur les bases de données et les conteneurs Azure Cosmos](set-throughput.md)
* [Partitioning in Azure Cosmos DB](partition-data.md) (Partitionnement dans Azure Cosmos DB)
* [Distribution mondiale dans Azure Cosmos DB](distribute-data-globally.md)
* [Indexation dans Azure Cosmos DB](index-overview.md)
* [Unités de requête dans Azure Cosmos DB](request-units.md)
