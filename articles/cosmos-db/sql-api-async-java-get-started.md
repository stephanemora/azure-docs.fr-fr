---
title: 'Tutoriel : Créer une application Java avec le kit SDK Java Async Azure Cosmos DB pour gérer les données d’un compte d’API SQL'
description: Ce tutoriel vous explique comment stocker et accéder à des données au sein d’un compte d’API SQL dans Azure Cosmos DB à l’aide d’une application Async Java.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: f81052f68d377603c7148ac2a39da626b426e352
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985233"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Tutoriel : Créer une application Java avec le kit SDK Java Async pour gérer les données stockées dans un compte d’API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java asynchrone](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

En tant que développeur, vous pouvez avoir des applications qui utilisent des données de documents NoSQL. Vous pouvez utiliser le compte d’API SQL dans Azure Cosmos DB pour stocker et accéder à ces données de documents. Ce tutoriel vous explique comment créer une application Java avec le kit SDK Java Async pour stocker et gérer des données de documents. 

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Création et connexion à un compte Azure Cosmos
> * Configuration de votre solution
> * Création d’une collection
> * Création de documents JSON
> * Interrogation de la collection

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous disposez des ressources suivantes :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Kit de développement Java (JDK) 8+](/java/azure/jdk/?view=azure-java-stable).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Créez un compte Azure Cosmos en procédant comme suit :

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>Cloner le référentiel GitHub

Clonez le référentiel GitHub pour [bien démarrer avec Azure Cosmos DB et Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Par exemple, à partir d’un répertoire local, exécutez la commande suivante pour récupérer l’exemple de projet localement.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

Le répertoire contient un fichier `pom.xml` et un dossier `src/main/java/com/microsoft/azure/cosmosdb/sample` comprenant le code source Java, y compris `Main.java`. Le projet contient le code nécessaire pour effectuer des opérations avec Azure Cosmos DB, comme créer des documents et interroger des données dans une collection. Le fichier `pom.xml` inclut une dépendance au [kit SDK Java d’Azure Cosmos DB sur Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>Se connecter à un compte Azure Cosmos DB

Ensuite, revenez au [Portail Azure](https://portal.azure.com) pour récupérer votre point de terminaison et votre clé primaire. Le point de terminaison et la clé primaire Azure Cosmos DB sont nécessaires pour que votre application sache où se connecter et qu’Azure Cosmos DB approuve la connexion de votre application. Le fichier `AccountSettings.java` contient la clé primaire et les valeurs d’URI. 

Dans le portail Azure, accédez à votre compte Azure Cosmos, puis cliquez sur **Clés**. Copiez l’URI et la clé primaire à partir du portail, puis collez-les dans le fichier `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Obtenir des clés à patir de la capture d’écran du portail][keys]

## <a name="initialize-the-client-object"></a>Initialiser l’objet client

Initialisez l’objet client à l’aide de l’URI de l’hôte et des valeurs de clé primaire définies dans le fichier « AccountSettings.java ».

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>Créer une base de données

Créez votre base de données Azure Cosmos en utilisant la méthode `createDatabaseIfNotExists()` de la classe DocumentClient. Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a name="create-a-collection"></a><a id="CreateColl"></a>Créer une collection

Vous pouvez créer une collection à l’aide de la méthode `createDocumentCollectionIfNotExists()` de la classe DocumentClient. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.

> [!WARNING]
> **createCollection** crée une collection avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a name="create-json-documents"></a><a id="CreateDoc"></a>Créer des documents JSON

Créez un document en utilisant la méthode createDocument de la classe DocumentClient. Les documents sont du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Le fichier « src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java » définit les documents de famille JSON. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>Interroger les ressources Azure Cosmos DB

Azure Cosmos DB prend en charge les requêtes enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant montre comment interroger des documents dans Azure Cosmos DB à l’aide de la syntaxe SQL avec la méthode `queryDocuments`.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a name="run-your-java-console-application"></a><a id="Run"></a>Exécuter votre application console Java

Pour exécuter l’application à partir de la console, accédez au dossier du projet et compilez à l’aide de Maven :

```bash
mvn package
```

L’exécution de `mvn package` télécharge la dernière bibliothèque Azure Cosmos DB à partir de Maven et génère `GetStarted-0.0.1-SNAPSHOT.jar`. Ensuite, exécutez l’application comme suit :

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Vous avez maintenant terminé ce didacticiel NoSQL et vous disposez d’une application console Java opérationnelle.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, le compte Azure Cosmos et toutes les ressources associées. Pour cela, sélectionnez le groupe de ressources de la machine virtuelle, sélectionnez **Supprimer**, puis confirmez le nom du groupe de ressources à supprimer.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer une application Java avec le Kit SDK Java Async pour gérer les données de l’API SQL dans Azure Cosmos DB. Vous pouvez maintenant passer à l’article suivant :

> [!div class="nextstepaction"]
> [Générer une application console Node.js avec le Kit de développement logiciel (SDK) JavaScript et Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
