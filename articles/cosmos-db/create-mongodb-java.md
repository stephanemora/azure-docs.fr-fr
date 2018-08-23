---
title: 'Azure Cosmos DB : Développer une application console avec Java et l’API MongoDB | Microsoft Docs'
description: Cet article présente un exemple de code Java que vous pouvez utiliser pour vous connecter à l’API MongoDB d’Azure Cosmos DB, et pour l’interroger.
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quick start connect, mvc
ms.devlang: java
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: sclyon
ms.openlocfilehash: 3309d5d57c4ef32ddf2284dbf4a395bf18b6a70d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "42023872"
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-java-and-the-azure-portal"></a>Azure Cosmos DB : Développer une application console API MongoDB avec Java et le Portail Azure

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer un compte, une base de données de documents et une collection [API MongoDB](mongodb-introduction.md) Azure Cosmos DB à l’aide du portail Azure. Vous allez ensuite créer et déployer une application console basée sur le [pilote Java MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
* JDK 1.7 + (exécutez `apt-get install default-jdk` si vous ne possédez pas JDK)
* Maven (exécutez `apt-get install maven` si vous ne possédez pas Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Ajouter une collection

Nommez votre nouvelle base de données, **db**, et votre nouvelle collection, **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API MongoDB à partir de GitHub, configurer la chaîne de connexion, et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

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

Maintenant, retournez sur le Portail Azure afin d’obtenir vos informations de chaîne de connexion, et copiez-les dans l’application.

1. À partir du compte, sélectionnez **Démarrage rapide**et Java, puis copiez la chaîne de connexion dans votre presse-papiers

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

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une collection à l’aide de l’Explorateur de données, et à exécuter une application console. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md)


