---
title: Options de migration Cosmos DB
description: Ce document décrit les différentes options de migration de vos données locales ou cloud vers Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984893"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Options de migration de vos données locales ou cloud vers Azure Cosmos DB

Vous pouvez charger des données à partir de différentes sources de données dans Azure Cosmos DB. De plus, comme Azure Cosmos DB prend en charge plusieurs API, les cibles peuvent être des API existantes quelconques. Pour prendre en charge les chemins de migration à partir des différentes sources vers les différentes API Azure Cosmos DB, plusieurs solutions fournissent une gestion spécialisée de chaque chemin de migration. Ce document liste les solutions disponibles et décrit leurs avantages et leurs limites.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Facteurs affectant le choix de l’outil de migration

Les facteurs suivants déterminent le choix de l’outil de migration :
* **Migration des données en ligne et hors connexion** : De nombreux outils de migration fournissent un chemin pour effectuer une migration unique. Cela signifie que les applications qui accèdent à la base de données peuvent subir un temps d’arrêt. Certaines solutions de migration offrent un moyen d’effectuer une migration dynamique dans laquelle un pipeline de réplication est configuré entre la source et la cible.

* **Source de données** : Les données existantes peuvent se trouver dans différentes sources de données comme Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL, etc. Les données peuvent également figurer dans un compte Azure Cosmos DB existant et l’objectif de la migration peut être de changer le modèle de données ou de repartitionner les données dans un conteneur avec une clé de partition différente.

* **API Azure Cosmos DB** : Pour l’API SQL dans Azure Cosmos DB, il existe un large éventail d’outils développés par l’équipe Azure Cosmos DB, qui peuvent aider dans les différents scénarios de migration. Toutes les autres API ont leur propre ensemble spécialisé d’outils développés et gérés par la communauté. Comme Azure Cosmos DB prend en charge ces API à un niveau de protocole filaire, ces outils doivent fonctionner en l’état également pendant la migration des données vers Azure Cosmos DB. Toutefois, ils peuvent nécessiter une gestion personnalisée des limitations, car ce concept est spécifique à Azure Cosmos DB.

* **Taille des données** : La plupart des outils de migration fonctionnent très bien pour de plus petits jeux de données. Lorsque le jeu de données dépasse quelques centaines de gigaoctets, le choix des outils de migration est limité. 

* **Durée de migration attendue** : Les migrations peuvent être configurées pour s’exécuter à un rythme lent et incrémentiel, qui consomme moins de débit ou qui peut consommer l’intégralité du débit provisionné sur le conteneur Azure Cosmos DB cible et effectuer la migration en un temps réduit.

