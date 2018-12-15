---
title: Créer une base de données de documents Azure Cosmos DB avec Java
description: Cet article présente un exemple de code Java que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB, et pour l’interroger.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc, devcenter
ms.devlang: java
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: moderakh
ms.openlocfilehash: 5f66623c8e37f0663da7defc10d87d89bd0ad99d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105776"
---
# <a name="create-and-manage-resources-of-an-azure-cosmos-db-sql-api-account-using-a-java-application"></a>Créer et gérer des ressources d’un compte d’API SQL Azure Cosmos DB à l’aide d’une application Java

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (préversion)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ce démarrage rapide montre comment créer et gérer des ressources d’un compte d’[API SQL](sql-api-introduction.md) Azure Cosmos DB à l’aide d’une application Java. Tout d’abord, vous créez un compte d’API SQL Azure Cosmos DB à l’aide du portail Azure, créez une application Java en utilisant le [kit de développement logiciel (SDK) Java SQL](sql-api-sdk-async-java.md), ajoutez des ressources à votre compte Cosmos DB à l’aide de l’application Java. Les instructions de ce guide de démarrage rapide s’appliquent à tous les systèmes d’exploitation pouvant exécuter Java. Après avoir suivi ce guide de démarrage rapide, vous saurez comment créer et modifier des bases de données Cosmos DB et des collections soit dans l’interface utilisateur ou par programme, selon la méthode que vous préférez.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Par ailleurs : 

* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargement](https://maven.apache.org/download.cgi) et [installation](https://maven.apache.org/install.html) d’une archive binaire [Maven](https://maven.apache.org/)
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Pour être en mesure de créer une base de données de documents, vous devez avoir préalablement créé un compte d’API SQL avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Ajouter un exemple de données

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Interroger vos données

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, travaillons sur le code. Nous allons cloner une application API SQL à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez directement passer à la section [Exécution de l’application](#run-the-app). 

* Initialisation de `AsyncDocumentClient`. Le client [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client) fournit la représentation logique côté client pour le service de base de données Azure Cosmos DB. Ce client est utilisé pour configurer et exécuter des requêtes auprès du service.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Création de [base de données](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._database).

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Création de [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document_collection).

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Création de documents à l’aide de la méthode [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document).

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* Les requêtes SQL sur JSON sont effectuées à l’aide de la méthode [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments?view=azure-java-stable).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Exécution de l'application

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et lancer l’application avec vos informations sur le point de terminaison. Cette opération permet à votre application de communiquer avec votre base de données hébergée.


1. Dans la fenêtre de terminal git, ouvrez `cd` le dossier d’exemple de code.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. Dans la fenêtre de terminal git, utilisez la commande suivante pour installer les packages Java requis.

    ```bash
    mvn package
    ```

3. Dans la fenêtre de terminal git, utilisez la commande suivante pour démarrer l’application Java (remplacez YOUR_COSMOS_DB_HOSTNAME par la valeur d’URI entre guillemets du portail et remplacez YOUR_COSMOS_DB_MASTER_KEY par la clé primaire entre guillemets du portail)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    La fenêtre de terminal affiche une notification vous informant que la base de données FamilyDB a été créée. 
    
4. Appuyez sur une touche pour créer la base de données, puis sur une autre touche pour créer la collection. 

    Revenez à l’Explorateur de données dans votre navigateur pour voir qu’il contient maintenant une collection FamilyCollection et une base de données FamilyDB.

5. Basculez vers la fenêtre de console et appuyez sur une touche pour créer le premier document, puis sur une autre touche pour créer le deuxième document. Revenez ensuite à l’Explorateur de données pour les afficher. 

6. Appuyez sur une touche pour exécuter une requête et afficher la sortie dans la fenêtre de console. 

7. L’application ne supprime pas les ressources créées. Revenez sur le portail pour [nettoyez les ressources](#clean-up-resources).  depuis votre compte afin d’éviter les frais.

    ![Sortie de la console](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, une base de données de documents et une collection à l’aide de l’Explorateur de données, puis à exécuter une application pour effectuer la même opération par programme. Vous pouvez maintenant importer des données supplémentaires dans votre collection Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)
