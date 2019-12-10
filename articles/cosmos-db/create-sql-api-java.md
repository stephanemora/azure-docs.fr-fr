---
title: Démarrage rapide – Utiliser Java pour créer une base de données de documents à l’aide d’Azure Cosmos DB
description: Ce guide de démarrage rapide présente un exemple de code Java que vous pouvez utiliser pour vous connecter à l’API SQL d’Azure Cosmos DB et pour l’interroger.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: a4a8990b3da534acb39ff87c9f7665fb3b08ef06
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708167"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Démarrage rapide : Créer une application Java pour gérer les données de l’API SQL d’Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.JS](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ce guide de démarrage rapide vous montre comment utiliser une application Java pour créer et gérer une base de données de documents à partir de votre compte Azure d’API SQL Azure Cosmos DB. Tout d’abord, vous créez un compte d’API SQL Azure Cosmos DB à l’aide du portail Azure, vous créez ensuite une application Java en utilisant le SDK Java SQL, puis vous ajoutez des ressources à votre compte Cosmos DB à l’aide de l’application Java. Les instructions de ce guide de démarrage rapide s’appliquent à tous les systèmes d’exploitation pouvant exécuter Java. Après avoir suivi ce guide de démarrage rapide, vous saurez comment créer et modifier des bases de données Cosmos DB et des conteneurs soit dans l’interface utilisateur, soit par programmation, selon la méthode que vous préférez.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Par ailleurs : 

* [Kit de développement Java (JDK), version 8](https://aka.ms/azure-jdks)
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargement](https://maven.apache.org/download.cgi) et [installation](https://maven.apache.org/install.html) d’une archive binaire [Maven](https://maven.apache.org/)
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-a-database-account"></a>Création d’un compte de base de données

Pour être en mesure de créer une base de données de documents, vous devez avoir préalablement créé un compte d’API SQL avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Ajouter un conteneur

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
    git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez directement passer à la section [Exécution de l’application](#run-the-app). 

* Initialisation de `CosmosClient`. Le `CosmosClient` fournit une représentation logique côté client du service de base de données Azure Cosmos. Ce client est utilisé pour configurer et exécuter des requêtes auprès du service.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Création de CosmosDatabase.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Création de CosmosContainer.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Création d’éléments avec la méthode `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Les lectures de points sont effectuées avec les méthodes `getItem` et `read`

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Les requêtes SQL sur JSON sont effectuées avec la méthode `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Exécution de l'application

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et lancer l’application avec vos informations sur le point de terminaison. Cette opération permet à votre application de communiquer avec votre base de données hébergée.


1. Dans la fenêtre de terminal git, ouvrez `cd` le dossier d’exemple de code.

    ```bash
    cd azure-cosmos-java-getting-started
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
    
4. L’application crée une base de données avec le nom `AzureSampleFamilyDB`
5. L’application crée un conteneur avec le nom `FamilyContainer`
6. L’application effectue des lectures de points en utilisant les ID d’objet et la valeur de la clé de partition (qui est lastName dans notre exemple). 
7. L’application interroge les éléments pour récupérer toutes les familles dont le nom de famille est dans (« Andersen », « Wakefield », « Johnson »)

7. L’application ne supprime pas les ressources créées. Revenez sur le portail pour [nettoyez les ressources](#clean-up-resources).  depuis votre compte afin d’éviter les frais.

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos, une base de données de documents et un conteneur à l’aide de l’Explorateur de données, puis à exécuter une application pour effectuer la même opération par programmation. Vous pouvez maintenant importer des données supplémentaires dans votre conteneur Azure Cosmos. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)
