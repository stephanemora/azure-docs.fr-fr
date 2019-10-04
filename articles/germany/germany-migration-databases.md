---
title: Migrer des ressources de base de données Azure d’Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de base de données Azure d’Azure Allemagne vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: bfa076f33ce3b2e7315a07717bba5768b89511c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033737"
---
# <a name="migrate-database-resources-to-global-azure"></a>Migrer des ressources de base de données vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources de base de données Azure d’Azure Allemagne vers Azure global.

## <a name="sql-database"></a>Base de données SQL

Pour migrer des plus petites charges de travail Azure SQL Database, utilisez la fonction d’exportation pour créer un fichier BACPAC. Un fichier BACPAC est un fichier (zippé) compressé qui contient des métadonnées et des données issues de la base de données SQL Server. Après avoir créé le fichier BACPAC, vous pouvez copier le fichier dans l’environnement cible (par exemple, en utilisant AzCopy) et utiliser la fonction d’importation pour régénérer la base de données. Tenez compte des points suivants :

- Pour qu’une exportation soit cohérente, assurez-vous qu’une des conditions suivantes est remplie :
  - Aucune activité d’écriture ne survient lors de l’exportation.
  - Vous exportez une copie cohérente au niveau transactionnel d’une base de données SQL.
- Pour exporter vers le stockage Blob Azure, la taille du fichier BACPAC est limitée à 200 Go. Pour exporter un fichier BACPAC plus volumineux, exportez dans le stockage local.
- Si l’opération d’exportation à partir de la base de données SQL Database prend plus de 20 heures, l’opération peut être annulée. Consultez les articles suivants pour obtenir des conseils sur la façon d’améliorer les performances.

> [!NOTE]
> La chaîne de connexion change après l’opération d’exportation, car le nom DNS du serveur change pendant l’exportation.

Pour plus d'informations :

