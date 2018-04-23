---
title: Créer une base de données de documents Azure Cosmos DB avec Java | Microsoft Docs
description: Cet article présente un exemple de code Java que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB, et pour l’interroger.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 5be05ebd201796707934eac665793dd2c1dc8f2a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB : créer une base de données de documents à l’aide de Java et du portail Azure

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Avec Azure Cosmos DB, vous pouvez rapidement créer et interroger des bases de données de documents, tables et graphiques gérées.

Ce guide de démarrage rapide crée une base de données de documents à l’aide des outils du portail Azure pour l’[API SQL](sql-api-introduction.md) Azure Cosmos DB. Ce guide vous indique également comment créer rapidement une application console Java au moyen de [l’API Java SQL](sql-api-sdk-java.md). Les instructions de ce guide de démarrage rapide s’appliquent à tous les systèmes d’exploitation pouvant exécuter Java. Après avoir suivi ce guide de démarrage rapide, vous saurez comment créer et modifier des ressources de base de données de documents dans l’interface utilisateur ou par programme, selon la méthode que vous préférez.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Par ailleurs : 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargement](http://maven.apache.org/download.cgi) et [installation](http://maven.apache.org/install.html) d’une archive binaire [Maven](http://maven.apache.org/)
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-a-database-account"></a>Création d'un compte de base de données

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

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé git-samples, puis fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application. 

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

Les extraits de code suivants sont tirés du fichier C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted\Program.java.

* Initialisation de `DocumentClient`. Le client [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) fournit la représentation logique côté client pour le service de base de données Azure Cosmos DB. Ce client est utilisé pour configurer et exécuter des requêtes auprès du service. Les parties `FILLME` de ce code seront mises à jour plus tard dans le démarrage rapide.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Création de [base de données](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database).

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Création de [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection).

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Création de documents à l’aide de la méthode [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument).

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Les requêtes SQL sur JSON sont effectuées à l’aide de la méthode [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée.

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Clés**. 

    Utilisez les boutons de copie sur le côté droit de l’écran pour copier la valeur supérieure, c’est-à-dire l’URI.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, page Clés](./media/create-sql-api-java/keys.png)

2. Ouvrez le fichier `Program.java` à partir du dossier C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. 

3. Collez la valeur URI à partir du portail sur `https://FILLME.documents.azure.com` au niveau de la ligne 45.

4. Accédez à nouveau au portail et copiez la valeur CLÉ PRIMAIRE, comme illustré dans la capture d’écran. Collez la valeur CLÉ PRIMAIRE à partir du portail sur `FILLME` au niveau de la ligne 46.

    La méthode getStartedDemo doit maintenant ressembler à ce qui suit : 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Enregistrez le fichier Program.java.

## <a name="run-the-app"></a>Exécution de l'application

1. Dans la fenêtre de terminal git, exécutez la commande `cd` pour accéder au dossier azure-cosmos-db-documentdb-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. Dans la fenêtre de terminal git, utilisez la commande suivante pour installer les packages Java requis.

    ```
    mvn package
    ```

3. Dans la fenêtre de terminal git, utilisez la commande suivante pour démarrer l’application Java.

    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    La fenêtre de terminal affiche une notification vous informant que la base de données FamilyDB a été créée. 
    
4. Appuyez sur une touche pour créer la base de données, puis sur une autre touche pour créer la collection. 

    À la fin du programme, toutes les ressources sont supprimées. Vous devez donc revenir à l’Explorateur de données dans votre navigateur pour voir qu’il contient maintenant une collection FamilyCollection et une base de données FamilyDB.

5. Basculez vers la fenêtre de console et appuyez sur une touche pour créer le premier document, puis sur une autre touche pour créer le deuxième document. Revenez ensuite à l’Explorateur de données pour les afficher. 

6. Appuyez sur une touche pour exécuter une requête et afficher la sortie dans la fenêtre de console. 

7. La prochaine touche sur laquelle vous appuierez supprimera les ressources. Si vous souhaitez conserver les ressources, vous pouvez appuyer sur CTRL + C dans la fenêtre de console pour mettre fin au programme. Sinon, appuyez sur n’importe quelle touche pour supprimer les ressources de votre compte pour éviter d’être facturé pour son utilisation. 

    ![Sortie de la console](./media/create-sql-api-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, une base de données de documents et une collection à l’aide de l’Explorateur de données, puis à exécuter une application pour effectuer la même opération par programme. Vous pouvez maintenant importer des données supplémentaires dans votre collection Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)


