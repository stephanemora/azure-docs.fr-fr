---
title: Générer une API web en utilisant l’API et le SDK .NET d’Azure Cosmos DB pour MongoDB
description: Cet article présente un exemple de code .NET que vous pouvez utiliser pour vous connecter à l’API d’Azure Cosmos DB pour MongoDB et pour l’interroger.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 8/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: b528804385b9b47f6c586b49f2a33a4c688753b2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250195"
---
# <a name="quickstart-build-a-net-web-api-using-azure-cosmos-dbs-api-for-mongodb"></a>Démarrage rapide : Générer une API web .NET à l’aide de l’API d’Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Ce guide de démarrage rapide montre comment effectuer les opérations suivantes :
1. Créer un [compte d’API Azure Cosmos DB pour MongoDB](mongodb-introduction.md) 
2. Générer une API web de catalogue de produits à l’aide du [pilote .NET MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/)
3. Importer des exemples de données

## <a name="prerequisites-to-run-the-sample-app"></a>Configuration requise pour exécuter l’exemple d’application

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* Compte Azure avec un abonnement actif. [Créez gratuitement un compte Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Vous pouvez aussi [essayer Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, sans aucuns frais ni engagements.

Si vous ne disposez pas de Visual Studio, téléchargez [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) avec la charge de travail **Développement ASP.NET et web** installée au moment de l’installation.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Création d’un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>Découvrir le modèle objet

Avant de poursuivre la génération de l’application, examinons la hiérarchie des ressources dans l’API pour MongoDB, ainsi que le modèle objet utilisé pour créer ces ressources et y accéder. L’API pour MongoDB crée les ressources dans l’ordre suivant :

* Compte d’API Azure Cosmos DB pour MongoDB
* Bases de données 
* Regroupements 
* Documents

Pour plus d’informations sur la hiérarchie des entités, consultez l’article [Modèle des ressources d’Azure Cosmos DB](../account-databases-containers-items.md).

## <a name="install-the-sample-app-template"></a>Installer l’exemple de modèle d’application

Cet exemple est un modèle de projet DotNet, qui peut être installé pour créer une copie locale. Exécutez les commandes suivantes dans une fenêtre de commande :

```bash
mkdir "C:\cosmos-samples"
cd "C:\cosmos-samples"
dotnet new -i Microsoft.Azure.Cosmos.Templates
dotnet new cosmosmongo-webapi
```

Les commandes précédentes :

1. Créent le répertoire *C:\cosmos-samples* pour l’exemple. Choisissent un dossier adapté à votre système d’exploitation.
1. Définissent le dossier *C:\cosmos-samples* comme votre répertoire actif.
1. Installent le modèle de projet, ce qui le rend disponible globalement à partir de l’interface CLI DotNet.
1. Créent un exemple d’application locale à l’aide du modèle de projet.

Si vous ne souhaitez pas utiliser l’interface CLI DotNet, vous pouvez également [télécharger les modèles de projet sous la forme d’un fichier ZIP](https://github.com/Azure/azure-cosmos-dotnet-templates). Cet exemple se trouve dans le dossier `Templates/APIForMongoDBQuickstart-WebAPI`.

## <a name="review-the-code"></a>Vérifier le code

Les étapes suivantes sont facultatives. Pour savoir comment les ressources de base de données sont créées dans le code, examinez les extraits de code suivants. Sinon, passez directement à la section [Mettre à jour les paramètres de l’application](#update-the-application-settings).

### <a name="setup-connection"></a>Configurer la connexion

L’extrait de code suivant provient du fichier *Services/MongoService.cs*.

* La classe suivante représente le client et est injectée par le .NET Framework dans les services qui l’utilisent :

    ```cs
        public class MongoService
        {
            private static MongoClient _client;

            public MongoService(IDatabaseSettings settings)
            {
                _client = new MongoClient(settings.MongoConnectionString);
            }

            public MongoClient GetClient()
            {
                return _client;
            }
        }
    ```

### <a name="setup-product-catalog-data-service"></a>Configurer le service de données du catalogue de produits

Les extraits de code suivants proviennent du fichier *Services/ProductService.cs*.

* Le code suivant récupère la base de données et la collection, ou les crée si elles n’existent pas déjà :

    ```csharp
    private readonly IMongoCollection<Product> _products;        

    public ProductService(MongoService mongo, IDatabaseSettings settings)
    {
        var db = mongo.GetClient().GetDatabase(settings.DatabaseName);
        _products = db.GetCollection<Product>(settings.ProductCollectionName);
    }
    ```

* Le code suivant récupère un document par référence (SKU), un identificateur de produit unique :

    ```csharp
    public Task<Product> GetBySkuAsync(string sku)
    {
        return _products.Find(p => p.Sku == sku).FirstOrDefaultAsync();
    }
    ```

* Le code suivant crée un produit et l’insère dans la collection :

    ```csharp
    public Task CreateAsync(Product product)
    {
        _products.InsertOneAsync(product);
    }
    ```

* Le code suivant recherche et met à jour un produit :

    ```csharp
    public Task<Product> UpdateAsync(Product update)
    {
        return _products.FindOneAndReplaceAsync(
            Builders<Product>.Filter.Eq(p => p.Sku, update.Sku), 
            update, 
            new FindOneAndReplaceOptions<Product> { ReturnDocument = ReturnDocument.After });
    }
    ```

    De même, vous pouvez supprimer des documents à l’aide de la méthode [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html).

## <a name="update-the-application-settings"></a>Mettre à jour les paramètres d’application

Dans le portail Azure, copiez les informations de chaîne de connexion :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Cosmos DB. Ensuite, sélectionnez **Chaîne de connexion**, dans le volet de navigation de gauche, puis sélectionnez **Clés en lecture/écriture**. Vous utiliserez le bouton Copier situé sur le côté droit de l’écran pour copier la chaîne de connexion principale dans le fichier *appsettings.json* à l’étape suivante.

2. Ouvrez le fichier *appsettings.json*.

3. Copiez la valeur de la **chaîne de connexion principale** à partir du portail (avec le bouton Copier) et faites-en la valeur de la propriété **DatabaseSettings.MongoConnectionString** dans le fichier **appsettings.json**.

4. Examinez la valeur de **nom de base de données** dans la propriété **DatabaseSettings.DatabaseName** du fichier **appsettings.json**.

5. Examinez la valeur de **nom de collection** dans la propriété **DatabaseSettings.ProductCollectionName** du fichier **appsettings.json**.

> [!WARNING]
> N’archivez jamais de mots de passe ou autres données sensibles dans le code source.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Cosmos DB.

## <a name="load-sample-data"></a>Charger un exemple de données

[Téléchargez](https://www.mongodb.com/try/download/database-tools) [mongoimport](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport), un outil CLI qui importe facilement de petites quantités de données JSON, CSV ou TSV. Nous allons utiliser mongoimport pour charger les exemples de données de produit fournis dans le dossier `Data` de ce projet.

Dans le portail Azure, copiez les informations de connexion, puis entrez-les dans la commande ci-dessous : 

```bash
mongoimport --host <HOST>:<PORT> -u <USERNAME> -p <PASSWORD> --db cosmicworks --collection products --ssl --jsonArray --writeConcern="{w:0}" --file Data/products.json
```

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte d’API Azure Cosmos DB pour MongoDB. Ensuite, sélectionnez **Chaîne de connexion**, dans le volet de navigation de gauche, puis sélectionnez **Clés en lecture/écriture**. 

1. Copiez la valeur **HOST** dans le portail (à l’aide du bouton Copier), puis entrez-la à la place de **\<HOST\>** .

1. Copiez la valeur **PORT** dans le portail (à l’aide du bouton Copier), puis entrez-la à la place de **\<PORT\>** .

1. Copiez la valeur **USERNAME** dans le portail (à l’aide du bouton Copier), puis entrez-la à la place de **\<USERNAME\>** .

1. Copiez la valeur **PASSWORD** dans le portail (à l’aide du bouton Copier), puis entrez-la à la place de **\<PASSWORD\>** .

1. Examinez la valeur de **nom de base de données** et mettez-la à jour si vous avez créé autre chose que `cosmicworks`.

1. Examinez la valeur de **nom de collection**  et mettez-la à jour si vous avez créé autre chose que `products`.

> [!Note]
> Si vous souhaitez ignorer cette étape, vous pouvez créer des documents avec le schéma correct à l’aide du point de terminaison POST fourni dans le cadre de ce projet d’API web.

## <a name="run-the-app"></a>Exécuter l’application

Dans Visual Studio, sélectionnez CTRL+F5 pour exécuter l’application. Le navigateur par défaut est lancé avec l’application.

Si vous préférez l’interface CLI, exécutez la commande suivante dans une fenêtre de commande pour démarrer l’exemple d’application. Cette commande installe également les dépendances du projet et génère le projet, mais ne lance pas automatiquement le navigateur.

```bash
dotnet run
```

Une fois l’application en cours d’exécution, accédez à `https://localhost:5001/swagger/index.html` afin de voir la [documentation Swagger](https://swagger.io/) pour l’API web et pour soumettre des exemples de requêtes.

Sélectionnez l’API que vous voulez tester, puis sélectionnez « Essayer ».

:::image type="content" source="./media/create-mongodb-dotnet/try-swagger.png" alt-text="Capture d’écran de la page des points de terminaison de l’API web Essayer de l’interface utilisateur Swagger.":::

Entrez tous les paramètres nécessaires, puis sélectionnez « Exécuter ».

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte d’API pour MongoDB, à créer une base de données et une collection avec du code, puis à exécuter une application API web. Vous pouvez maintenant importer des données supplémentaires dans votre base de données. 

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existant pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, lisez la section concernant l’[estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
