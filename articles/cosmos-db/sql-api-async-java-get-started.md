---
title: Créer une application Java à l’aide du kit de développement logiciel (SDK) Java Async Azure Cosmos DB | Microsoft Docs
description: Ce tutoriel vous explique comment utiliser des comptes d’API SQL Azure Cosmos DB pour stocker des données et y accéder à l’aide d’une application Async Java.
keywords: didacticiel nosql, base de données en ligne, application console java
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: aa2613f7cb73c2c338189aaaa48587c49a3093f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962184"
---
# <a name="build-a-java-application-by-using-azure-cosmos-db-async-java-sdk"></a>Créer une application Java à l’aide du kit de développement logiciel (SDK) Java Async Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Java asynchrone](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB est une base de données multimodèle distribuée à l’échelle mondiale. Ce tutoriel vous explique comment utiliser des comptes d’API SQL Azure Cosmos DB pour stocker des données et y accéder à l’aide d’une application Async Java. 

Le programme est le suivant :

* Création et connexion à un compte Azure Cosmos DB
* Configuration de votre solution
* Création d’une collection
* Création de documents JSON
* Interrogation de la collection

Commençons dès maintenant !

## <a name="prerequisites"></a>Prérequis
Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Kit de développement Java (JDK) 8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Étape 1 : créer un compte Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Cloner le projet GitHub](#GitClone). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour configurer l’émulateur et passez directement à l’étape [Cloner le projet GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Étape 2 : cloner le projet GitHub
Vous pouvez commencer par cloner le référentiel GitHub pour une bonne [prise en main d’Azure Cosmos DB et de Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Par exemple, à partir d’un répertoire local, exécutez la commande suivante pour récupérer l’exemple de projet localement.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started

```
Le répertoire contient un fichier `pom.xml` pour le projet et un dossier `src/main/java/com/microsoft/azure/cosmosdb/sample` contenant le code source Java, y compris `Main.java` qui montre comment effectuer des opérations simples avec Azure Cosmos DB, comme créer des documents et interroger des données dans une collection. Le fichier `pom.xml` inclut une dépendance au [SDK Java d’Azure Cosmos DB sur Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Étape 3 : se connecter à un compte Azure Cosmos DB
Ensuite, revenez au [portail Azure](https://portal.azure.com) pour récupérer votre point de terminaison et votre clé primaire. Le point de terminaison et la clé primaire Azure Cosmos DB sont nécessaires pour que votre application sache où se connecter et qu’Azure Cosmos DB approuve la connexion de votre application. Le fichier `AccountSettings.java` contient la clé primaire et les valeurs d’URI. 

Dans le portail Azure, accédez à votre compte Azure Cosmos DB, puis cliquez sur **Clés**. Copiez l’URI et la clé primaire à partir du portail, puis collez-les dans le fichier `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Capture d’écran du portail Azure utilisé par le didacticiel NoSQL pour créer une application console Java. Présente un compte Azure Cosmos DB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte Azure Cosmos DB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

## <a name="step-4-initialize-the-client-object"></a>Étape 4 : Initialiser l’objet client
Initialisez l’objet client à l’aide de l’URI de l’hôte et des valeurs de clé primaire définies dans le fichier « AccountSettings.java »

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Étape 5 : Créer une base de données

Votre [base de données](sql-api-resources.md#databases) Azure Cosmos DB peut être créée à l’aide de la méthode createDatabaseIfNotExists() de la classe DocumentClient. Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections.

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

## <a id="CreateColl"></a>Étape 6 : Création d’une collection

> [!WARNING]
> **createCollection** crée une collection avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
Vous pouvez créer une collection à l’aide de la méthode createDocumentCollectionIfNotExists() de la classe DocumentClient. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.

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

## <a id="CreateDoc"></a>Étape 7 : Créer des documents JSON

Pour créer un [document](sql-api-resources.md#documents), utilisez la méthode createDocument de la classe DocumentClient. Les documents sont du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Le fichier « src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java » définit les documents de famille JSON 

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

## <a id="Query"></a>Étape 8 : interroger les ressources Azure Cosmos DB

Azure Cosmos DB prend en charge les [requêtes](sql-api-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant montre comment interroger des documents dans Azure Cosmos DB à l’aide de la syntaxe SQL avec la méthode [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments).

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

## <a id="Run"></a>Étape 9 : Exécuter l’ensemble de votre application console Java
Pour exécuter l’application à partir de la console, accédez au dossier du projet et compilez à l’aide de Maven :

```bash
mvn package
```

L’exécution de `mvn package` télécharge la dernière bibliothèque Azure Cosmos DB à partir de Maven et génère `GetStarted-0.0.1-SNAPSHOT.jar`. Ensuite, exécutez l’application comme suit :

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```
Félicitations ! Vous avez terminé ce didacticiel NoSQL et vous disposez d’une application console Java opérationnelle !

## <a name="next-steps"></a>Étapes suivantes
* Vous recherchez un didacticiel sur les applications web Java ? Consultez [Créer une application web Java avec Azure Cosmos DB](sql-api-java-application.md).
* Découvrez comment [surveiller un compte Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
