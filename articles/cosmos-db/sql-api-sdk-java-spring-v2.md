---
title: Spring Data Azure Cosmos DB v2 pour les notes de publication et les ressources de l’API SQL
description: Découvrez Spring Data Azure Cosmos DB v2 pour l’API SQL, notamment les dates de mise en production, dates de mise hors service et modifications apportées entre chaque version du kit de développement logiciel (SDK) Java asynchrone SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590476"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Spring Data Azure Cosmos DB v2 pour l’API Core (SQL) : notes de publication et ressources
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK .NET Change Feed v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [SDK Java v4](sql-api-sdk-java-v4.md)
> * [SDK Java Async v2](sql-api-sdk-async-java.md)
> * [SDK Java Sync v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [API REST Resource Provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Exécuteur en bloc – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Exécuteur en bloc – Java](sql-api-sdk-bulk-executor-java.md)

Spring Data Azure Cosmos DB v2 pour Core (SQL) permet aux développeurs d’utiliser Azure Cosmos DB dans des applications Spring. Spring Data Azure Cosmos DB expose l’interface de données Spring pour manipuler les bases de données et les collections, travailler avec des documents et émettre des requêtes. Les API synchrones et asynchrones (réactives) sont prises en charge dans le même artefact Maven. 

L’[infrastructure Spring](https://spring.io/projects/spring-framework) est un modèle de programmation et de configuration qui rationalise le développement d’applications Java. Pour citer le site web de l’organisation, Spring rationalise la « plomberie » des applications à l’aide de l’injection de dépendances. De nombreux développeurs comme Spring parce que la génération et le test d’applications deviennent plus simples. [Spring Boot](https://spring.io/projects/spring-boot) étend cette idée de la gestion de la plomberie avec un œil sur l’application web et le développement de microservices. [Spring Data](https://spring.io/projects/spring-data) est un modèle de programmation permettant l’accès à des magasins de données tels que Azure Cosmos DB à partir du contexte d’une application Spring ou Spring Boot. 

Vous pouvez utiliser Spring Data Azure Cosmos DB dans vos applications [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/).

> [!IMPORTANT]  
> Ces notes de publication sont destinées à la version v2 de Spring Data Azure Cosmos DB. Vous trouverez les notes de publication v3 [ici](sql-api-sdk-java-spring-v3.md). 
>
> Spring Data Azure Cosmos DB prend en charge uniquement l’API SQL.
>
> Les guides suivants ont trait à Spring Data sur d’autres API de Azure Cosmos DB :
> * [Spring Data pour Apache Cassandra avec Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB avec Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin avec Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Vous souhaitez vous familiariser rapidement ?
> 1. Installez le [Runtime Java minimal pris en charge, JDK 8,](/java/azure/jdk/?view=azure-java-stable) pour pouvoir utiliser le Kit de développement logiciel (SDK).
> 2. Créez une application Spring Data Azure Cosmos DB à l’aide du starter- [c’est facile](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) !
> 3. Utilisez le [Guide de développeurs Spring Data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) qui vous guide pas à pas à travers les requêtes de base Azure Cosmos DB.
>
> Vous pouvez créer rapidement des applications Spring Boot Starter avec [Spring Initializer](https://start.spring.io/) !
>

## <a name="helpful-content"></a>Contenu de l’aide

| Contenu | Lien |
|---|---|
| **Téléchargement du Kit de développement logiciel (SDK)** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentation de l’API** | [Documentation de référence Spring Data Azure Cosmos DB]() |
|**Contribution au Kit de développement logiciel (SDK)** | [Référentiel Spring Data Azure Cosmos DB sur GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Bibliothèque de client Spring Boot Starter Azure Cosmos DB pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Exemple d’application Spring TODO avec Azure Cosmos DB**| [Expérience Java de bout en bout dans App Service Linux (partie 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guide de développeurs** | [Guide de développeurs Spring Data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Guide d’utilisation de starter** | [Comment utiliser Spring Boot Starter avec une API SQL Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Référentiel GitHub pour Azure Spring Boot Starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Exemple avec App Services** | [Guide pratique pour utiliser Spring et Cosmos DB avec App Service sur Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Exemple d’application TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Historique des mises en production

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mettre à jour la version de Spring Boot vers 2.3.0 
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mise à jour de la version Azure Cosmos DB vers la version 3.7.3
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Contient des correctifs de fuites de mémoire et des mises à niveau de version netty à partir du kit de développement logiciel (SDK) Cosmos v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correction de l’indicateur allowTelemetry à prendre en compte à partir de CosmosDbConfig
* Correction de la propriété TTL sur le conteneur

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajout d’une nouvelle findAll par API clé de partition
* Mise à jour de la version Azure-Cosmos vers 3.7.0
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correction collectionName -> containerName
* Correction entityClass & domainClass -> domainType
* Correction « Retour de la collection d’entités enregistrée par référentiel au lieu d’entités d’entrée »

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correction rétro-portée « Retour de la collection d’entités enregistrée par référentiel au lieu d’entités d’entrée »

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mise à jour de la version Azure-Cosmos vers la version 3.6.0
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mise à jour du Kit de développement logiciel (SDK) Cosmos DB vers 3.5.0
* Ajout d’un champ d’annotation pour activer/désactiver la création automatique de la collection
* Meilleure gestion des exceptions, CosmosClientException exposé via CosmosDBAccessException
* RequestCharge et activityId exposés via ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correctifs de la mise à jour du kit de développement logiciel (SDK) 3.5.0 : « exception lorsque l’en-tête de la réponse HTTP Cosmos DB dépasse 8 192 octets », « Échec de ConsistencyPolicy.defaultConsistencyLevel() sur l’obsolescence limitée et le préfixe cohérent »
* Correction du comportement des API findById, retour vide introuvable, au lieu de lever l’exception
* Correction d’un bogue dans lequel le tri n’a pas été appliqué à la page suivante lors de l’utilisation de CosmosPageRequest

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajout d’un champ d’annotation pour activer/désactiver la création automatique de la collection
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correction du comportement des API findById, retour vide introuvable, au lieu de lever l’exception

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Support du référentiel Cosmos réactif complet
* Chaîne de diagnostics de requêtes et support de mesures de requêtes Cosmos DB
* Mise à jour du Kit de développement logiciel (SDK) Cosmos DB vers 3.3.1
* Mise à niveau de la version de Spring Framework vers 5.2.0.RELEASE
* Mise à niveau de la version de Spring Data Commons vers 2.2.0.RELEASE
* Ajout des API findByIdAndPartitionKey, deleteByIdAndPartitionKey
* Dépendance supprimée de azure-documentdb
* DocumentDb rebaptisé pour Cosmos
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correction « Le tri lève une exception quand pagesize est inférieur au nombre total d’éléments dans le référentiel »

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Déprécier les API de base de données de documents
* Ajout des API findByIdAndPartitionKey, deleteByIdAndPartitionKey.
* Ajout du verrouillage optimiste basé sur _etag
* Expression SPeL activée pour le nom de la collection de documents
* Améliorations apportées à ObjectMapper.
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ajout de la dépendance du kit de développement logiciel (SDK) Cosmos v3
* Référentiel Cosmos réactif ajouté
* Implémentation mise à jour de DocumentDbTemplate pour utiliser le kit de développement logiciel (SDK) Cosmos v3.
* Autres modifications de configuration pour le support des référentiels Cosmos réactifs.
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Supprimer la dépendance applicationInsights pour
    * Risque potentiel des dépendances polluantes
    * Incompatibilité Java 11
    * Éviter un impact potentiel sur les performances du processeur et/ou de la mémoire.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Nouvelles fonctionnalités
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Le rétro-portage supprime la dépendance applicationInsights pour
    * Risque potentiel des dépendances polluantes
    * Incompatibilité Java 11
    * Éviter un impact potentiel sur les performances du processeur et/ou de la mémoire.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mettre à jour la version principale vers la version 2.1.1
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Ignorer toutes les exceptions des données de télémétrie
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Mettre à jour la version vers 2.1.0 pour résoudre le problème
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* L’ajout du mot clé existe, startsWith
* Mettre à jour le fichier Readme
#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Corriger « impossible d’appeler autohref directement pour l’entité »
* Corriger « le findAll échoue si la collection n’est pas créée »

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (Préversion) (2018-08-23)
#### <a name="new-features"></a>Nouvelles fonctionnalités
* Package restant de documentdb à cosmosdb,
* Ajoutez une nouvelle fonctionnalité du mot clé de méthode de requête, 16 mots clés de l’API SQL pris en charge.
* Ajoutez une nouvelle fonctionnalité de requête avec la pagination et le tri.
* Simplifiez la configuration de spring-data-cosmosdb.
* Ajoutez l’API deleteCollection et deleteAll.

#### <a name="key-bug-fixes"></a>Correctifs des bogues clés
* Correction des bogues et amélioration des défauts.

## <a name="faq"></a>Questions fréquentes (FAQ)
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Pour en savoir plus sur l’infrastructure Spring, consultez la [page d’accueil du projet](https://spring.io/projects/spring-framework).

Pour en savoir plus sur Spring Boot, consultez la [page d’accueil du projet](https://spring.io/projects/spring-boot).

Pour en savoir plus sur Spring Data, consultez la [page d’accueil du projet](https://spring.io/projects/spring-data).