- Découvrez comment [Exporter une base de données vers un fichier BACPAC](../sql-database/sql-database-export.md).
- Découvrez comment [Importer un fichier BACPAC dans une base de données](../sql-database/sql-database-import.md).
- Consultez la [Documentation Azure SQL Database](https://docs.microsoft.com/azure/sql-database/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sql-data-warehouse"></a>SQL Data Warehouse

Pour migrer les ressources Azure SQL Data Warehouse d’Azure Allemagne vers Azure global, suivez les étapes décrites dans Azure SQL Data Warehouse.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Vous pouvez utiliser l’outil de migration de données Azure Cosmos DB pour migrer vos données vers Azure Cosmos DB. Il s’agit d’une solution open source qui importe des données dans Azure Cosmos DB à partir de différentes sources.

L’outil de migration de données Azure Cosmos DB est disponible sous la forme d’un outil d’interface graphique ou d’un outil de ligne de commande. Le code source est disponible dans le référentiel GitHub [de l’outil de migration de données Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool). Une [version compilée de l’outil](https://www.microsoft.com/download/details.aspx?id=46436) est disponible dans le Centre de téléchargement Microsoft.

Pour migrer des ressources Azure Cosmos DB, nous vous recommandons de procéder comme suit :

1. Passez en revue les exigences relatives à la durée de fonctionnement et aux configurations de compte, afin de déterminer le meilleur plan d’action.
1. Clonez les configurations de compte à partir d’Azure Allemagne vers la nouvelle région, en exécutant l’outil de migration de données.
1. Si vous pouvez utiliser une fenêtre de maintenance, copiez les données de la source sur la destination, via l’outil de migration de données.
1. Sinon, copiez les données de la source sur la destination via l’outil, puis procédez comme suit :
   1. Utilisez une approche basée sur la configuration pour modifier les lectures/écritures dans une application.
   1. Effectuez une synchronisation de type synchronisation initiale.
   1. Configurez une synchronisation incrémentielle et rattrapez le flux de modification.
   1. Faites pointer les lectures vers le nouveau compte et validez l’application.
   1. Arrêtez les écritures sur l’ancien compte, vérifiez que vous avez rattrapé le flux de modification, puis faites pointer les écritures vers le nouveau compte.
   1. Arrêtez l’outil et supprimez l’ancien compte.
1. Exécutez l’outil pour valider la cohérence des données entre l’ancien et le nouveau compte.

Pour plus d'informations :

- Lisez une [présentation d’Azure Cosmos DB](../cosmos-db/introduction.md).
- Apprenez comment [importer des données dans Azure Cosmos DB](../cosmos-db/import-data.md).

## <a name="azure-cache-for-redis"></a>Cache Azure pour Redis

Plusieurs options s’offrent à vous pour migrer une instance de cache Azure pour Redis d’Azure Allemagne vers Azure global. L’option choisie dépend de vos besoins.

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>Option 1 : Accepter la perte de données et créer une nouvelle instance

Cette approche est idéale lorsque les deux conditions suivantes sont remplies :

- Vous utilisez le cache Azure pour Redis comme cache de données temporaire.
- Votre application remplit à nouveau les données du cache automatiquement dans la nouvelle région.

Pour migrer avec une perte de données et créer une nouvelle instance :

1. Créez une nouvelle instance de cache Azure pour Redis dans la nouvelle région cible.
1. Mettez à jour votre application de manière à utiliser la nouvelle instance dans la nouvelle région.
1. Supprimez l’ancienne instance de cache Azure pour Redis dans la région source.

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>Option 2 : Copier des données à partir de l’instance source vers l’instance cible

Un membre de l’équipe Cache Azure pour Redis a développé un outil open source qui copie les données à partir d’une instance de cache Azure pour Redis vers une autre sans avoir recours à la fonctionnalité d’importation ou d’exportation. Consultez l’étape 4 ci-dessous pour en savoir plus sur l’outil.

Pour copier des données de l’instance source vers l’instance cible :

1. Créez une machine virtuelle dans la région source. Si le jeu de données du cache Azure pour Redis est volumineux, sélectionnez une taille de machine virtuelle relativement puissante pour réduire le temps de copie.
1. Créez une nouvelle instance de cache Azure pour Redis dans la région cible.
1. Videz les données de l’instance **cible**. Assurez-vous de ne *pas* procéder au vidage à partir de l’instance **source**. Le vidage est nécessaire, car l’outil de copie *ne remplace pas* les clés existantes dans l’emplacement cible.
1. Utilisez l’outil suivant pour copier automatiquement des données de l’instance source Cache Azure pour Redis vers l’instance cible Cache Azure pour Redis : [Source de l’outil](https://github.com/deepakverma/redis-copy) et [téléchargement de l’outil](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip).

> [!NOTE]
> Ce processus peut prendre un certain temps selon la taille de votre jeu de données.

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>Option 3 : Exporter à partir de l’instance source et importer vers l’instance de destination

Cette approche tire parti des fonctionnalités uniquement disponibles au niveau Premium.

Pour exporter à partir de l’instance source et importer vers l’instance de destination :

1. Créez une nouvelle instance de cache Azure pour Redis de niveau Premium dans la région cible. Utiliser la même taille que l’instance de cache Azure pour Redis source.
1. [Exporter des données à partir du cache source](../redis-cache/cache-how-to-import-export-data.md) ou utiliser la [cmdlet PowerShell Export-AzRedisCache](/powershell/module/az.rediscache/export-azrediscache).

   > [!NOTE]
   > Le compte de stockage Azure d’exportation doit se trouver dans la même région que l’instance de cache.

1. Copiez les objets BLOB exportés vers un compte de stockage dans la région de destination (par exemple, en utilisant AzCopy).
1. [Importez des données dans le cache de destination](../redis-cache/cache-how-to-import-export-data.md) ou utilisez la [Import-AzRedisCAche PowerShell cmdlet](/powershell/module/az.rediscache/import-azrediscache).
1. Reconfigurez votre application de manière à utiliser l’instance de cache Azure pour Redis cible.

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>Option 4 : Écrire des données dans deux instances de cache Azure pour Redis et lire à partir d’une seule instance

Pour cette approche, vous devez modifier votre application. L’application doit écrire des données dans plusieurs instances de cache lors de la lecture à partir d’une des instances de cache. Cette approche est appropriée si les données stockées dans le cache Azure pour Redis répondent aux critères suivants :
- Les données sont actualisées régulièrement. 
- Toutes les données sont écrites dans l’instance de cache Azure pour Redis cible.
- Vous avez suffisamment de temps pour actualiser toutes les données.

Pour plus d'informations :

- Examinez la [vue d’ensemble du cache Azure pour Redis](../redis-cache/cache-overview.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