## <a name="azure-cosmos-db-sql-api"></a>API SQL Azure Cosmos DB
|**Type de migration**|**Solution**|**Considérations**|
|---------|---------|---------|
|Hors connexion|[Outil de migration de données](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; Facile à configurer et prise en charge de plusieurs sources <br/>&bull; Non adapté aux jeux de données volumineux|
|Hors connexion|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db).|&bull; Facile à configurer et prise en charge de plusieurs sources <br/>&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux <br/>&bull; Absence de points de contrôle – Si un problème se produit au cours de la migration, vous devez redémarrer l’ensemble du processus de migration<br/>&bull; Absence de file d’attente de lettres mortes – Quelques fichiers erronés peuvent arrêter l’ensemble du processus de migration.|
|Hors connexion|[Connecteur Spark Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux <br/>&bull; Nécessite une configuration Spark personnalisée <br/>&bull; Spark est sensible aux incohérences de schéma et cela peut être un problème lors de la migration |
|Hors connexion|[Outil personnalisé avec la bibliothèque d’exécuteur en bloc Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; Fournit des fonctionnalités de points de contrôle et de lettres mortes qui renforcent la résilience de la migration <br/>&bull; Adapté aux jeux de données très volumineux (plus de 10 To)  <br/>&bull; Nécessite l’installation personnalisée de cet outil exécuté en tant qu’App Service |
|En ligne|[Fonctions Cosmos DB + API ChangeFeed](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; Facilité de configuration <br/>&bull; Fonctionne uniquement si la source est un conteneur Azure Cosmos DB <br/>&bull; Non adapté aux jeux de données volumineux <br/>&bull; Ne capture pas les suppressions à partir du conteneur source |
|En ligne|[Service de migration personnalisé utilisant ChangeFeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull; Assure le suivi de la progression <br/>&bull; Fonctionne uniquement si la source est un conteneur Azure Cosmos DB <br/>&bull; Fonctionne également pour les jeux de données plus volumineux <br/>&bull; Exige de l’utilisateur qu’il configure un App Service pour héberger le processeur de flux de modification <br/>&bull; Ne capture pas les suppressions à partir du conteneur source|
|En ligne|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; Fonctionne avec un vaste éventail de sources comme Oracle, DB2, SQL Server <br/>&bull; Facilite la génération de pipelines ETL et fournit un tableau de bord pour la surveillance <br/>&bull; Prend en charge des jeux de données plus volumineux <br/>&bull; Comme il s’agit d’un outil tiers, il doit être acheté sur la place de marché et installé dans l’environnement de l’utilisateur|

## <a name="azure-cosmos-db-mongo-api"></a>API Mongo d’Azure Cosmos DB
|**Type de migration**|**Solution**|**Considérations**|
|---------|---------|---------|
|Hors connexion|[Outil de migration de données](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; Facile à configurer et prise en charge de plusieurs sources <br/>&bull; Non adapté aux jeux de données volumineux|
|Hors connexion|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db).|&bull; Facile à configurer et prise en charge de plusieurs sources <br/>&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux <br/>&bull; L’absence de points de contrôle signifie que tout problème au cours de la migration impose le redémarrage de l’ensemble du processus de migration<br/>&bull; L’absence de file d’attente de lettres mortes signifie que quelques fichiers erronés peuvent arrêter l’ensemble du processus de migration <br/>&bull; Nécessite du code personnalisé pour augmenter le débit de lecture pour certaines sources de données|
|Hors connexion|[Outils Mongo existants (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Facilité de configuration et d’intégration <br/>&bull; Nécessite une gestion personnalisée des limitations|
|En ligne|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; Utilise la bibliothèque d’exécuteurs en bloc Azure Cosmos DB <br/>&bull; Adapté aux jeux de données volumineux et chargé de la réplication des modifications dynamiques <br/>&bull; Fonctionne uniquement avec d’autres sources MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API Cassandra Azure Cosmos DB
|**Type de migration**|**Solution**|**Considérations**|
|---------|---------|---------|
|Hors connexion|[Commande cqlsh COPY](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; Facilité de configuration <br/>&bull; Non adapté aux jeux de données volumineux <br/>&bull; Fonctionne uniquement lorsque la source est une table Cassandra|
|Hors connexion|[Copier la table avec Spark](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; Peut tirer profit des fonctionnalités Spark pour paralléliser la transformation et l’ingestion <br/>&bull; Nécessite une configuration avec une stratégie personnalisée de nouvelles tentatives pour gérer les limitations|
|En ligne|[Striim (à partir d’Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; Fonctionne avec un vaste éventail de sources comme Oracle, DB2, SQL Server <br/>&bull; Facilite la génération de pipelines ETL et fournit un tableau de bord pour la surveillance <br/>&bull; Prend en charge des jeux de données plus volumineux <br/>&bull; Comme il s’agit d’un outil tiers, il doit être acheté sur la place de marché et installé dans l’environnement de l’utilisateur|
|En ligne|[ (à partir d’Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; Prend en charge des jeux de données plus volumineux <br/>&bull; Comme il s’agit d’un outil tiers, il doit être acheté sur la place de marché et installé dans l’environnement de l’utilisateur|

## <a name="other-apis"></a>Autres API
Pour les API autres que l’API SQL, l’API Mongo et l’API Cassandra, différents outils sont pris en charge par les écosystèmes existants de chaque API. 

**API de table** 
* [Outil de migration de données](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AZCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**API Gremlin**
* [Bibliothèque d’exécuteurs en bloc Graph](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus, essayez les exemples d'applications utilisant la bibliothèque d'Exécuteurs en bloc en [.NET](bulk-executor-dot-net.md) et [Java](bulk-executor-java.md). 
* La bibliothèque d'Exécuteurs en bloc est intégrée au connecteur Spark Cosmos DB. Pour plus d'informations, consultez [Connecteur Spark Azure Cosmos DB](spark-connector.md).  
* Pour contacter l’équipe Azure Cosmos DB en ouvrant un ticket de support sous le type de problème « Conseils généraux », sous-type de problème « Grandes (To+) migrations » pour obtenir une aide supplémentaire lors des migrations à grande échelle.
