---
title: 'Démarrage rapide : Générer une application web à l’aide du Kit de développement logiciel (SDK) API Azure pour MongoDB et Java - Azure Cosmos DB'
description: Cet article présente un exemple de code Java que vous pouvez utiliser pour vous connecter à l’API Azure Cosmos DB pour MongoDB et pour l’interroger.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: bfded407790d01d65feec0b6e069c23739064e97
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756920"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Démarrage rapide : Créer une application console avec Java et l’API MongoDB dans Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Dans ce guide de démarrage rapide, vous allez utiliser l’API Azure Cosmos DB pour Mongo DB et le SDK Java pour créer une application web de console. Azure Cosmos DB vous permet de créer et d’interroger rapidement des documents, des paires clé/valeur, et des bases de données de graphe, tous bénéficiant de la distribution à l’échelle mondiale et des fonctionnalités de mise à l’échelle horizontale au cœur de Cosmos DB.

Ce démarrage rapide montre comment créer un compte Cosmos avec l’[API d’Azure Cosmos DB pour MongoDB](mongodb-introduction.md). Vous allez ensuite créer et déployer une application console à l’aide du [pilote Java MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
* [Installer le kit JDK pour Azure et le kit JDK Azure Stack version 8](https://aka.ms/azure-jdks)
* Maven (exécutez `apt-get install maven` si vous ne possédez pas Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Ajouter une collection

Nommez votre nouvelle base de données, **db**, et votre nouvelle collection, **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une invite de commandes, créez un nouveau dossier nommé git-samples, puis fermez l’invite de commandes.

    ```bash
    md "C:\git-samples"
    ```

2. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder au nouveau dossier d’installation pour l’exemple d’application.

    ```bash
    cd "C:\git-samples"
    ```

3. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Ouvrez ensuite le code dans votre éditeur préféré. 

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

Tous les extraits de code suivants proviennent du fichier Program.java.

* Le DocumentClient est initialisé.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Une base de données et une collection ont été créées.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Certains documents sont intégrés à l’aide de `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Certaines requêtes sont effectuées à l’aide de `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. À partir du compte, sélectionnez **Démarrage rapide** et **Java**, puis copiez la chaîne de connexion dans votre Presse-papiers.

2. Ouvrez le fichier `Program.java`, remplacez l’argument au constructeur MongoClientURI par la chaîne de connexion. Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Exécution de l’application console

1. Exécutez `mvn package` sur un terminal pour installer les modules npm requis.

2. Exécutez `mvn exec:java -D exec.mainClass=GetStarted.Program` sur un terminal pour démarrer votre application Java.

Vous pouvez maintenant utiliser [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) pour interroger ces nouvelles données, pour les modifier, et pour travailler avec elles.

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Cosmos, à créer une collection et à exécuter une application console. Vous pouvez maintenant importer des données supplémentaires dans votre base de données Cosmos.

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md)
