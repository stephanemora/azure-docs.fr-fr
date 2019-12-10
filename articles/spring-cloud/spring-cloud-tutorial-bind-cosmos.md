---
title: Tutoriel - Lier une base de données Azure Cosmos DB à votre application Azure Spring Cloud
description: Dans ce tutoriel, découvrez comment lier une base de données Azure Cosmos DB à votre application Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 7e796c6f8b2ae17ba267a19da1d909087163d99c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708829"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Didacticiel : Lier une base de données Azure Cosmos DB à votre application Azure Spring Cloud

Azure Spring Cloud vous permet de lier automatiquement certains services Azure à vos applications au lieu de configurer manuellement votre application Spring Boot. Cet article explique comment lier votre application à une base de données Azure Cosmos DB.

Configuration requise :
* Une instance Azure Spring Cloud déployée.  Suivez notre [guide de démarrage rapide](spring-cloud-quickstart-launch-app-cli.md) pour commencer
* Un compte Azure Cosmos DB avec un niveau d’autorisations minimal de contributeur

## <a name="bind-azure-cosmos-db"></a>Lier Azure Cosmos DB

Azure Cosmos DB a cinq types d’API différents qui prennent en charge la liaison :

1. Crée une base de données Azure Cosmos DB. [Consultez cet article](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) pour obtenir de l’aide sur la création de la base de données. Prenez note du nom de votre base de données. La nôtre se nomme `testdb`.

1. Ajoutez l’une des dépendances suivantes dans le fichier `pom.xml` de votre application Spring Cloud en fonction de votre type d’API.
    
    #### <a name="api-type-core-sql"></a>Type d’API : Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Type d’API : MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Type d’API : Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Type d’API : Gremlin (graphique)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Type d’API : table Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Mettez à jour le déploiement actuel avec `az spring-cloud app update` ou créez un déploiement pour ce changement avec `az spring-cloud app deployment create`.  Ces commandes mettent à jour ou créent l’application avec la nouvelle dépendance.

1. Accédez à la page du service Azure Spring Cloud dans le portail Azure. Il s’agit de la même application que celle que vous avez mise à jour ou déployée à l’étape précédente. Recherchez le **Tableau de bord de l’application** et sélectionnez l’application à lier à la base de données Cosmos DB. Sélectionnez `Service binding`, puis le bouton `Create service binding`. Remplissez le formulaire, en sélectionnant le **Type de liaison** `Azure Cosmos DB`, le type d’API, le nom de votre base de données et le compte Azure Cosmos DB.

    > [!NOTE]
    > Utilisez un espace de clés pour le nom de la base de données si vous utilisez Cassandra.

1. Redémarrez l’application en sélectionnant le bouton **Redémarrer** dans la page de l’application.

1. Pour vérifier que le service est lié correctement, sélectionnez le nom de la liaison et examinez ses détails. Le champ `property` doit se présenter ainsi :

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à lier votre application Azure Spring Cloud à une base de données Cosmos DB.  Pour découvrir comment lier votre application à un Cache Redis Azure, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Lier une application Azure Spring Cloud à un Cache Redis Azure](spring-cloud-tutorial-bind-redis.md).
