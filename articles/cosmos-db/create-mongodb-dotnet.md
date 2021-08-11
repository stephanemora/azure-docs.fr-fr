---
title: Générer une application web en utilisant l’API d’Azure Cosmos DB pour MongoDB et le kit de développement logiciel (SDK) .NET
description: Cet article présente un exemple de code .NET que vous pouvez utiliser pour vous connecter à l’API d’Azure Cosmos DB pour MongoDB et pour l’interroger.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 77e7b4feedc6d6d9fcc7dce589db3e75ce0bc180
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354915"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Démarrage rapide : Générer une application web .NET à l’aide de l’API d’Azure Cosmos DB pour MongoDB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB est la base de données NoSQL rapide de Microsoft avec des API ouvertes pour toute échelle. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur de Cosmos DB. 

Ce démarrage rapide montre comment créer un compte Cosmos avec l’[API d’Azure Cosmos DB pour MongoDB](mongodb-introduction.md). Vous allez ensuite créer et déployer une application web de liste des tâches à l’aide du [pilote .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Configuration requise pour exécuter l’exemple d’application

* [Visual Studio](https://www.visualstudio.com/downloads/)
* Un compte Azure Cosmos DB.

Si vous ne disposez pas de Visual Studio, téléchargez [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web** installée au moment de l’installation.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount-mongodb.md)]

L’exemple décrit dans cet article est compatible avec la version 2.6.1 du pilote MongoDB.Driver.

## <a name="clone-the-sample-app"></a>Clonage de l’exemple d’application

Exécutez les commandes suivantes dans une fenêtre de commande compatible GitHub comme [Git bash](https://git-scm.com/downloads) :

```bash
mkdir "C:\git-samples"
cd "C:\git-samples"
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
```

Les commandes précédentes :

1. Créent le répertoire *C:\git-samples* pour l’exemple. Choisissent un dossier adapté à votre système d’exploitation.
1. Définissent votre répertoire actif sur le dossier *C:\git-samples*.
1. Clonent l’exemple dans le dossier *C:\git-samples*.

Si vous ne souhaitez pas utiliser git, vous pouvez également [télécharger le projet en tant que fichier .zip](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Vérifier le code

1. Dans Visual Studio, cliquez avec le bouton droit sur le nom du projet dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**.
1. Dans la zone **Parcourir** de NuGet, tapez *MongoDB.Driver*.
1. À partir des résultats, installez la bibliothèque **MongoDB.Driver**. Cette opération permet d’installer le package MondoDB.Driver ainsi que toutes les dépendances.

Les étapes suivantes sont facultatives. Pour savoir comment les ressources de base de données sont créées dans le code, examinez les extraits de code suivants. Sinon, passez à l’étape [Mise à jour de votre chaîne de connexion](#update-the-connection-string).

Les extraits suivants sont tirés du fichier *DAL/Dal.cs*.

* Le code suivant initialise le client :

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Le code suivant récupère la base de données et la collection :

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Le code suivant récupère tous les documents :

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Le code suivant crée une tâche et l’insère dans la collection :

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   De même, vous pouvez mettre à jour et supprimer des documents à l’aide des méthodes [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) et [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html).

## <a name="update-the-connection-string"></a>Mettre à jour la chaîne de connexion

À partir du portail Azure, copiez les informations de la chaîne de connexion :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Cosmos, puis dans le volet de navigation de gauche, cliquez sur **Chaîne de connexion**, puis sur **Clés en lecture-écriture**. Vous utiliserez le bouton Copier sur le côté droit de l’écran pour copier le nom d’utilisateur, le mot de passe et l’hôte dans le fichier Dal.cs à l’étape suivante.

2. Ouvrez le fichier *DAL/Dal.cs*.

3. Copiez la valeur **nom d’utilisateur** à partir du portail (à l’aide du bouton Copier) et définissez-la comme valeur de **nom d’utilisateur** dans le fichier **Dal.cs**.

4. Copiez la valeur **hôte** à partir du portail et définissez-la comme valeur **hôte** dans le fichier **Dal.cs**.

5. Copiez la valeur **mot de passe** à partir du portail et définissez-la comme valeur **mot de passe** dans votre fichier **Dal.cs**.

<!-- TODO Store PW correctly-->
> [!WARNING]
> N’archivez jamais de mots de passe ou autres données sensibles dans le code source.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Cosmos DB.

## <a name="run-the-web-app"></a>Exécuter l’application web

1. Cliquez sur Ctrl + F5 pour exécuter l’application. Le navigateur par défaut est lancé avec l’application. 
1. Cliquez sur **Créer** dans le navigateur et créez quelques tâches dans votre application de liste des tâches.

<!-- 
## Deploy the app to Azure 
1. In VS, right click .. publish
2. This is so easy, why is this critical step missed?
-->
## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Cosmos, à créer une collection et à exécuter une application console. Vous pouvez maintenant importer des données supplémentaires dans votre base de données Cosmos. 

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
